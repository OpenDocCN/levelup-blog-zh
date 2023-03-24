# 与 Pulumi 的第一步

> 原文：<https://levelup.gitconnected.com/first-steps-with-pulumi-e6025eecece4>

## 您正在寻找的最简单、最实用的教程。

![](img/5f822b704ce98f33ed5c46eb154285d6.png)

由 [Kelvin Ang](https://unsplash.com/@kelvin1987?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

作为一名软件工程师，我大量使用 [Docker](https://www.docker.com) 来构建和运行我的代码，在我的本地开发机器上和生产环境中。

如果只是一个容器，单独使用 Docker 是可以的，但是如果容器越复杂(n > 1 个容器)，我就使用 [Docker Compose](https://docs.docker.com/compose/) 。

然后是 Kubernetes，我在当地也大量使用它。

例如，当添加像 [Istio](https://istio.io) 这样的服务网格时，乐趣才真正开始。

越来越多的层，每一层都有自己的工具和配置格式。

在过去，设置很小，只能单独使用`docker`和`kubectl`命令，使用简单的 shell 脚本实现自动化。

当然，我本可以使用[头盔](https://helm.sh)、[地形](https://www.terraform.io)和其他工具让我的生活变得更轻松，但我从未有时间更仔细地研究这些工具。

此外，这些只是一些其他的抽象层和一些配置文件。我不喜欢配置文件。在整个“基础设施即代码”的事情中，我一直忽略了编码部分。

然后我听说了 Pulumi，它背后的想法似乎非常适合我:使用一种高级编程语言来建模我的基础设施。

天才！

已经有很多针对特定用例的教程了，但是我想介绍一个最简单的场景:编写一些后端，并在 Docker 容器中本地部署和运行它。

开始是以前的一个帖子，在那里我创建了一个小小的“Hello World！”-基于烧瓶和用于部署的已用 Docker 的应用:[“使用烧瓶的第一步”](https://twissmueller.medium.com/first-steps-with-flask-dcf7325ad2c6)

我不想使用 Docker，而只想使用 Pulumi 来完成同样的部署工作。

这是我的开发机器:

*   MacBook Pro，13 英寸，2016 年
*   11.0.1 大苏尔马科斯
*   码头工人 2.5.0.1

首先，我用[自制软件](https://brew.sh)安装了 Pulumi。

```
> brew install pulumi
```

由于最近的操作系统升级，得到了一个小自制警告:

```
Warning: You are using macOS 11.0.
We do not provide support for this released but not yet supported version.
You will encounter build failures with some formulae.
Please create pull requests instead of asking for help on Homebrew's GitHub,
Twitter or any other official channels. You are responsible for resolving
any issues you experience while you are running this
released but not yet supported version.
```

不管怎样，一切正常。

```
> pulumi version
v2.14.0
```

在那之后，我已经从[“使用 Flask 的第一步”](https://twissmueller.medium.com/first-steps-with-flask-dcf7325ad2c6)创建了与项目目录在同一层的项目目录。

```
> mkdir pulumi-first-steps && cd pulumi-first-steps
> pulumi login --local
> pulumi new python
```

我刚按了回车键，直到我完成了设置。

请记住，这只是一个将要被删除的测试项目。对于任何严肃的工作，仔细检查设置并提供有意义的值，尤其是涉及到安全相关的设置时。

旁注:在本教程中，我将所有的东西都保留在本地，完全忽略了 Pulumi 提供的一个主要的建筑概念。你可以在关于[状态和后端](https://www.pulumi.com/docs/intro/concepts/state/)的文档中读到更多。

接下来，需要修改几个文件。

依赖关系在`requirements.txt`中定义:

```
pulumi>=2.0.0,<3.0.0
pulumi-docker
```

然后安装了:

```
> source venv/bin/activate
> pip3 install -r requirements.txt
```

在`__main__.py`中，所有的基础设施都实现了自动化。这里，我已经定义了 Docker 映像及其容器的构建。

容器通过以下方式启动:

```
> export PULUMI_CONFIG_PASSPHRASE=""
> pulumi up --yes
```

最后，检查一切是否正确启动:

```
> docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                           NAMES
8be32a773841        myapp               "/entrypoint.sh /sta…"   25 seconds ago      Up 22 seconds       443/tcp, 0.0.0.0:8080->80/tcp   myapp_container-cd96716
```

和一点烟雾测试来总结一下:

```
> curl localhost:8080
Hello World!
```

最后，我用以下工具清理了所有东西:

`> pulumi destroy --yes`

`> pulumi stack rm dev`

仅此而已。

从这里开始，我可以去任何地方，并通过使用我作为软件工程师所习惯的相同概念来以编程方式扩展我的基础设施:序列、选择和迭代。

如果你喜欢这篇文章，请给我买杯咖啡。