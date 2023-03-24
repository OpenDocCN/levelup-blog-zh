# 使用 ECS 和 Jenkins 在 AWS 上为容器实施 CI/CD

> 原文：<https://levelup.gitconnected.com/implementing-ci-cd-for-containers-on-aws-using-ecs-and-jenkins-ac1952741fcd>

![](img/884b20567673929577a8957b077f86dc.png)

由阿尔瓦罗·雷耶斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

有一篇文章介绍了如何[使用 ECS 和 CodePipeline](https://medium.com/cloudadventure/implement-ci-cd-for-containers-on-aws-using-ecs-and-codepipeline-1f93e58d8b08) 为 AWS 上的容器实现 CI/CD。但我的经历略有不同。我们在自己的私有数据中心有一个 Jenkins 设置，它已经被公司使用了很长时间，不仅用于 AWS，还用于其他用途，如 Android build。因此，几乎没有人希望使用 AWS 代码管道。请允许我向您展示如何使用 Jenkins 而不是 CodePipeline 来交付您的容器。

# 我们开始吧

**先决条件:**

A.您的源代码包括根文件夹中的 *taskdef.json、Dockerfile、Jenkinsfile、appspec.yml* 。

B.您正在 ap-southeast-1 可用性区域中运行 ECS 集群、ECR、服务、任务定义、S3 时段和代码部署。

C.确保安装了相关的 Jenkins pipeline 插件、 [docker-workflow](https://plugins.jenkins.io/docker-workflow/) 和 [AWS pipeline](https://plugins.jenkins.io/pipeline-aws/)

D.创建一个`awsId` Jenkins 凭证(用户名和密码)。在 AWS IAM 中创建一个名为 Jenkins 的新用户，并创建新的安全凭证。使用 AWS_ACCESS_KEY_ID 作为用户名，使用 AWS_SECRET_KEY_ID 作为密码

E.确保运行管道的 Jenkins 机器(无论是主机器还是节点机器)需要安装 Docker，并且可以被 Jenkins 访问

现在打开你的 Jenkinsfile 并开始实现你的阶段:

**阶段 1** — git 克隆你的项目，这样你就有了一个基础

```
stage('Git Clone') {
      steps {
        script {
            git branch: master,
              credentialsId: <your credentials id>,
              url: <your github url>
        }
      }
    }
```

第二阶段 —开始你的 docker 构建

```
stage('Docker Build') {
      steps {
        script {
            docker.build('<your-image-name>')
        }
      }
    }
```

**第三阶段**——将你的形象推向 ECR。使用`withAWS`命令登录

```
stage('docker push SG') {
      steps {
        script {
          withAWS(region: 'ap-southeast-1', credentials: 'awsId') {
            sh "${ecrLogin()}"
            sh "docker tag <your-image-name> <your-ecr-uri>"
            sh "docker push <your-ecr-uri>"
          }
        }
      }
    }
```

**阶段 4(可选)** —更新您的任务定义。我只推荐这一步，如果你没有在第三步中标记你的图片，或者因为某些原因你经常改变你的`taskdef.json`文件。

```
script {
    withAWS(region: 'ap-southeast-1', credentials: 'awsId') {
        taskDefRegistry = readJSON text: sh(returnStdout: true, script:"aws ecs register-task-definition \
            --memory 4096 \
            --cpu 2048 \
            --task-role-arn <task-role-arn> \
            --family <task-def-name> \
            --network-mode awsvpc \
            --requires-compatibilities EC2 FARGATE \
            --cli-input-json file://taskdef.json"), returnPojo: true
    }
}
```

**阶段 5a** —如果您没有为 ECS 服务使用 CodeDeploy，那么普通的 ECS 更新就可以了。

```
try {
  withAWS(region: 'ap-southeast-1', credentials: 'awsId') {
    def updateService = "aws ecs update-service --service <your-ecs-service-name> --cluster <your-cluster-name> --force-new-deployment"
    def runUpdateService = sh(returnStdout: true, script: updateService)
    def serviceStable = "aws ecs wait services-stable --service $internationalService --cluster $internationalCluster"
    sh(returnStdout: true, script: serviceStable)
    // put all your slack messaging here
  }
} catch(Exception e) {
  echo e.message.toString()
}
```

**阶段 5b** —如果您使用 CodeDeploy 进行蓝绿色部署，那么您必须以不同的方式执行最终的 Jenkinsfile 阶段

```
try {
  withAWS(region: 'ap-southeast-1', credentials: 'awsId') {
    sh(returnStdout: true, script: "aws s3 cp appspec.yml s3://<your-s3-bucket-name>/appspec.yml")
    def deploy = sh(returnStdout: true, script: "aws deploy create-deployment \
    --application-name <application-name> \
    --deployment-group-name <deployment-group-name> \
    --s3-location bucket=<your-s3-bucket-name>,bundleType=yaml,key=appspec.yml")
    def deployJson = readJSON text: deploy, returnPojo: true
    def waitDeploy = "aws deploy wait deployment-successful --deployment-id ${deployJson['deploymentId']}"
    sh(returnStdout: true, script: waitDeploy)
    // your slack message here to alert you deployment is done
    sh(returnStdout: true, script: "aws s3 rm s3://<your-s3-bucket-name>/appspec.yml")
  }
} catch(Exception e) {
  echo e.toString()
  env.ERROR_MESSAGE = e.toString()
  currentBuild.result = 'FAILURE'
}
```

最后，将 Jenkinsfile 推送到 SCM 源，并在 Jenkins 中运行管道。

恭喜你！！！你有一个 AWS 的工作詹金斯管道，享受自动化。

**注意:**`wait`命令起着至关重要的作用，让你的 Jenkins 等到部署完成后再结束流水线。这不是强制性的，但是如果你想通过 slack 消息得到通知，这是一个好办法。如果不使用 [AWS SNS](https://mosesliao.medium.com/aws-sns-lambda-with-ms-teams-27c36f669b3) 给你更好的细粒消息是怎么回事。