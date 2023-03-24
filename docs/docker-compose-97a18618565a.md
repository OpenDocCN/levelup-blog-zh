# Docker 撰写入门

> 原文：<https://levelup.gitconnected.com/docker-compose-97a18618565a>

自动化和划分本地开发环境

![](img/1f96e7dbf046a1558b6d879f892bc346.png)

划分——由[托马斯·霍克](https://www.flickr.com/photos/thomashawk/34563079732)

当然，Docker 在 CI/CD 领域做得非常出色，许多开发人员无法想象回到过去糟糕、脆弱的部署时代。但是在本文中，我想先讨论一下 Docker Compose，以及它如何从根本上改善了我的本地开发环境。

*注意:*本文并不包括构建一个示例应用程序或服务。这意味着您可以带来自己的 Nodejs 服务，并利用本文中 Docker Compose 的优势。

# 什么是 Docker Compose？

引用 Docker 的官方文件:

> Compose 是一个定义和运行多容器 Docker 应用程序的工具。使用 Compose，您可以使用 YAML 文件来配置应用程序的服务。然后，只需一个命令，您就可以从您的配置中创建并启动所有服务。

或者换句话说，一个简单的文本文件，其中包含 Docker 应该运行哪些图像的指令，以及每个容器的命令或配置。

# 为什么使用 Docker Compose？

当我们的开发团队从。NET 到 nodejs，这对我们来说是一个相当大的变化。网退伍军人。一个困难是运行多个服务，所有服务都有不同的端口和配置，并且不断地改变我的本地配置以匹配我想要运行的任何服务。至少可以说是一团糟。

这就是 Docker Compose 拯救世界的地方。

# 入门指南

如果您的系统上已经安装并运行了 Docker，您可以跳过这一部分。

前往 Docker 下载 Docker 桌面。

一旦你完成了 Docker 桌面安装，打开一个终端并运行`docker --version`来查看你的系统上安装了哪个版本的 Docker。

```
$ docker --version
Docker version 19.03.8, build afacb8b
```

如果您看到类似上面的输出，那么恭喜您，您已经准备好了。

# 示例设置

在这个例子中，我们将使用节点版本 10，更多解释如下。

首先，让我们从使用 Node 的一个非常简单的设置开始。在节点服务所在的同一父目录中创建一个名为`docker-compose.yml`的文件(并行):

```
repo-dir/
  | -- docker-compose.yml
  | -- my-first-service/
```

使用您喜欢的文本编辑器，插入以下内容:

```
version: '3'
services: my-first-service:
    image: node:10
    volumes:
      - ./my-first-service:/app:ro
    working_dir: /app
    command: npm run start
    ports:
      - "3000:8080"
```

*注意:*关于 YAML，现在你需要知道的是，它类似于 JSON，但是依赖于空格缩进而不是花括号。

关于 Docker Compose 文件格式，可以写一整篇文章，但出于本文的目的，我将简要解释上面的内容。更多详情见: [Docker 合成文件格式](https://docs.docker.com/compose/compose-file/#compose-file-structure-and-examples)。

好了，我们把这个拆开，说说每一行是什么意思。第一行告诉 Docker 这个组合文件格式是哪个版本的。第二行描述了我们将执行哪个`services`,这由一个全新的缩进部分表示

。在本节中，我们给我们的服务起了个名字:`my-first-service`，这个名字又缩进了:

**映像**
这指示 Docker 使用`node` Docker 映像和该映像的版本`10`，不一定是 Node 的版本 10。然而，那些疯狂的酷节点家伙很聪明，他们将节点 Docker 映像的版本与节点本身的版本进行了映射。在这种情况下:`node:10`意味着我们使用的是节点版本 10 的最新*版本。*

这将服务所在的本地目录映射到容器中代码所在的目录。您还会注意到`ro`，它指示 Docker 这个目录应该被视为只读的。

一旦你的容器被初始化(快速说明:一个容器是从运行过的`node:10`映像创建的，稍后会详细介绍)，这将指示 Docker 什么目录应该是工作目录。

**命令**
一旦容器启动并运行，应该执行什么命令。这将通过 Docker docs 为该容器设置默认值:

> CMD 的主要目的是为正在执行的容器提供默认值。

**端口**
这映射了哪些端口应该暴露在容器之外。因此，在本例中，节点在内部运行，端口`8080`使用端口`3000`将其暴露给外部。当您有多个服务(稍后将详细介绍)且所有服务都有意配置为在特定端口上运行时，这尤其有用。这使得本地环境中的配置非常简单。

# 运行服务

我们现在准备好运行服务了。现在是时候使用`docker-compose`命令了。在与我们的`docker-compose.yml`文件相同的目录中，我们只需执行:

```
docker-compose up
```

简而言之，它所做的就是查看你的 YAML 文件，看看它需要什么样的映像，从 [Docker Hub](https://hub.docker.com/_/node) 获取映像，使用 YAML 文件中指定的所有参数通过`docker run`启动映像，最后生成一个容器。

现在，您应该可以通过 curl、浏览器(GET)或优秀的 [Postman](https://www.postman.com/) 应用程序在端口`3000`访问您的任何服务端点。

# 管理容器

有很多非常有用的工具可以用来管理你的容器，我将简单描述其中的几个。

您的容器正在运行，您将在某个时候需要停止它。为此，只需在命令行上执行组合键:`ctrl-c`。现在您的容器停止了，让我们再次启动它，但这次是在分离模式下:

```
docker-compose up -d
```

你会注意到它很快启动容器，显示一个指示`done`的状态，然后退出。

您可以通过以下方式检查容器的状态:

```
docker ps
```

其中有一些有用的信息，如每个集装箱的`CONTAINER ID`、`STATUS`和`NAMES`。

或者，您也可以运行:

```
docker-compose ps
```

它给你类似的信息，但是特定于*“组合的”*容器。

既然您的容器在后台愉快地运行，那么如果您希望准备好服务的输出，该怎么办呢？嗯，在分离模式下，这些`stdout`消息将被隐藏。不用担心，我们可以使用下一个命令:

```
docker logs <container name>
```

只需提供容器的名称，这个名称可以在`docker ps`中找到，它从这个服务中抛出`stdout`。

如果您不想在每次服务执行某项操作时都执行该命令，该怎么办呢？好的，你可以这样做:

```
docker logs --follow <container name>
```

这非常有用，因为您可以`ctrl-c`*【跟随】*日志，您的容器将继续运行。

好的……现在我们的容器正在后台愉快地运行，我们如何停止它。“上”的反义词是什么？你猜对了:

```
docker-compose down
```

瞧啊。

# 还有什么？

现在假设你有多个服务，ala 微服务！？让我们来看一个稍微修改过的 YAML 文件:

```
version: '3'
services: my-first-service:
    image: node:10
    volumes:
      - ./my-first-service:/app:ro
    working_dir: /app
    command: npm run start
    env_file: my-first-service/.docker-env
    ports:
      - "3000:8080" my-second-service:
    image: node:10
    volumes:
      - ./my-first-service:/app:ro
    working_dir: /app
    command: npm run start
    environment:
      - SOME_ENV=some-value 
    ports:
      - "3001:8080"
```

因此，我们在这里引入了两个新概念:

首先，当然我们可以有多种服务，这也是 Docker Compose 的亮点所在。注意，这两个服务公开了不同的端口，但是在内部使用相同的`8080`端口。因此，划分或更好:集装箱！

其次，这两个服务现在都在利用环境变量。在这里，您可以看到实现它们的两种不同方式。一个允许您指定一个文件的位置来解析多个环境变量，而另一个允许您从一个`ENV`中指定一个。

# 结论

我希望这篇文章对你有所帮助，并提供一些我在整天敲键盘时也能得到的好处。

哦，对了，最后一个金块:这些服务可以继续在后台运行，而你不必每次都重启容器(也就是说，如果你的应用或服务有类似于`nodemon`的东西，可以在代码改变时自动重启)。这允许您在模拟生产的同时，在本地环境中快速开发和故障排除。

尽情享受吧！