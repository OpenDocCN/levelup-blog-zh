# 码头工人蜂拥而至

> 原文：<https://levelup.gitconnected.com/docker-swarm-in-a-nutshell-ed2a9c42cd7c>

## 别再害怕码头工人群了！

## 这个简单的教程展示了如何在大约 15 分钟内创建一个正在运行的 docker swarm 集群。

![](img/c8b917ad5dc399f4ad6da050ab7463e4.png)

[伊恩·泰勒](https://unsplash.com/@carrier_lost?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

## 我创建了一个后续指南来展示每个 Docker 群中应该使用的重要服务。该指南基于本教程。

[](/the-most-important-services-everyone-should-deploy-in-a-docker-swarm-8e120b5a66) [## 每个人都应该在 Docker 群中部署的 4 项重要服务

### 学习如何用四个你会喜欢的重要服务来增强你的 Docker 群。

levelup.gitconnected.com](/the-most-important-services-everyone-should-deploy-in-a-docker-swarm-8e120b5a66) 

# 概观

```
**1\. Introduction**
|--- 1.1 Why Docker Swarm 
|--- 1.2 Prerequisites
**2\. Create the Docker Swarm**
|--- 2.1 Installing dependencies
|--- 2.2 Initialize the Docker Swarm
|--- 2.3 Finalising the Docker Swarm
**3\. Optional enhancement**
| **4\. Closing Notes**
```

# 1.介绍

> 每当你阅读“Docker Swarm”时，我们实际上都在谈论“ **Docker Swarm 模式**”。(不是不推荐使用的产品 Docker swarm)

## 1.1 为什么 Docker Swarm？

[Docker Swarm](https://docs.docker.com/engine/swarm/) 是在**分布式集群**中**部署**您的应用程序堆栈到**生产**的合适工具，使用 Docker 本地合成使用的相同文件。

其他优势包括:

*   可复制性(开发文件可以在生产中使用)
*   健壮性(容错集群)
*   **开发和部署的简单性和速度**

Docker Swarm 的主要优势是简单性和开发速度，同时仍然能够在15 分钟内建立一个准备用于生产的分布式集群**。**

## 1.2 先决条件

要在 Docker Swarm 上学习本教程，您需要具备以下先决条件:

*   **基本**熟悉 **Linux 和 Docker**
*   需要从运行 Docker Swarm 的云提供商那里访问至少 2 个 ubuntu 服务器
*   一个指向你的一个服务器的域

# 2.创建码头工人群体

## 2.1 安装依赖项

在新安装的服务器基础设施中，必须安装每个依赖项来创建群集群。这些包括**更新操作系统**和**安装 docker-engine**

将操作系统更新到最新版本

```
$> sudo apt-get update && sudo apt-get upgrade
```

随后，`curl`可以用来下载 **get-docker.sh** ，这是[docker.com](https://docker.com)创建的一个脚本，可以轻松地在系统上安装 docker:

```
$> curl -fsSL https://get.docker.com -o get-docker.sh
```

此`curl`命令打开一个网站，读取内容并将其内容传输到文件`get-docker.sh`

最终，该文件必须被执行(作为根用户):

```
sh get-docker.sh
```

这个脚本在你的服务器上安装了你需要使用 docker 的所有东西。

## 2.2 初始化 Docker 群组

Docker 安装完成后，选择您的 Manager 节点。管理节点将是您集群中的服务器，负责所有部署和*管理*。管理器节点应该是环境中的服务器，其 IP 由域名作为目标。

在您的管理器上执行以下命令:

```
docker swarm init --advertise-addr X.X.X.X
```

该命令激活服务器上的群组模式。`advertise-addr`必须是来自服务器的内部 IP。(通常，如果您从同一家托管公司购买了多台服务器，您可以将它们全部放入内部网络并获得内部 IP)。

## 2.3 完成码头工人群

最后一步是从其他服务器加入 docker 群。在执行了前面的命令之后，最后会打印出一些应该在集群中的剩余服务器上执行的行。它看起来会像这样:

```
docker swarm join --token SOME_CRYPTIC_HASH_TOKEN X.X.X.X:2377 
```

在网络中的每台其他服务器上执行该命令，然后这些服务器将连接到 Manager 的 Worker 节点。

# 3.可选增强功能

现在 swarm 已经可以使用了，可以部署服务了。

我建议至少部署[门架](https://github.com/portainer/portainer/blob/develop/README.md)。Portainer 是一个容器管理服务，可用于管理和部署服务。

作为起点，您可以使用 docker-compose.yml

在 Manager 节点上保存该文件后，可以将其部署在 Docker Swarm 中:

```
$> docker stack deploy -c docker-compose.portainer.yml portainer
```

执行该命令后，可以立即通过[***http://YOUR _ DOMAIN:9000***](http://YOUR_DOMAIN:9000)访问 Portainer

# 4.结束语

请记住，示例 Portainer 实例将在没有 SSL 的情况下部署**。为此，您可以使用 traefik 之类的负载平衡器。首先，您可以阅读一篇文章，解释如何在**普通 docker 环境中设置 traefik 服务:****

[](/how-to-setup-traefik-v2-with-automatic-lets-encrypt-certificate-resolver-83de0ed0f542) [## 如何使用自动加密证书解析器设置 Traefik v2

### 在我学会如何 docker 之后，我真正想要的下一件事是一个帮助我组织网站的服务…

levelup.gitconnected.com](/how-to-setup-traefik-v2-with-automatic-lets-encrypt-certificate-resolver-83de0ed0f542) 

此外，我正在撰写一篇后续文章，其中包含了你希望 Docker 群体提供的最佳服务的信息。traefik 将作为负载平衡器参与其中。关注或订阅我，当我的“docker swarm services”文章在 Medium 上发表时，您会得到通知。

本博客到此结束。我很想听听你的想法和想法🤗请把它们记在下面👇👇👇

*✍️写的*

*保罗·克努特*

👨🏻‍💻🤓🏋️‍🏸🎾🚀

丈夫，两个孩子的父亲，极客，终身学习者，技术爱好者&软件工程师

*本文最初发表在我的博客上*[*https://www.paulsblog.dev/docker-swarm-in-a-nutshell/*](https://www.paulsblog.dev/docker-swarm-in-a-nutshell/)

*问好🙌开:*

[*Twitter*](https://www.twitter.com/paulknulst) *，*[*LinkedIn*](https://www.linkedin.com/in/paulknulst/)*，*[*GitHub*](https://github.com/paulknulst)*，* [*个人网站*](https://www.paulsblog.dev)