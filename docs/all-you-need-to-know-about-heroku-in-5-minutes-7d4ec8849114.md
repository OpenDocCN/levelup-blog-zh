# 在 5 分钟内你需要知道的关于 Heroku 的一切

> 原文：<https://levelup.gitconnected.com/all-you-need-to-know-about-heroku-in-5-minutes-7d4ec8849114>

## 将您的应用程序迁移到云中

![](img/f1bdaee2d0e0b7e840bba049cff5a6cd.png)

埃姆雷·卡拉塔什在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

你是主持新手吗？并且希望在云上托管您的应用程序？免费的？！

Heroku 是一个托管平台，在托管您的解决方案时，您可能要考虑它。特别是如果你是主机新手，没有主机经验，这将是一个很好的开始。

# 什么是 Heroku

[Heroku](http://www.heroku.com) 是一个平台即服务(PaaS ),使开发者能够完全在云中构建、运行和操作应用。

该平台支持多种编程语言。

Heroku 通过名为 Dynos 的虚拟容器运行应用程序。

简而言之，Heroku 是一个用于部署和运行现代应用程序的平台。

# 官方支持的语言

Heroku 官方支持这些语言。然而，它并不局限于这些语言。

*   Java 语言(一种计算机语言，尤用于创建网站)
*   红宝石
*   服务器端编程语言（Professional Hypertext Preprocessor 的缩写）
*   节点. js
*   计算机编程语言
*   斯卡拉
*   Clojure

您还可以通过第三方构建包运行任何在 Linux 或 Heroku 上运行的语言。你可以在这里查阅[中的所有构建包。](https://elements.heroku.com/buildpacks)

# 为什么是 Heroku？

Heroku 非常 ***开发者友好*** 。对于想要学习软件部署和托管的新手来说，这是一个很好的开始。对于新手开发者来说，Heroku 是 ***免费*** 。这有利于托管概念证明和个人项目。免费层提供:

*   每月 550-1，000 小时
*   使用 Git 和 Docker 部署
*   自定义域
*   容器编排
*   自动操作系统补丁

我希望你没有得到这个平台是专门为新手开发者准备的想法。Heroku 为各种规模的开发人员、团队和企业提供部署、管理和 ***轻松扩展*** 其应用程序的所有支持。如需了解更多有关其定价的信息，请点击查看[。](https://www.heroku.com/pricing)

Heroku 提供了 ***大插件*** 和 ***第三方支持*** 。除了官方支持的语言，Heroku 还通过第三方构建包支持任何运行在 Linux 上的语言。他们还提供了 200 多种附加服务，只需点击几下鼠标，就可以轻松集成这些服务。

除此之外，Heroku 拥有云计算的所有一般和预期的好处。当应用程序扩展时，作为开发人员，我们不想管理服务器。Heroku 是 ***以开发者为中心的*** ，让我们专注于编码，并让 Heroku 管理在云中使用的服务器。

说到以开发者为中心，Heroku 还有一个 [***强大的 CLI***](https://devcenter.heroku.com/articles/heroku-cli) 。你可以简单地下载并安装在任何操作系统平台上。使用 Heroku CLI，您可以直接通过终端轻松创建和管理应用程序。这是使用 Heroku 的重要部分。

在云中，安全性始终是一个问题。Heroku 为各种应用程序提供 ***安全性*** 。企业应用程序可以受益于额外的安全功能，例如与私人空间的网络隔离。所有 Heroku 安全服务的列表，点击[此处](https://devcenter.heroku.com/categories/security)。

# 限制

那么就跟 Heroku 说一下局限性吧。在评估托管平台时，我们不仅要看平台提供什么，还要看它们不提供什么以及其他限制。

Heroku 提供各种价格的不同服务。有时，价格似乎与您的应用需求不相容。既然如此，就参考 [Heroku 定价](https://www.heroku.com/pricing)。

Heroku 使用名为 dynos 的容器。因此，如果一个“空闲”的 dyno 在大约 30 分钟内没有接收到任何流量，那么 ***dyno 就会休眠*** 。如果流量开始再次流入，那么 dyno 将被唤醒。但是， ***有明显的延迟*** 。所以，根据你的需要，这可能是优点也可能是缺点。

Heroku 在许多地区都有销售。要检查这一点，您可以在 CLI 中使用`Heroku regions`命令。您还可以查看公共空间和私人空间的列表。有关信息，请参考[文档](https://devcenter.heroku.com/articles/regions)。

Heroku 在这个[链接](https://devcenter.heroku.com/articles/limits)中详细介绍了其他一些限制。

# 可供选择的事物

Heroku 是托管您的应用程序的绝佳平台。然而，IT 行业充斥着大量的选择。这里有一些比 Heroku 更好的替代品。

*   AWS 云服务
*   AZURE 云服务
*   谷歌应用引擎
*   从语法上分析
*   数字海洋
*   重火力点

我不是说 Heroku 是完美的主机解决方案。但是，考虑到您的部署、托管和维护需求，您可能需要考虑它的一些特性。

# 结论

我在为我的一些项目寻找托管解决方案，这是我发现的关于 Heroku 的内容。对我来说，这是一个相当大的环视，所以我想把它都带到一个地方，这样如果你正在寻找 Heroku，它也可以帮助你。

分享知识是获取知识的唯一途径。对于那些可能从这个故事中受益的人，请随意添加并通过评论做出贡献。

玩得开心！享受编码！