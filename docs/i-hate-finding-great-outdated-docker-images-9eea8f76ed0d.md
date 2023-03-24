# 我讨厌发现伟大的，过时的 Docker 图像🤬

> 原文：<https://levelup.gitconnected.com/i-hate-finding-great-outdated-docker-images-9eea8f76ed0d>

## 不同的 CI 平台如何自动和定期地构建和推送您的开源 Docker 映像。

![](img/deccb7f3440d9b08bd73fcf51c19f6dd.png)

安德烈·亨特在 [Unsplash](https://unsplash.com/s/photos/angry?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

最糟糕的事情莫过于在网上搜索适合您当前使用情况的 Docker 图片，却在几个误导性链接后发现您想要使用的图片是“最后一次推送:3 年前”…

# 最佳码头工人形象

在搜索符合自己需求的公共 docker 图片时，你会寻找什么？我的意思是，这样的形象应该满足什么要求？

我假设当你最后一次复制和粘贴 docker 图片到你的 CI 平台或舵图或任何你使用它的地方时，你确实考虑过这个问题。如果没有，现在或者下次再考虑吧。原因很简单，尽管在一个容器中，但您可能正在执行来自其他人的代码，这些人可能怀有恶意。

## 什么是相关的

您是否需要访问源代码(Dockerfile)来验证和/或自己构建它？

必须是最近建的还是自动建的？以便它包含最新版本的库和工具？

CI 管道运行(和结果)是否也必须公开，以便您可以检查和验证构建过程？

你会检查它所依赖的基本映像和它使用的版本吗？以便您可以确保兼容性，并且它使用最新的基本系统？

尺寸重要吗？

你上一次在容器中使用秘密是什么时候，你没有 100%检查其图像是否有恶意软件？

## 如何建立你的公众形象

为了回答你关于如何识别比其他人更可信的公共图像的问题，让我向你展示这个过程可能是什么样子的——并指出一些实际的例子。

# 建立你的形象

每个 Docker 图像的基础是 Docker 文件。下面是我在构建图像时学到的一些东西——到目前为止还不完整，所以你必须自己看得更远，那里有很多好的来源和例子。

## 使用`ARG`来支持版本

使用`ARG`,您可以将变量传递给构建过程。这允许您在 docker 文件中做各种有趣的事情，比如传递版本或条件构建步骤。

```
ARG VERSION=latest
FROM alpine:$VERSION AS base
# do some base work# each `FROM` resets ARGs, so define it before you use it
FROM base AS case-one
ARG CASE_VALUE=something
# run command(s) that use CASE_VALUEFROM base AS case-two
ARG CASE_VALUE=other
# run command(s) that use CASE_VALUEARG CASE=one
FROM case-$CASE
# continue build steps 
```

*   确保在使用`FROM`后定义默认值并重新定义参数。
*   运行`docker build --build-arg CASE=two`来基于上面例子中的`case-two`构建一个映像。

链接到 Dockerfile `ARG`参考:

[](https://docs.docker.com/engine/reference/builder/#arg) [## Dockerfile 文件参考

### 本页描述了您可以在 Dockerfile 文件中使用的命令。当您阅读完本页后，请参阅…

docs.docker.com](https://docs.docker.com/engine/reference/builder/#arg) 

## 为多种架构构建

你可能不需要，但是不同架构的使用，也就是`arm64`，正在上升。如果您使用的基础映像支持它，那么您只需要在构建管道中增加几个步骤就可以构建多个架构了！

我已经在下面的文章中描述了如何建立一个多拱图像，所以我在这里不再赘述。关键是:当你发布开源软件时，你的受众是广泛的，所以不要遗漏那些正在尝试新架构的极客，比如在树莓 Pi 上运行《我的世界》服务器...

[](https://betterprogramming.pub/setting-up-a-multi-arch-docker-build-with-circleci-and-alpine-for-your-apple-m1-ba739ef1f754) [## 用 CircleCI 和 Alpine(为您的 Apple M1)设置多拱门码头构件

### 为多个基本映像版本和平台构建 Docker 映像的简洁设置

better 编程. pub](https://betterprogramming.pub/setting-up-a-multi-arch-docker-build-with-circleci-and-alpine-for-your-apple-m1-ba739ef1f754) 

# 添加 CI 平台

现在，您的 Dockerfile 已经在您的公共 Git 存储库中了(再次，在这里做一些假设…)，是时候在管道中构建它了——可能用于多种架构。

不同管道中的方法是不同的，但它们都有一些共同点:它们可以在您推送代码时被触发，它们允许您使用 Docker 服务或 Docker-in-Docker ( `dind`)构建 Docker 映像。他们都非常有能力建立你的公众码头工人形象，所以不要浪费太多时间选择。

> 为了建立公共 Docker 形象，你的 CI 平台的选择应该是:“可用的那个”。

我将在另一篇文章中发布关于使用这些 CI 平台的详细信息，但我想在这里提到一些差异和示例:

## 基于机器的 CI 平台

这些 CI 平台在虚拟机上运行您的代码，因此您可以选择它们运行的机器映像(例如`ubuntu:bionic`或`ubuntu-2004:202101–01`或`macos`或`windows-latest`)。

例子有 [Travis-CI](https://travis-ci.com) 、 [Circle-CI](https://circleci.com) (如果使用 [Circle-CI 机器执行器](https://circleci.com/docs/2.0/configuration-reference/?section=configuration#machine))、 [GitHub 动作](https://docs.github.com/en/actions)或[信号量-CI](https://semaphoreci.com)

*   我用 Travis-CI 构建的多拱`dnsmasq` Docker 形象的例子:`[drpsychick/dnsmasq](https://github.com/DrPsychick/docker-dnsmasq)`(不是最好的例子，我必须承认，但确实有效)。
*   GitHub Actions 通过一个适当的动作让它变得简单:[构建并推送 Docker 图片](https://github.com/marketplace/actions/build-and-push-docker-images)。

## 基于 Docker 的 CI 平台

这些 CI 平台在 docker 映像中运行您的代码。在每个构建阶段，您可以选择不同的映像来运行代码，因此非常灵活。然而，你需要接受的是，在 docker 中运行任何东西也可能带来一些挑战:访问磁盘空间、服务之间的通信或与远程 docker 引擎的通信等等

例如 [GitLab-CI](https://about.gitlab.com/stages-devops-lifecycle/continuous-integration/) 、 [Circle-CI](https://circleci.com) 或 [Semaphore-CI](https://semaphoreci.com)

为了简化在基于 Docker 的 CI 平台上的构建，我发布了一组简单的 [Docker CI 映像](https://github.com/DrPsychick/docker-ci-images)，其中包含了使用`kubectl`或`helm`在 kubernetes 上构建、测试和部署映像所需的全部内容。它们实际上是通过 Circle-CI 管道构建并保持最新的。

[](https://github.com/DrPsychick/docker-ci-images) [## DrPsychick/docker-ci-images

### 可以在基于 docker 的 CI 管道中使用的图像集合。而不是将依赖项安装在…

github.com](https://github.com/DrPsychick/docker-ci-images) 

作为另一个例子，我的 [Helm Charts repository](https://github.com/DrPsychick/charts) 使用这些带有 Circle-CI 的图像来测试带有 [Chart-Testing](https://github.com/helm/chart-testing) 的图表，并将它们测试部署到管道中的[Kubernetes-in-Docker(KinD)](https://kind.sigs.k8s.io)集群。

# 添加计划

最后一步非常重要，也非常容易设置:安排管道定期运行:每周一次或每月一次。这将使您的图像保持最新，如果管道出现故障，您会收到通知(这种情况**将**发生)。

## 在中设置计划的管道运行

*   GitLab-CI:通过 [GitLab UI](https://docs.gitlab.com/ee/ci/pipelines/schedules.html) 配置日程
*   GitHub 动作:将时间表添加到您的[工作流程](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#onschedule)
*   Circle-CI:将时间表添加到您的管道 [config.yml](https://github.com/DrPsychick/docker-ci-images/blob/53f6137bde972aba4b9cea3a4142b75ce452274f/.circleci/config.yml#L128)
*   Travis-CI:通过 [Travis UI](https://docs.travis-ci.com/user/cron-jobs/) 配置 cronjobs
*   信号量-CI:通过 [Web UI 或 CLI](https://docs.semaphoreci.com/essentials/schedule-a-workflow-run/) 配置

# 摘要

我希望这能让您很好地理解这个过程，并且它实际上很容易构建，然后向开源社区提供最新的图像。

在一定的预算内，大多数 CI 平台对于开源项目都是免费的，所以**试试吧！让我知道我是否可以为这个作品添加一些我已经忘记或错过的东西。或者给我一个你的项目的链接，例如 Semaphore-CI，我可以把它添加到例子中。**

完全没有必要浪费人们为建立有用的 Docker 形象所付出的努力——因为现在缺少的只是一个 CI 平台。