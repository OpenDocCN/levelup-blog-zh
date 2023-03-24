# 固体软件体系结构中的“D”——依赖倒置原则

> 原文：<https://levelup.gitconnected.com/the-d-in-solid-software-architecture-dependency-inversion-principle-80603f29ef77>

## “D”代表依赖倒置，提倡依赖抽象而非具体实现。

当开发新系统时，通常很容易注意到创建模块化结构和遵守[单一责任原则](/the-s-in-solid-5a6e0d778cbc)。因此，人们通常首先构建负责特定任务的模块，然后在业务逻辑中使用它们。

然而，这通常会带来一个缺点:这些系统通常是紧密耦合的，因为业务逻辑直接依赖于底层模块。

![](img/7fd46b8c17953281663535696a959a04.png)

不要构建棘手的系统。(照片由[道格拉斯·巴格](https://unsplash.com/@nzdoug16?utm_source=medium&utm_medium=referral)拍摄)

依赖性倒置意味着改变(“倒置”)这种关系。与其直接引用底层模块，不如引用它的抽象。

让我们看一个类似于[开闭原理](/the-o-in-solid-software-architecture-open-closed-principle-ccdb25bbecd2)的例子。实际上，这种相似性并不奇怪，因为这两个原则通过消除代码中的紧密耦合而相互受益。

`NotificationHandler`实例化`EmailBroadcaster`(因此使其成为直接依赖项)并在以后调用它。这导致了以下问题:

*   处理程序不容易测试，因为无法模拟真实的`EmailBroadcaster`——需要同时测试两者
*   处理程序依赖于低级模块，这意味着它受该低级模块中的变化的影响

一个潜在的解决方案可能如下所示:

在这种情况下，`NotificationHandler`不需要任何关于`EmailBroadcaster`实现的知识。它需要知道的是，给定一个“广播者”,它可以发送通知。

通过将`Broadcaster`接口放在两个类之间并颠倒关系，我们去除了紧密耦合，现在可以享受**简单测试**和一个**不太容易受到低级变化的连锁反应**的系统。

您可能已经注意到,“新”关键词并没有完全消失。它仅仅向上移动了一级，进入`onAccountCreated`功能。理论上，这将导致与`NotificationHandler`中类似的问题。

这就是“控制容器倒置”(简称 IoC 容器)发挥作用的地方。通过管理模块之间的所有依赖关系，它们允许更高层次的抽象，例如通过将它们自动注入到类的构造函数中。

大多数现代框架都具备这种开箱即用的“自动连线”能力。在我们的例子中，`onAccountCreated`函数很可能是模块的一部分，可以通过我们自己的或框架 IoC 容器连接起来。然后`NotificationHandler`将通过容器被实例化，并直接提供给我们可以全局配置的广播器。

依赖性反转是一个强大的概念，它允许构建更加模块化的系统，并且能够更快地适应不断变化的需求。该原则是五项坚实原则中的最后一项。

在我的博客上注册[，就能收到关于新帖子的电子邮件通知。](http://blog.richartkeil.com)