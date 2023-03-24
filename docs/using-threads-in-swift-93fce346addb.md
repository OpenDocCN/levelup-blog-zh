# 在 Swift 中使用线程

> 原文：<https://levelup.gitconnected.com/using-threads-in-swift-93fce346addb>

## Swift 提供 DispatchQueue 作为原始线程之上的优秀层。但是有时你想使用一个低级的线程 API

![](img/8b1f5f31459c80ffa8f3143915010a10.png)

照片由[里卡多·戈麦斯·安吉尔](https://unsplash.com/@rgaleria?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) 拍摄

Swift 提供 DispatchQueue 作为原始线程之上的优秀层。但是有时你想创建一个新的线程专门用于一些特定的任务。或者实现自己的并发执行器。Swift 让您可以访问原始线程，在本文中，我将展示如何使用它。

# 线

使用`Thread`类在 Swift 中创建线程非常简单。您可以通过选择器指定`objc`函数作为起点，或者传递一个闭包，更方便的方法是，传递子类`Thread`。

调用初始值设定项时，线程没有启动。您需要显式地调用`start()`方法来启动 tread。

尽管`Thread`初始化器返回了它的句柄，线程还是会运行。就是这样——变量不再存在，线程仍将运行。这很好，但是你将失去控制线程的能力:检查它是否完成，等待它完成，取消它，等等。

# 等待完成，加入线程

Swift 没有提供等待线程完成的方法。

> 💡主线程可以在新线程之前完成。在这种情况下，后者也被终止

为了等待线程完成，我们可以使用`DispatchGroup`来加入线程

# 终止线程

线程到达`main`末端后自动终止。要提前退出线程，可以从线程中调用`Thread.exit()`函数。要正确使用 created `DispatchGroup`，最好创建一个自定义退出方法:

# 取消线程

除了终止线程之外，您还可以通过调用线程句柄上的`cancel()`方法或者在线程内部取消线程。这将把`isCancelled`属性设置为`true`。

为了让这个特性工作，在线程中你需要定期检查这个标志，如果标志是`true`，那么调用 exit。

> 💡调用 cancel 不会停止线程，而是通知它必须停止。你甚至可以忽略它，但这不是一个好的做法

在下面的例子中，我们可以使用 cancel 来通知线程超时。

输出:`Cancelled after 5.509860992431641 seconds`

# 并发必须是安全的

记住，在使用几个线程的情况下，共享的数据和结构必须是线程安全的。

# 用例

*   应用程序中的长期运行任务

如果你的应用程序中有一个长时间运行的任务，那么考虑为它创建一个专用线程。

*   创建并发执行人

Swift 的 DispatchQueue 和 OperationQueue 是强大的工具，但即使是它们的功能也是有限的。例如，没有[线程执行器](https://www.boost.org/doc/libs/master/doc/html/boost_asio/overview/core/strands.html)或者显式线程池。

*   将编写自己的线程池作为一种练习和一个宠物项目

为什么不呢？理解 DispatchQueue 如何工作的最好方法是编写自己的程序！

# 最后

查看我的 Swift 中的 async/await 快速指南，更好地掌握 Swift 中的并发性。

[](https://alexdremov.me/quick-guide-to-async-await-in-swift/) [## Swift 异步等待快速指南| Alex Dremov

### Alex dre mov iniOS & Swift——您需要了解的关于 Swift 异步新功能的一切。异步等待，主要演员…

alexdremov.me](https://alexdremov.me/quick-guide-to-async-await-in-swift/) 

```
**Want to Connect?**This post was originally published on [alexdremov.me](https://alexdremov.me/using-threads-in-swift/)
```