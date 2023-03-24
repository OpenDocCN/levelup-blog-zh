# 我的 Laravel 应用程序的 Google Run 云构建设置——migration+PostgreSQL 和 Secret ENV

> 原文：<https://levelup.gitconnected.com/my-google-run-cloud-build-setup-for-my-laravel-app-migration-postgresql-and-secret-env-79ce8b00a9b5>

![](img/401822219d161f273ebd34488439ef99.png)

照片由[克里斯蒂娜@ wocintechchat.com](https://unsplash.com/@wocintechchat?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这个故事是为那些已经在搜索如何使用 Google Run Cloud Build 使他们的 Laravel 应用程序工作的例子和提示的人准备的，包括如何设置 PostgreSQL(因为大多数例子都是在 MySQL:()中)，同时还想在构建过程中处理迁移。如果是你，我想我可以给你提供我个人的 cloudbuild.yaml，可能还会帮你做好自己的设置。

我假设您已经在 Google Cloud-Cloud Run 中部署了 Laravel 应用程序的最简单设置，这包括您已经有了一个生产 docker 文件，已经初步创建了 Google Cloud Run 服务，或者至少已经遵循了这个[视频教程](https://www.youtube.com/watch?v=KCWGJV3x1Rs)，因为我只想处理和分享部署的云构建部分。

这是:

```
steps:
  - name: gcr.io/cloud-builders/docker
    args:
      - build
      - '--no-cache'
      - '-t'
      - '$_GCR_HOSTNAME/$PROJECT_ID/$REPO_NAME/$_SERVICE_NAME:$COMMIT_SHA'
      - .
      - '-f'
      - Dockerfile
    id: Build

  - name: gcr.io/cloud-builders/docker
    args:
      - push
      - '$_GCR_HOSTNAME/$PROJECT_ID/$REPO_NAME/$_SERVICE_NAME:$COMMIT_SHA'
    id: Push

  - name: 'gcr.io/google.com/cloudsdktool/cloud-sdk:slim'
    args:
      - run
      - services
      - update
      - $_SERVICE_NAME
      - '--platform=managed'
      - '--image=$_GCR_HOSTNAME/$PROJECT_ID/$REPO_NAME/$_SERVICE_NAME:$COMMIT_SHA'
      - >-
        --labels=managed-by=gcp-cloud-build-deploy-cloud-run,commit-sha=$COMMIT_SHA,gcb-build-id=$BUILD_ID,gcb-trigger-id=$_TRIGGER_ID,$_LABELS
      - '--region=$_DEPLOY_REGION'
      - '--quiet'
    id: Deploy
    entrypoint: gcloud

  - name: 'gcr.io/google-appengine/exec-wrapper'
    entrypoint: 'bash'
    args:
      - -c
      - |
        /buildstep/execute.sh \
        -i $_GCR_HOSTNAME/$PROJECT_ID/$REPO_NAME/$_SERVICE_NAME:$COMMIT_SHA \
        -e DB_CONNECTION=$_DB_CONNECTION \
        -e DB_SOCKET=$$DB_HOST \
        -e DB_HOST=$$DB_HOST \
        -e CLOUD_SQL_CONNECTION_NAME=$_INSTANCE_CONNECTION_NAME \
        -e DB_DATABASE=$$DB_DATABASE \
        -e DB_USERNAME=$$DB_USERNAME \
        -e DB_PASSWORD=$$DB_PASSWORD 
        -s $_INSTANCE_CONNECTION_NAME \
        -- php /app/artisan migrate --force
    secretEnv: ['DB_PASSWORD', 'DB_DATABASE', 'DB_USERNAME', 'DB_HOST'] 
    waitFor:
      - Build
      - Push
      - Deploy
    id: Migrate

availableSecrets:
  secretManager:
    - versionName: projects/$_PROJECT_NUMBER/secrets/db_password/versions/2
      env: 'DB_PASSWORD'
    - versionName: projects/$_PROJECT_NUMBER/secrets/db_username/versions/1
      env: 'DB_USERNAME'
    - versionName: projects/$_PROJECT_NUMBER/secrets/db_database/versions/1
      env: 'DB_DATABASE'
    - versionName: projects/$_PROJECT_NUMBER/secrets/db_host/versions/1
      env: 'DB_HOST'

timeout: 1200s
images:
  - '$_GCR_HOSTNAME/$PROJECT_ID/$REPO_NAME/$_SERVICE_NAME:$COMMIT_SHA'
options:
  substitutionOption: ALLOW_LOOSE
tags:
  - gcp-cloud-build-deploy-cloud-run
  - gcp-cloud-build-deploy-cloud-run-managed
  - prod_deployment
```

所以在我的 cloudbuild.yaml 中，有 4 个步骤——构建->推送->部署->迁移。你可能注意到了，这些步骤非常简单。前三个是部署主应用程序的主要步骤，包括构建容器映像(Dockerfile)，然后将映像推送到容器注册表，然后将容器映像部署到云运行。但是，我们最感兴趣的是迁移步骤，我们将在下面进一步讨论。

# 迁移步骤

迁移步骤使用 App Engine exec 包装器，这将帮助我们在云构建作业期间向数据库运行命令。与其他步骤相同，该作业将在使用**云构建触发器**在源代码控制中更新项目代码时自动运行。

这一步的`entrypoint`需要使用`bash`,因为我们使用了 exec 包装器的`/buildstep/execute.sh`脚本。然后，我们添加了我们最近构建的图像的 URL`-i $_GCR_HOSTNAME/$PROJECT_ID/$REPO_NAME/$_SERVICE_NAME:$COMMIT_SHA`，并提供了所有带有`e`选项的图像的 PostgreSQL 数据库连接所需的连接细节。还注意到`CLOUD_SQL_CONNECTION_NAME`是必需的，你可以在使用云 SQL 服务时获得。添加完所有必需的选项后，我们运行 laravel 的迁移命令— `-- php /app/artisan migrate — force`。

你也可以注意到，我们已经定义了`secretEnv`。将这些信息作为秘密提供是可选的，但是非常推荐。Google 有一个我们也可以使用的 secret manager 服务，你必须将这些数据库信息添加到你自己的 Secret Manager 中，这样你才能在云构建中使用它们(参见这个[文档](https://cloud.google.com/run/docs/configuring/secrets)，了解如何使用)。对于我的云构建，我选择使用它们。在迁移步骤中访问这些环境变量之前，我们需要在 yaml 中将它们定义为`availableSecrets`选项。当您已经在 Secret manager 中设置了环境变量并在 Google Run 中设置了部署时，您可以检索这些版本名称。还要记住，在步骤部分访问 secretEnv 时，需要双美元符号。

`waitFor`选项用于确保迁移步骤只在前 3 个步骤完成后才运行。

我想这就是迁移步骤！

## 额外提示！

您在应用程序中需要的所有环境变量都可以在 Google Run 部署管理器中定义(参见[文档](https://cloud.google.com/run/docs/configuring/environment-variables))。这些变量可以在云构建中使用`$_`和变量名直接访问。但是，您可能想知道如何在迁移脚本中传递或访问环境变量。嗯，你可以简单地将它们添加为另一个`e`选项，比如说 `e APP_URL=$_APP_URL \`，然后在你的脚本中使用一个 Laravel 方法，比如`env(‘APP_URL’)`或者`config(‘APP_URL’)`，如果它是在你的配置中定义的话。

我想，我可能漏掉了一些你可能还不清楚的地方，请随意添加评论或问题。

*如果你喜欢这篇文章并想表达爱意，如果你能……*那就太棒了

[![](img/c439ed2101f75de0f04c6b1910db7338.png)](https://www.buymeacoffee.com/dxcgrl)