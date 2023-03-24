# 使用这些命令有效地管理 Docker 资源

> 原文：<https://levelup.gitconnected.com/manage-docker-resources-efficiently-with-these-commands-f96d51f1d9de>

![](img/a29a7980b1a6073629799d501198a9aa.png)

照片由[卡梅隆通风](https://unsplash.com/@ventiviews?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/ship?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

构建 Docker 容器和图像是一种艺术形式。当涉及到构建应用程序和系统时，Docker 为开发人员提供的速度和灵活性绝对至关重要。现代软件开发的一个主要特点是能够构建完美环境的容器。但是开发人员不想处理的一件事是草率的图像和容器资源管理。

长时间不检查 Docker 会使主机瘫痪。图像可能会堆积起来，啃噬磁盘空间，运行容器可能会将 CPU 和内存挤压到极限。快速有效的资源管理是 Docker 主机顺利运行的必要条件。

在本文中，我们将研究几个简单的命令，使您能够整理 Docker 环境。您将节省宝贵的空间，提高机器速度，并更深入地了解运行中的容器。让我们来看看。

## 停止所有运行的容器

```
docker stop $(docker ps -q)
```

很难相信实际上没有一个内置的论点来实现这一点。如果您经常运行不止一个或两个容器，并且想一次停止所有这些容器，这个简单的命令就可以做到。这比遍历运行中的容器列表并对每个容器执行`docker stop`要好。

[官方码头停靠文件](https://docs.docker.com/engine/reference/commandline/stop/)。

## 测试时命名并自动移除容器

```
docker run -d --rm --name testing debian:latest sleep 300
```

当你在构建一个新容器的过程中，你很有可能会产生一批*测试版本。如果您想保存一些步骤，并让容器在退出时自动移除，那么您只需添加`--rm`作为参数。一旦容器退出，你将不再在`docker ps -a`的输出中看到它。*

另一个重要但经常被忽视的任务是给容器命名。如果您正在动态地构建容器，使用`--name`参数提供一个名称会非常有帮助。这可以让您在忘记容器后快速识别它，以便您可以删除它或保存它以备将来使用。

上面显示的命令将启动一个名为“testing”的新 Debian 容器，它执行`sleep`命令。容器以分离模式启动，一旦 300 秒结束，容器将停止并自动移除。

[官方 Docker 运行文档](https://docs.docker.com/engine/reference/run/)。

## 显示所有当前 Docker 磁盘使用情况

```
docker system df
```

如果您想做一些日常工作，您可以使用上面的命令很容易地看到 Docker 资源消耗了多少空间。这将列出映像、容器、卷和构建缓存的使用情况。输出如下所示:

```
TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
Images          14        14        4.636GB   926.9MB (19%)
Containers      15        0         23.07GB   23.07GB (100%)
Local Volumes   14        3         716MB     238.7MB (33%)
Build Cache     0         0         0B        0B
```

这将向您显示所有宝贵的磁盘空间的去向。

如果您想要更详细地列出哪个资源正在消耗空间，您可以为详细输出添加`-v`标志。

[官方 Docker 系统文档](https://docs.docker.com/engine/reference/commandline/system/)。

## 移除所有未使用的图像

```
docker image prune -a
```

你已经下载了一张又一张的图片，并在寻找完美的解决方案时旋转了一吨的容器。就在你觉得下一张图片拉完解决方案就对了的时候，*威猛*！您的磁盘空间不足。它发生在我们最好的人身上。通往安全的最快途径是清除掉所有不想要的图像。

使用上面的命令，你可以删除所有没有关联容器的 Docker 图片。如果您已经移除了测试容器，但是没有移除图像，那么这就是缺失的部分。运行这个命令，并观察您的磁盘空间返回。

还有`docker system prune`命令，但是这个更具破坏性，甚至会删除停止的容器。只有当你完全确定不需要任何已停止的容器或已解除关联的资源时，才应该使用这种方法。

[官方 Docker 图像修剪文档](https://docs.docker.com/engine/reference/commandline/image_prune/)。

## 获取容器的使用统计信息

```
docker stats --no-stream
```

如果您的一些容器正在运行资源密集型进程，您可能会发现主机系统承受了太大的压力。下一步是调查当前的资源使用情况和限制。使用上面的命令，您可以查看所有正在运行的容器的当前资源使用情况的快照。

这将表明一个特定的容器是否比其他容器占用更多的 CPU 或内存。一旦你找到了问题容器，你就可以`docker stop`它或者执行`docker kill`，如果容器被挂起的话。

[官方码头统计文档](https://docs.docker.com/engine/reference/commandline/stats/)。

*感谢您的阅读！我希望这些命令对您管理 Docker 资源有所帮助。Docker 中有许多不同的命令和参数。查看官方文档*[](https://docs.docker.com/)**或者浏览手册页获取更多有用的代码片段。**

**如果你正在寻找构建一个令人敬畏的 Docker 开发环境，请查看:* [*5 个适用于开发人员工作站的便捷 Docker 映像*](/5-handy-docker-images-for-developer-workstations-4e81fffa985b) 。*