# 如何使用 AWS 代码构建将节点 Typescript 应用程序部署到 AWS Elastic Beanstalk

> 原文：<https://levelup.gitconnected.com/how-to-deploy-a-node-typescript-app-to-aws-elastic-beanstalk-using-aws-code-build-f67c36e48f06>

![](img/66456f0f3d8cd273f1b9a72510bb9f0d.png)

在 Javascript 开发人员一生中的某个时刻，您可能会开始质疑自己所有的人生决策，因为您没有用 Typescript 开始那个项目

给你一个建议，如果你在做一个大项目，使用打字稿

无论如何，我必须将项目迁移到 Typescript🤒。我认为这次迁移非常值得，它有助于发现一些错误；有些我甚至不知道存在😅

现在是本文的核心，在迁移之后，即使我在 package.json 文件中指定了一个构建命令，AWS 上的部署还是失败了。

经过大量的头脑风暴，有问题的 StackOverflow 答案和花时间在可怕的 AWS 文档上；我后来找到了解决方案，那就是在我的 AWS 代码管道中添加一个构建阶段

![](img/ff2b2a1db44b7ec8e89579829a37f38a.png)

我将介绍如何在构建阶段使用 AWS CodeBuild 设置 AWS 代码管道

先决条件

*   AWS 帐户

**阶段 1:设置您的构建配置**

首先，让我们看看 YAML 的档案

脚本的第一部分相当简单；它安装所需的依赖项，然后运行您的构建命令

*工件:文件:*指定您想要发送到 AWS Elastic Beanstalk 的文件或文件夹

*discard-paths:* 选项如果设置为 *yes* 将会把所有的人工文件放在一个文件夹中，忽略它们当前的目录结构，你肯定不希望这样

你可以在这里阅读更多关于其他构建配置的信息

**第二阶段:在 AWS Elastic Beanstalk 上创建您的应用**

![](img/def0299f34d653dfa8e309fd7101f107.png)![](img/bb15c0d18a082397527bf7e5b6748eef.png)![](img/db5f3c67552086975ca68f2a91356e90.png)

根据提示创建应用程序

**阶段 3:设置 AWS 代码管道——在构建阶段使用 AWS 代码构建**

我们用默认设置创建一个新的代码管道，并将其链接到我们的 GitHub 存储库

![](img/52ffe23b70282a0b8e98dfa0977618a7.png)![](img/18c4270a7dbc94bed82419123bb9862d.png)

现在让我们配置构建阶段；为此，您必须创建一个新的代码构建项目

![](img/e0d2ade2c60da5c2c695ba18f7bbbe2a.png)![](img/6c6b18d9a7edb5a91b6cea9829cb4c65.png)

确保您为项目使用了正确的运行时。检查[这里的](https://docs.aws.amazon.com/codebuild/latest/userguide/available-runtimes.html)

![](img/a62d5a0c1d5bb758dbdb47254de60e05.png)![](img/4b83756deffcfc6c3edd9969c86f5fff.png)

您可以选择启用日志，如果是第一次使用，您可能应该这样做(您可以在以后关闭它)

![](img/7fa8adf404f98fd4e4e2f359abf01ddd.png)

现在我们将管道指向我们的弹性 Beanstalk 应用程序

![](img/32450ef1f2c2b788991a4983e0ecbbfb.png)

这就是🕺🕺

一旦构建完成，我们将看到我们编译的 typescript 应用程序🚀

![](img/e54d13d76a6a32bab6f87cfd00bb8d4c.png)

这是我的 GitHub 的链接，带有演示代码。[此处](https://github.com/toluolatubosun/hello-world-server-node-ts)

![](img/d0f7fb39184810a61900fec1cddad11c.png)

感谢您阅读完这篇文章🙌

在 [Twitter](https://twitter.com/king_tolu_7) 上关注我并访问我的[网站](https://toluolatubosun.com/)😁…下次再见，✌️