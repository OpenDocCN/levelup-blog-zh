# Docker 为什么牛逼

> 原文：<https://levelup.gitconnected.com/why-docker-is-awesome-e1e6eae82777>

想要在您的机器上运行一些软件，但不想下载每个依赖项？Docker 改变了软件开发和部署的方式，允许小容器在几乎任何机器上运行。

![](img/c659efecb6b8d16e262263d341a7746c.png)

由[鲁拜图·阿扎德](https://unsplash.com/@rubaitulazad?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

*   **用很少的命令和很少的时间运行几乎任何软件**

轻松运行几个不同的数据存储(RabbitMQ、PostgreSQL、MySQL)。您可以测试多个数据库，而无需经历艰苦的安装过程，并快速启动和运行以专注于您的应用程序。

*   **不需要从头开始构建，这有助于提高可移植性**

Docker 使用[层](https://vsupalov.com/docker-image-layers/)，节省磁盘空间。可以把它想象成洋葱的层，每一层都建立在前面的层之上，但是实际上你可以在新的图像中重用前面的层。因此，如果 RabbitMQ 和 Kafka 是从同一个基础映像构建的，那么您只需存储一次，这样可以节省磁盘空间并减少依赖项的数量。

*   **使用 Dockerfile 文件在团队中共享配置**

将 Dockerfile 或`docker-compose.yml`文件签入您的代码库，团队只需一个命令就可以开始运行。这几乎消除了团队成员和新成员必须解决的“在我的机器上工作”的问题。

> docker 文件是一个文本文件，包含如何构建 docker 映像的说明。它指定了作为容器基础的操作系统，以及代码、环境变量、文件位置、网络端口和它需要的其他组件——以及容器在运行时将做什么。

有了 Docker，您还可以镜像生产环境，并相信您的代码将运行良好，而不管它在哪里运行。容器应该被视为短暂的，以避免问题并获得最大的好处。

# 术语

**容器映像** —包含创建容器所需的所有依赖项和信息的包(运行容器的一组指令)。

**容器**—来自与主机环境断开连接的映像的虚拟化运行时环境。与虚拟机或裸机相比是轻量级的。

**卷** —一个可写层，位于容器映像之上，因此程序可以访问可写文件系统。对于任何不是内存数据库的数据库，您都将拥有这个。

**Image Registry** —一个存储库，其中保存了 Docker 映像，供您或您的团队部署到您的生产环境中或用于本地开发。DockerHub 是一个众所周知的公共 Docker 图像库。

**(图像)标签** —应用于图像的标签，以便可以识别不同的图像或同一图像的版本(取决于版本号或目标环境)。

# 有用的命令

`docker compose up`

在一个`docker-compose.yml`文件中运行一个或多个 Docker 图像。注意，如果在您的机器上找不到 Docker 图像，这也会提取它们。

`docker run ubuntu:xenial`

运行 Docker 图像。注意，如果在您的机器上找不到 Docker 图像，这也会提取它们。

`docker run -it ubuntu:xenial`

在交互模式下运行图像，并将 shell 放入运行容器中。注意，如果在您的机器上找不到 Docker 图像，这也会提取它们。

`docker run -d ubuntu:xenial`

在分离的容器(如后台容器)中运行映像。注意，如果在您的机器上找不到 Docker 图像，这也会提取它们。

`docker images`

列出本地 Docker 图像

`docker container ps`

列出所有正在运行的容器

`docker compose down -v`

关闭`docker-compose.yml`中指定的 Docker 容器，并移除任何卷(用它来清除本地数据库)

`docker kill ubuntu:xenial`

停止一个或多个容器

# **结论**

如果您还没有使用 Docker，那么请尝试将本地开发系统中的一些设置转换为使用它，特别是对于数据库、消息存储或队列以及外部依赖项。如果你发现 Docker 有一个特别有用的命令，请在评论中分享！

如果你喜欢这篇文章，考虑[订阅 Medium](https://medium.com/@ascourter/membership) ！

如果你或你的公司有兴趣找人进行技术面试，那么请在 Twitter ( [@Exosyphon](http://twitter.com/Exosyphon) )上给我发 DM，或者访问我的[网站](https://andrewcourter.com/)。如果你喜欢这样的话题，那么你可能也会喜欢我的 [Youtube 频道](https://www.youtube.com/channel/UCx3Vist13GWLzRPvhUxQ3Jg)。如果你喜欢 3D 打印的东西，去我的 [Etsy 商店](https://www.etsy.com/listing/1273702925/6-sided-fidget-cube)看看。祝您愉快！