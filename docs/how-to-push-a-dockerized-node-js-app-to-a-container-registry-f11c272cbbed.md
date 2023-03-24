# 如何将 Dockerized Node.js 应用程序推送到容器注册表

> 原文：<https://levelup.gitconnected.com/how-to-push-a-dockerized-node-js-app-to-a-container-registry-f11c272cbbed>

## 容器注册表存储并允许您分发容器映像。

![](img/445328e1d3d41356e1a382199e958fef.png)

在 [Unsplash](https://unsplash.com/s/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由 [Max Duzij](https://unsplash.com/@max_duz?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍照

容器化应用是现代软件开发中的一个流行概念，这一概念为开发者提供了更多的机会。在引入开源软件 **Kubernetes** 之后，这个概念变得更加有趣，它开辟了将容器应用程序与第三方工具集成的各种方式。

在本文中，我将讨论如何将容器图像推送到容器注册中心。容器注册中心基本上是一个存储库，它帮助您存储容器映像，并允许其他主机从注册中心服务器下载您的映像。

出于演示的目的，我将使用一个简单的 Node.js 应用程序，并从头开始介绍每个步骤。所以你可以继续，如果你的电脑上已经有了一个集装箱图像，你可以从**步骤 3** 开始。

让我们首先开始创建一个容器化的 Node.js 应用程序。

# 步骤 1:安装 Docker

首先，你的电脑上需要安装 Docker。你可以从下面的链接下载 **Docker 桌面**，一旦下载并成功安装，在你的电脑上启动 Docker 桌面。

[](https://www.docker.com/products/docker-desktop) [## 用于 Mac 和 Windows 的 Docker 桌面

### Docker 订阅服务协议已更新。我们的 Docker 订阅服务协议包括一项更改…

www.docker.com](https://www.docker.com/products/docker-desktop) 

# 步骤 2:创建一个容器化的应用程序

下一步是使用 Docker 封装您的应用程序。要容器化你的应用程序，你需要有一个在 YAML 写的 docker file**。让我们使用 ***节点*** 作为基础图像创建一个 Dockerfile。**

我创建了一个简单的 Node.js 应用程序，我的工作目录包含以下文件。

```
hello-world
  - app.js
  - package-lock.json
  - package.json
```

然后我需要在这个目录中创建一个 Dockerfile。

```
FROM node:10.23-alpine

WORKDIR /usr/src/app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 8080

CMD ["node", "app.js"]
```

添加 docker 文件后，可以为应用程序创建 docker 映像。使用下面的命令*【确保您位于 docker 文件所在的目录中】*。

```
$ docker build . -t <image-name>Eg:$ docker build . -t node-app
```

# 步骤 3:将您的映像推送到容器注册中心

在这个演示中，我使用 **Docker Hub** 作为容器注册中心。但是您可以根据自己的喜好选择任何其他容器注册表。

首先，去 [Docker Hub](https://hub.docker.com/signup) 创建一个新账户。然后登录并进入**资源库** > **创建资源库**。将一个**名称**添加到您的存储库*【您也可以向您的存储库添加描述，但这是可选的】*并且您可以选择它是作为**公共**还是**私有**存储库*【如果您使用的是* ***自由计划*** *，您只能在您的帐户中创建一个私有存储库】*。

然后，您需要使用您的终端登录您的 Docker Hub 帐户。为此，请使用下面的命令，并在必要时提供您的凭据。

```
$ docker login <container-registry-name> -u <username> -p <password>
```

这是您可以用来登录您的帐户的通用命令。但是使用这个命令的缺点是，您必须以纯文本格式输入您的密码。因此，请使用以下命令之一，这样即使在终端上也看不到您的密码。

```
$ docker login <container-registry-name>// then hit enter & provide your username and password- OR -$ docker login <container-registry-name> -u <username>// then hit enter and provide your password
```

因为我使用 Docker Hub 作为容器注册中心，所以您可以在终端上使用`docker login`命令，并且在不指定容器注册中心名称的情况下提供凭证。

```
Eg: type *docker login* and hit enter$ docker loginUsername: <username>Password: <password>// provide credentials and hit enter
```

在您的终端上得到`Login Succeeded`作为输出后，您可以开始**对接标记**和**对接推动**。

`docker tag`命令帮助你给你的 docker 图片添加一个标签，而`docker push`命令将它推入 Docker Hub。让我们看看如何在终端上使用这些命令。

```
$ docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]SOURCE_IMAGE[:TAG] - you can keep this as your local imageTARGET_IMAGE[:TAG] - <username>/<image-name>:<tag>Eg:$ docker tag node-app mikej/node-app:v1.0
```

上述命令将标记您的图像，然后您可以使用 docker push 命令，如下所示，它会将您的图像或存储库推送到注册表。

```
$ docker push --helpUsage: docker push [OPTIONS] NAME[:TAG]Push an image or a repository to a registryOptions:--disable-content-trust   Skip image signing (default true)Eg:$ docker push mikej/node-app:v1.0
```

# 结论

***恭喜*** 🎉

您已经成功地将 dockerized Node.js 应用程序推送到容器注册表中。

# 为初学者下载 Docker PDF🎉

[](https://www.buymeacoffee.com/krbtennakoon/e/68939) [## 初学者码头工人

### 这涵盖了 Docker 的基础知识，包括您需要知道的命令。以 PDF 格式提供。

www.buymeacoffee.com](https://www.buymeacoffee.com/krbtennakoon/e/68939)