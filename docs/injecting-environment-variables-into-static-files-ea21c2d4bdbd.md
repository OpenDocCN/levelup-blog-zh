# 将环境变量注入 JSON 文件

> 原文：<https://levelup.gitconnected.com/injecting-environment-variables-into-static-files-ea21c2d4bdbd>

![](img/8911e4e342524be0514914fc6e7ec962.png)

[巨摄](https://unsplash.com/@juon?utm_source=medium&utm_medium=referral)上[下](https://unsplash.com?utm_source=medium&utm_medium=referral)

在我目前从事的项目中，我们有多个托管在 docker 容器中的环境，通常很难跟踪它们是否正确更新以及环境中运行的是哪个版本。

这些容器是使用我们的 Gitlab CI/CD 管道构建的，该管道提供了大量的[环境变量](https://docs.gitlab.com/ee/ci/variables/predefined_variables.html)，可用于将上下文信息注入到构建中。例如，通过组合像提交散列或标记、构建号和 repo 名称这样的东西，可以唯一地标识每个构建，然后可以在启动时打印到控制台中。

通常我们会简单地将环境变量放到一个`env`文件中，或者通过著名的`$FOO_BAR`语法在其他文件中引用它们。我们希望我们的语言、框架或工具在运行时获取语法，并通过`process.env`向我们展示。

但是唉，事情没那么简单。这可能就是你在这里的原因。

例如 Angular，作为客户端，不能访问任何环境变量。通常，环境变量是运行该进程的机器(也称为环境)的一种表示。然而，对于一个 web 应用程序来说，运行它的机器是用户的个人电脑，但我们通常对托管它的服务器更感兴趣。更糟糕的是，也许我们需要了解一些早已过去的构建环境。我们需要找到一种方法来注入可以保存和提供的静态文件，以便我们始终可以访问一些服务器信息的快照。

目标是轻松地生成一个文件，最好是 JSON，并在其中注入一些变量。我发现最简单的方法是创建一个文件(`environmentVariables.json`)，然后在构建期间将变量注入其中。

![](img/4205a911fb18827b6170626500dfed16.png)

Paul Esch-Laurent 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 方法一:NPM

通过 CLI 轻松编辑 JSON 文件的一种方法是使用 [json](https://www.npmjs.com/package/json) NPM 包。这是一个非常简单的库，基本上允许您通过 JSON Path esque 语法从命令行处理 JSON 文件。

例如，我有以下对象:

空环境信息文件

每次改变时都填充分支和提交散列会让我发疯。因此，您可以将该命令作为构建脚本的一部分来运行。

```
export BRANCH=$(git rev-parse --abbrev-ref HEAD)
export COMMIT_SHA=$(git rev-parse --short HEAD)json -I -f environmentVariables.json \
      -e “this.branch=’$BRANCH’” \
      -e "this.commit='$COMMIT_SHA'
```

或者跳过整个环境变量:

```
json -I -f environmentVariables.json \
      -e "this.branch='$(git rev-parse --abbrev-ref HEAD)'" \
      -e "this.commit='$(git rev-parse --short HEAD)'"
```

# **注**

> 为了使它成为有效的 JSON，您必须将选择的环境变量包含在另一组引号中。

# **命令行参数分解:**

> `-I -f`激活将更新写入输入文件的[就地编辑](http://trentm.com/json/#FEATURE-In-place-editing)
> 
> `-e` [对输入 JSON 对象执行](http://trentm.com/json/#FEATURE-Execution)提供的表达式

# 方法 2:外壳

> 让我们继续上面的示例文件。

如果您希望保持较小的 NPM 依赖性，或者不使用 Node.js，那么您也可以选择使用 CLI 工具。例如，我发现 [jq](https://stedolan.github.io/jq/) 是一个优秀的小命令行工具，可以用来交互和操作 JSON。

将它应用到这个问题看起来会像这样:

```
jq ‘.build=env.CI_PIPELINE_ID’ environment.json >> tmp.json && mv tmp.json environment.json
```

它只是将对象的`build`属性设置为`CI_PIPELINE_ID`变量。JQs 内置了对通过`env`对象访问 OS 环境变量的支持。

`jq`的一个缺点是它似乎还不提供就地编辑，所以你不得不创建一个临时文件。然而，这是一个很小的代价，可以很容易地自动化。

# 方法 3:更多外壳

您可以随时使用您选择的字符替换工具(例如 awk、sed)来查找和替换配置文件中的属性。这可能被认为是不引入依赖性的最纯粹的方法，但是它也会增加复杂性和可维护性开销。

我发现最好的方法是给你的文件添加一种“标签”,这种标签是独一无二的，足以引起搜索的关注。例如，在我的一个 Kubernetes 配置文件中，我在我要编辑的行(图片)上方添加了一个注释。

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myApp-deploy-v1
  labels:
    app: myApp
    version: v1
spec:
  replicas: 2
  selector:
    matchLabels:
      app: myApp
      version: v1
  template:
    metadata:
      name: myApp-pod
      labels:
        app: myApp
        version: v1
    spec:
      serviceAccountName: myApp-sa
      containers:
        - name: myApp
          livenessProbe:
            httpGet:
              port: 80
              path: /health
          readinessProbe:
            httpGet:
              port: 80
              path: /myApp/
          #->CI-IMAGE-NAME
          image: myApp:1
          ports:
            - containerPort: 80
```

然后，您可以像这样使用类似`sed`的 CLI 工具:

```
sed '/#->CI-IMAGE-NAME.*/!b;n;s/:.*\s*$/:1.0.2/' deploy.yaml
```

> 这用 1.0.2 替换了`myApp:1`图像标签，因此它使用了`myApp:1.0.2`

# 注意

> 自从第一次写这篇文章以来，我发现了 [Kustomize](https://kustomize.io/) 和 [Helm](https://helm.sh/) 的乐趣，这使得外壳替换的应用变得多余。

我确信有更多的选择可以实现这一点。此外，根据您正在使用的框架、语言和/或工具，可能已经有可用的变量替换解决方案(参见 K8 文件的 [Kustomize](https://kustomize.io/) 和 [Helm](https://helm.sh/) )。

在没有合适的工具的情况下，我希望这些方法中的一个能帮到你。

祝你好运！:)