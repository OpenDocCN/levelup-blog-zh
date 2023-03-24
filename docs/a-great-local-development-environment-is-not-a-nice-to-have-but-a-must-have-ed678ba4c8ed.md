# 一个好的本地开发环境不是一个好东西，而是一个必需品

> 原文：<https://levelup.gitconnected.com/a-great-local-development-environment-is-not-a-nice-to-have-but-a-must-have-ed678ba4c8ed>

注:更新于 2023 年 1 月**日**以包括最新的改进。

我一直认为一个优秀的本地开发环境是构建优秀软件的基础。当本地开发环境缓慢、不一致、复杂，甚至错误百出时，很难期望软件能够高质量地构建出来。

![](img/7d839133172a48ea021a4d0a7009bbbc.png)

由[克里斯托弗·高尔](https://unsplash.com/@cgower?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/mac?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

过去，我加入过一些行业领先的公司，这些公司花了我几天、几周，甚至一个月的时间让代码在我的电脑上运行，这样我就可以开始工作了。在这个过程中，我必须安装一堆软件和工具，运行一个又一个脚本，我几乎不知道他们做了什么，因为它们很长。一个小小的错误就让我花了好几天去调试。

> 你以前遇到过这种情况吗？

今天这篇文章将关注如何为构建微服务创建一个良好的本地开发环境。

# 介绍

一个好的本地开发环境有几个重要的目标:

*   **启动极其简单**:运行一条命令
*   **快速重建/重新运行** : < 2 秒
*   **一致:**不同机器上的相同环境
*   **容易理解:**最小构建脚本

这篇文章的目标是提供一个解决方案来满足所有这些需求。

我们将使用这个非常简单的 Golang 服务。它的作用是在进入 *localhost:8080* 时返回“Hello world”。在这种情况下，我们使用 Golang，但是本文中提到的技术可以很容易地应用于其他语言。GitHub 到回购的链接会在最后。

# **第一步:让它变得简单而一致**

重要的是要有这样一种心态，即开发人员是客户。您希望您的客户在处理代码库时如何获得最佳体验？一些不太好的经历是:

*   安装许多工具和软件
*   一个接一个地运行脚本
*   构建代码需要很长时间
*   找出在别人的电脑上运行的代码在你的电脑上并不运行，反之亦然

解决以上所有问题的方法是使用 Docker 容器。

![](img/64e5c422ad7b1608d9e557098b1a411e.png)

Guillaume Bolduc 在 [Unsplash](https://unsplash.com/s/photos/container?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Docker 容器是一种轻量级虚拟化技术，是构建和共享代码的事实标准。在这种情况下，Docker 是:

*   **容易上手**:不需要在本地安装东西，比如编程语言，或者各种包。它们都被嵌入到 Docker 映像中。
*   **一致**:处处环境相同。

这是集装箱化上述 Golang 服务的 Dockerfile。

这使得利用 [**Docker 多阶段构建**](https://docs.docker.com/develop/develop-images/multistage-build/) **。**使用同一个 Docker 文件进行本地开发和建立高质量的 Docker 形象变得非常容易。

这里有三个阶段。只有名为“构建器”的第一阶段用于本地开发。第二和第三阶段是构建 Golang 二进制文件，并将其放入一个更小的映像 alpine 中，这对于产品分发来说更快更安全。

# **第二步:速战速决**

> 在 Docker 容器中构建和运行应用程序时，人们主要关心的是速度。

大多数人认为码头工人的开销是无法克服的。然而，Docker 非常灵活，当你以特定的方式使用 Docker 时，它可以实现原生速度。

![](img/271e6cf19f0ad1dc24d77a1301fdedbc.png)

[澳门图片社](https://unsplash.com/@macauphotoagency?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄于 [Unsplash](https://unsplash.com/s/photos/great?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

这里有一个基准测试，在我的机器上测试了构建和运行 Golang 服务的三种方法，平均测试了 3 次。

*   **原生构建+运行**:直接在电脑上运行" *go 运行 main.go"* 。没有码头工人。
*   **非优化的 docker 构建+运行**:用上述 docker 文件运行“Docker 构建”和“Docker 运行”。
*   **优化的 Docker 构建+运行**:它利用了 3 种技术 1。我上面提到的 Docker 多阶段构建 2。在 Docker 容器 3 内重建。Docker 卷安装 4。不断观察和重建

```
Native build+run: **0.76s**
Non-opimized Docker build+run: **2.69s**
Optimized Docker build+run: **0.83s**
```

非优化的 docker 构建+运行比原生构建+运行慢 3 倍多。不奇怪。

优化后的 Docker build+run**几乎和原生速度**一样！惊喜！

除了 Docker 多阶段构建之外，还有三种技术可以最大程度地缩短时间。

**在码头集装箱内重建**

```
docker run **-t** name-of-service
```

**-t 标志**是最重要的技术，也是相对[较少记载的](https://docs.docker.com/engine/reference/run/#foreground)技术，这使得**在构建速度上相差**10 倍。它的作用是防止容器在后台运行时退出。

这使我们能够在容器内部重建 Golang 服务，从而消除了最耗时的 Docker 活动:1。重建码头工人形象 2。基于该图像创建新的容器，以及 3 .连接到容器。

它使得简单地向容器发出命令成为可能。例如:

```
// Build and run the Golang service.
docker exec -it helloworld-service go run /go/src/app/main.go
```

这个帖子给了我灵感:

 [## 让您的 Golang 容器构建速度提高 10 倍的提示和技巧

### 几周前，我在和一只虫子搏斗。

medium.com](https://medium.com/windmill-engineering/tips-tricks-for-making-your-golang-container-builds-10x-faster-4cc618a43827) 

**Docker 卷安装**

```
docker run -v $(shell pwd):/go/src/app
```

[卷挂载](https://docs.docker.com/storage/volumes/)使 Docker 能够直接使用本地文件系统中的文件，而不必每次都将它们复制到容器中。

这样做的额外好处是，如果代码中有一些自动生成的更改，它们将会反映在本地文件系统中。

**持续观察并重建**

这利用了巨大的[反射](https://github.com/cespare/reflex)组件。一旦它被安装在 Docker 中，它可以持续监视文件的变化，并自动触发一个命令。

```
docker exec -it helloworld-service reflex -s go run /go/src/app/main.go
```

在这种情况下,“reflex -s”会在检测到更改时自动重启 Golang 服务器，例如当您保存包含更改的文件时。大大减少了地方开发的时间和精力。

# **第三步:变得方便**

所以现在我们有了一个基于 Docker 的快速本地开发系统。让我们用起来更方便。因为您不想每次都键入一个很长的 Docker 命令。一个简单的 Makefile 会有所帮助。

```
# Build and run the Golang service
make run# Run all the tests
make test# Stop and remove the containers
make clean
```

这里是 GitHub repo 的链接，包含所有代码。

[](https://github.com/zhanjingjie/hello-world-service) [## 詹静洁/hello-world-service

### 这是一个报告，展示了如何创建一个生产级的本地开发环境，建立在 Docker 之上…

github.com](https://github.com/zhanjingjie/hello-world-service) 

感谢大家阅读这篇文章。如果有一个教训可以吸取的话，那就是建立一个生产质量的本地开发环境并不容易。鉴于只有几行代码，这看起来似乎很容易，但是通向解决方案的道路并不明显。

这篇文章只探讨了构建生产级微服务的一个方面，但还有更多。你可以看看我之前的帖子:

[](https://medium.com/nerd-for-tech/build-production-grade-microservices-a1bbbf68247d) [## 构建生产级微服务

### 如今，利用在线资源，构建“hello world”服务相当容易。

medium.com](https://medium.com/nerd-for-tech/build-production-grade-microservices-a1bbbf68247d) 

我正在执行一项任务，帮助工程团队和工程师更快地提高生产质量。如果您的团队对技术咨询会议感兴趣，请随时通过 [LinkedIn](https://www.linkedin.com/in/jingjiezhan/) 联系我。