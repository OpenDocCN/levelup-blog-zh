# 了解 Dockerfiles:如何为您的应用程序构建和运行容器

> 原文：<https://levelup.gitconnected.com/understanding-dockerfiles-how-to-build-and-run-containers-for-your-applications-5395cc484074>

## 了解如何使用 Dockerfiles 来自动化构建和运行应用程序容器的过程

![](img/2ac4e936c50d7e5de5074fcec09d2a67.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上[的照片](https://unsplash.com/@ffstop?utm_source=medium&utm_medium=referral)

# 介绍

Docker 文件是包含构建 Docker 映像的说明的文本文件。这些映像是包含应用程序代码、库、依赖项和运行时环境的可执行包。通过使用 Dockerfiles，您可以自动化构建和部署应用程序的过程，并且可以轻松地将您的映像共享和分发给其他用户和环境。

在本文中，我们将解释 Docker 文件是什么，它们是如何工作的，以及如何使用它们来构建和管理 Docker 映像。我们还将回顾一些常见 Dockerfile 命令和最佳实践的示例，以便您可以开始在自己的项目中使用 docker file。

在本文结束时，您将会对 Docker 文件有一个很好的理解，以及它们如何帮助您构建、部署和管理您的 Docker 映像。

## 了解 docker 文件以及如何为您的应用程序编写它们

**Dockerfiles** 用于构建和管理 **Docker 镜像**，它们是轻量级的、独立的、可移植的软件包，包括运行应用程序所需的所有依赖项和库。Docker 文件是包含用于构建 Docker 映像的指令列表的文本文件**。这些指令是在构建 Docker 映像时按顺序** 执行的**，它们指定要使用的 ***基础映像*** ，要包含在映像中的 ***文件和依赖关系*，**和当**容器**启动时运行** 的 ***命令。***

> Docker 文件→ Docker 图像→ Docker 容器

下面是一个为 Python 应用程序构建图像的简单 docker 文件示例:

```
FROM python:3.8-slim

COPY . /app

RUN pip install -r requirements.txt

CMD ["python", "/app/main.py"]
```

> 这个 Dockerfile 使用`*FROM*`指令来指定要使用的基本映像，在本例中是`*python:3.8-slim*`基本映像。这个基础映像包括 Python 3.8 运行时和一组最小的库，因此它是构建 Python 应用程序的良好起点。
> 
> `*COPY*`指令用于将当前目录中的文件复制到容器中，`*RUN*`指令用于安装任何所需的依赖项。在这种情况下，`*pip install*`命令用于安装`*requirements.txt*`文件中列出的依赖项。
> 
> 最后，`*CMD*`指令用于指定容器启动时应该运行的命令。在这种情况下，`*python*`命令用于执行`*/app*`目录中的`*main.py*`脚本。

## 如何建立码头工人形象

一旦创建了这个 Docker 文件，您就可以使用`docker build`命令来构建 Docker 映像并创建一个容器。例如，以下命令将从当前目录中的 docker 文件构建一个名为`my-app`的映像:

```
$ docker build -t my-app .
```

## 如何运行 Docker 容器

然后，您可以使用`docker run`命令运行容器并访问应用程序。例如，以下命令将运行`my-app`容器并将其绑定到主机上的端口 8080:

```
$ docker run -p 8080:80 my-app
```

> 在这种情况下，带有`*docker run*`命令的`*-p*`标志用于将容器的端口 80 绑定到主机上的端口 8080。这意味着您可以通过在 web 浏览器中访问`*http://localhost:8080*`来访问运行在容器中的应用程序。

## Docker 撰写

您还可以使用`docker-compose`命令一次构建并运行多个容器。例如，如果您的应用程序由一个 web 服务器和一个数据库组成，您可以创建一个`docker-compose.yml`文件来指定这两个容器的配置:

```
version: "3"
services:
  web:
    build: .
    ports:
      - "80:80"
  db:
    image: postgres
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
```

然后，您可以使用`docker-compose up`命令来构建和运行容器。将从当前目录中的 Dockerfile 文件构建`web`容器，并且将从`postgres`图像创建`db`容器。容器将被链接在一起，应用程序将可在主机的端口 80 上访问。

## 暴露集装箱港口

除了以上示例中使用的指令，Dockerfiles 还可以包括其他指令，如`EXPOSE`和`ENV`，以指定容器的 ***暴露端口*** 和 ***环境变量*** 。

`EXPOSE`指令用于指定容器应该公开的端口。这意味着容器将监听指定端口上的传入连接，但它将不能从主机或任何其他网络访问，除非您使用带有`docker run`命令的`-p`标志将容器的端口绑定到主机上的端口。

例如，如果您将容器中的 web 服务器部署到云环境中，您可以使用以下 docker 文件:

```
FROM python:3.8-slim

COPY . /app

RUN pip install -r requirements.txt

EXPOSE 80

CMD ["python", "/app/main.py"]
```

> 在这个 docker 文件中，`*EXPOSE*`指令指定容器应该公开端口 80，这是 HTTP 流量的默认端口。这意味着容器将监听端口 80 上的传入连接，但是它将无法从主机或任何其他网络访问，除非您使用带有`*docker run*`命令的`*-p*`标志将容器的端口绑定到主机上的端口。

## 设置环境变量

`ENV`指令用于设置容器的环境变量。这允许您为容器中运行的应用程序所使用的变量指定值，例如数据库连接字符串或 API 键。

例如，如果您的 Python 应用程序使用存储在名为`DATABASE_URL`的环境变量中的数据库连接字符串，您可以使用 docker 文件中的`ENV`指令将该变量设置为适当的值:

```
FROM python:3.8-slim

COPY . /app

RUN pip install -r requirements.txt

ENV DATABASE_URL postgres://user:pass@host:port/database

CMD ["python", "/app/main.py"]
```

> 在这个 Dockerfile 中，`*ENV*`指令将`*DATABASE_URL*`变量设置为指定的连接字符串，该字符串将被容器中运行的应用程序使用。

# 结论

总之，Docker 文件用于构建和管理 Docker 映像，Docker 映像是轻量级的、独立的和可移植的软件包，包括运行应用程序所需的所有依赖项和库。Docker 文件是一个文本文件，它包含一组定义如何构建 Docker 映像的指令，这些指令在构建 Docker 映像时按顺序执行。docker 文件中使用的指令可以包括`FROM`、`COPY`、`RUN`、`EXPOSE`和`ENV`等等，这些指令允许您指定要使用的基本映像、要包含在映像中的文件和依赖项、容器的公开端口和环境变量，以及容器启动时要运行的命令。

如果你喜欢阅读这样的故事，并想支持我成为一名作家，考虑注册成为 Medium 会员，让你无限制地访问 Medium 上的故事。如果你用我的 [***链接***](https://lmatalka90.medium.com/membership) *注册，我会赚一小笔佣金。*

[](https://lmatalka90.medium.com/membership) [## 通过我的推荐链接加入媒体——卢艾·马塔尔卡

### 阅读卢艾·马塔尔卡的每一个故事(以及媒体上成千上万的其他作家)。您的会员费直接支持…

lmatalka90.medium.com](https://lmatalka90.medium.com/membership)