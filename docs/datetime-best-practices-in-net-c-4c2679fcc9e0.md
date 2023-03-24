# 中的日期时间最佳实践。NET C#

> 原文：<https://levelup.gitconnected.com/datetime-best-practices-in-net-c-4c2679fcc9e0>

## 最佳实践

## 在中使用 DateTime 时要遵循的最佳实践。NET C#

![](img/2d71ea3ebd11887d0d2a2fbe1580482f.png)

由 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的[埃斯特·扬森斯](https://unsplash.com/@esteejanssens?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄，由[艾哈迈德·塔雷克](https://medium.com/@eng_ahmed.tarek)调整

# 介绍

在我们正在开发的几乎所有软件系统中，我们都需要使用 **DateTime** 来表示一些动作的时间戳。

然而，我注意到的是，有时我们会犯一些在某些场合可能是致命的错误。

因此，我决定写这篇文章，与大家分享在**中处理 **DateTime** 时可以遵循的一些最佳实践。NET C#**

![](img/dae42316c04548aad197e34b378e3bf1.png)[](https://medium.com/subscribe/@eng_ahmed.tarek) [## 🔥订阅艾哈迈德的时事通讯🔥

### 订阅艾哈迈德的时事通讯📰直接获得最佳实践、教程、提示、技巧和许多其他很酷的东西…

medium.com](https://medium.com/subscribe/@eng_ahmed.tarek) ![](img/dae42316c04548aad197e34b378e3bf1.png)![](img/9478296cd9f1f74aaadc79e31323a3f7.png)

由[JESHOOTS.COM](https://unsplash.com/@jeshoots?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄，由 [Ahmed Tarek](https://medium.com/@eng_ahmed.tarek) 调整

# 议程

在本文中，我们将讨论以下主题:

1.  **日期时间** vs **日期时间偏移量**。
2.  注意值初始化的时间。
3.  统一来源。
4.  静态与抽象。

现在，你可以系好安全带，享受旅程了。

![](img/dae42316c04548aad197e34b378e3bf1.png)

# **日期时间** vs **日期时间偏移量**

我确信这一切。NET 开发者知道 **DateTime** ，然而，并不是所有人都知道 **DateTimeOffset** 。两者有显著的区别。

关于这个话题，[就这样结束了吗？不，我鼓励你做自己的研究和评估，因为这将有助于你了解更多。](https://medium.com/u/2f1faa247027# </strong>。</p><p id=)

[最后，我希望你喜欢读这篇文章，就像我喜欢写它一样。](https://medium.com/u/2f1faa247027# </strong>。</p><p id=)

[![](img/dae42316c04548aad197e34b378e3bf1.png)](https://medium.com/u/2f1faa247027# </strong>。</p><p id=)

# [希望这些内容对你有用。如果您想支持:](https://medium.com/u/2f1faa247027# </strong>。</p><p id=)

[如果你还不是**中的**会员，你可以使用](https://medium.com/u/2f1faa247027# </strong>。</p><p id=) [**我的推荐链接**](https://medium.com/@eng_ahmed.tarek/membership) ，这样我可以从**中**得到你的一部分费用，你不需要支付任何额外的费用。订阅 [**我的简讯**](https://medium.com/subscribe/@eng_ahmed.tarek) 将最佳实践、教程、提示、技巧和许多其他很酷的东西直接发送到您的收件箱。

![](img/dae42316c04548aad197e34b378e3bf1.png)

# 其他资源

这些是你可能会发现有用的其他资源。

[](/curse-of-recursion-in-net-c-b017271ddbe6) [## 递归的诅咒。NET C#

### 为什么以及如何在？NET C#

levelup.gitconnected.com](/curse-of-recursion-in-net-c-b017271ddbe6) [](/how-to-cancel-a-running-process-in-a-separate-request-command-in-net-c-2ca8fb733618) [## 如何在单独的请求/命令中取消正在运行的进程。NET C#

### 了解如何在单独的请求中取消已经运行的进程。NET C#

levelup.gitconnected.com](/how-to-cancel-a-running-process-in-a-separate-request-command-in-net-c-2ca8fb733618) [](https://medium.com/illumination/how-to-drive-traffic-to-your-medium-stories-f82a9f5eb6c7) [## 如何为你的媒体故事带来流量

### 如何在媒体上取得成功并为你的故事带来流量的实用指南。

medium.com](https://medium.com/illumination/how-to-drive-traffic-to-your-medium-stories-f82a9f5eb6c7) [](/template-method-design-pattern-in-net-c-73d0be82571e) [## 中模板方法设计模式的分析。NET C#

### 中学习模板方法设计模式。NET C#并探索不同的可能性。

levelup.gitconnected.com](/template-method-design-pattern-in-net-c-73d0be82571e) ![](img/dae42316c04548aad197e34b378e3bf1.png)