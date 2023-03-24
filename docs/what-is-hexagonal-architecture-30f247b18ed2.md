# 什么是六边形建筑

> 原文：<https://levelup.gitconnected.com/what-is-hexagonal-architecture-30f247b18ed2>

## Java 实用指南

![](img/37fff7900c4afba3f52fdce8ff6933fe.png)

[兰斯·安德森](https://unsplash.com/@lanceanderson?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# 1.概观

六角形架构是由阿利斯泰尔·考克伯恩引入的一种模式，也被称为**端口和适配器**。它加强了对核心领域的内在依赖性，这允许开发人员独立地测试和开发特性。

为了可视化这种模式的使用，让我们探索一个简单的在线商店域场景。通常，订单状态的更改会导致向客户发送通知。

考虑到前面的场景，本文展示了如何实现核心域、端口和适配器。

# 2.核心域

核心域是业务逻辑所在的地方。如前所述，假设我们正在处理一个在线商店领域。为了简单起见，我们使用 POJOs。

首先，让我们定义一个*客户*域:

接下来，让我们定义一个*订单*域:

需要注意的一个细节是名为 *OrderStatusChangedNotifier 的*端口*的使用。*

此外，我们可以看到每次订单状态改变时都会调用它。

# 3.港口

端口是一个*接口。*使用端口作为抽象可以保持域与基础设施代码的隔离。

现在让我们创建一个名为*OrderStatusChangedNotifier*的*端口*:

# 4.适配器

适配器是端口的具体实现。在我们的例子中，根据底层的实现，我们可以选择我们需要的方式发送通知。

现在让我们创建一个名为 *EmailService* 的适配器:

# 5.单元测试

最后，为了验证来自*端口*的方法被调用，让我们为*订单*域创建一个单元测试:

# 6.结论

这篇文章是 Java 中六边形架构的快速实用指南。

Github 上的[提供了源代码。感谢您阅读这篇文章。](https://github.com/emyasa/tutorials/tree/eval-article/core-java-modules/core-java-lang-oop-patterns/src)