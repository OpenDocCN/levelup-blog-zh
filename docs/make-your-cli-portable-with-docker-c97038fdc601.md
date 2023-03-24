# 使用 Docker 让您的 CLI 变得可移植

> 原文：<https://levelup.gitconnected.com/make-your-cli-portable-with-docker-c97038fdc601>

![](img/2958d67bc09804f97001f959f8fc0b1b.png)

假设您已经开发了一个很棒的 CLI 应用程序，但是您对新开发人员的安装或入门过程不满意，并且您想要一个更简单的替代方案来让人们更快地使用您的 CLI。如果这是你，Docker 可能是你工具的一个很好的补充，拥有一个很好的可共享的人工制品，任何人都可以构建并使用它来运行你的 CLI。本文将介绍如何使用 Docker 文件轻松地将 CLI 安装到 Docker 映像中，以及如何运行包含优秀 CLI 应用程序的新 Docker 映像。

对于下面的 Dockerfile 示例，我将使用[m pulse/node-boilerplate-cli](https://github.com/mhulse/node-boilerplate-cli)repo 并对 CLI 进行 Dockerizing，如果您愿意的话。在本文中，我们将假设您的系统上已经安装了 Docker 如果没有，查看这里的[文档](https://docs.docker.com/install/)让你开始。

# 建立码头形象

首先，我们需要选择可以安装 CLI 的基本 Docker 映像；我们可以使用 [Dockerhub](https://hub.docker.com/search?q=) 为我们的 CLI 搜索一个兼容的运行时，它将是 NodeJs。我们将使用下面的 [NodeJs 映像](https://hub.docker.com/search?q=node&type=image&image_filter=official)作为我们的基本 Docker 映像，在其上构建我们的 CLI 安装。我们还将使用`latest`标签来确保我们总是获得最新的 Docker 图像。在我们的 repo 中，我们想要创建一个名为`Dockerfile`的文件。使用`FROM`命令，告诉 Docker 我们想要用来运行文件中定义的其余 Docker 命令的基本映像。

```
# Base image of the docker container
FROM node:latest
```

接下来，使用`COPY`命令，我们将把 repo 的内容复制到 docker 容器中，因为我们希望在容器运行时使用这些文件。`WORKDIR`命令有助于改变正在执行的命令的当前工作路径，以避免在 docker 文件中有大量难以阅读的路径。使用`RUN`命令，您将希望遵循您的 CLI 的安装步骤。

```
# Base image of the docker container
FROM node:latest# Copy the contents of the repo into the /app folder inside the container
COPY . /app# Update the current working directory to the /app folder
WORKDIR /app# Add your CLI's installation steps here
RUN npm install && npm link
```

最后，您将希望使用`ENTRYPOINT`命令提供 CLI 可执行文件的路径。这个命令意味着每次运行容器时，都会运行入口点可执行文件。

```
# Base image of the docker container
FROM node:latest# Copy the contents of the repo into the /app folder inside the container
COPY . /app# Update the current working directory to the /app folder
WORKDIR /app# Add your CLI's installation steps here
RUN npm install && npm linkENTRYPOINT ["/usr/local/bin/boilerplate"]
```

准备好`DOCKERFILE`之后，我们就可以构建包含已安装 CLI 的最终 Docker 映像了。我们将把这个图像标记为`your-cli-app`,这样我们以后可以识别它。

```
# Run the commands in our Dockerfile and create a container
# -t can be used to set name:tag of the built image
$ docker build -t your-cli-app .
Sending build context to Docker daemon   21.5MB
...
Successfully built 3fdbb4987594
Successfully tagged your-cli-app:latest
```

# 运行 Docker 映像

使用构建`your-cli-app`中的 Docker 图像标签，我们现在可以使用它来高效地运行我们的 CLI 应用程序。

```
# Run the latest `your-cli-app` container and pass it arguments like normal
$ docker run your-cli-app --version
1.0.0
$ docker run your-cli-app --help
Usage: boilerplate -d [/path/to/directory/]
...
```

仅此而已；现在您有了一个 Docker 映像，其中包含您的 CLI 映像，您可以使用它来运行您的 CLI。这种设置的好处在于，您现在可以将 other 文件提交到 repo 中，这样其他开发人员就可以下载并构建映像来一致地运行 CLI。这种模式允许所有开发人员使用相同的运行时来运行他们的应用程序，并减少可怕的“但它在我的机器上工作”的事件。如果它与 Docker 一起工作，它将在任何机器上工作。

# 包扎

如果你有兴趣发布 docker 容器供其他人使用，而不必自己构建容器，[点击](https://docs.docker.com/docker-hub/publish/publish/)查看发布图片的文档。如果您对深入 Docker 容器感兴趣，一个好的起点是 [docker 文件最佳实践文档](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)。

如果你已经在运行一些 docker 工作负载，你可能也想看看如何运行你自己的映像注册表，看看下面关于这个过程的帖子。

[](https://medium.com/codex/running-your-own-docker-registry-made-easy-549086b2e6db) [## 轻松运行您自己的 Docker 注册表

### 本文将介绍如何轻松地建立一个本地或外部可访问的 Docker 注册表来托管您自己的…

medium.com](https://medium.com/codex/running-your-own-docker-registry-made-easy-549086b2e6db) 

# 进一步连接

*   如果你正在考虑购买一份中等订阅，你可以通过我的[推荐链接](https://aaron-kt-berry.medium.com/membership)来帮我。
*   查看我在[媒体](https://medium.com/@aaron-kt-berry)上的其他文章，如果你想了解最新消息，可以通过[电子邮件](https://aaron-kt-berry.medium.com/subscribe)订阅。
*   如果你想聊天，请在推特上联系我，如果你想雇佣我，我在共同导师上。