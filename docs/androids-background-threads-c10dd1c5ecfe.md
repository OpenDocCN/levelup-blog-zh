# Android 的后台线程

> 原文：<https://levelup.gitconnected.com/androids-background-threads-c10dd1c5ecfe>

![](img/197a07e59594f597083fc0c79cfd61cd.png)

照片由 [Atik sulianami](https://unsplash.com/@atik1616?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在直接进入 Android 生态系统之前。有必要谈谈线程，它是什么？什么使得系统是多线程的？。当然，如果我们谈论 Android，我们会涵盖 Java 以及其中的任何特性或工具特性。

线程的本质是当一个 CPU 执行一个指令时，此时，指令按顺序执行，这是在计算机只有一个处理器的时候。但是，在今天的市场上，我认为几乎每台现代计算机都有多个 CPU(核心)。这当然会导致同时生成多个并发执行的线程。

所以现代 Android 设备有多个内核，UI 在主线程中处理。但是在体验长时间运行的操作时，利用所有手机核心来改善体验将是非常棒的。这种类型的操作可以冻结 UI(可以发现渲染阻塞)，事实是，由于渲染阻塞，即使网络请求在主线程上也被禁止，这也应该在子进程中执行。

Java 有一些并发工具，基本上是设置一些 API 来处理后台线程，管理将要执行的任务，并对发生的事情施加一些控制。你可以参考`Thread`和`Handler`类，或者包含许多类和接口的`java.util.concurrent`包

## 安卓呢？

因为我们知道 Java 有一种特殊的方法，Android 也是，它们都包括上面提到的 Java 类，这似乎是显而易见的，因为 Android 的性质。但是无论如何，我们可以把它分成三种不同的方法。

1.  **Asynctask** 扩展，作为 Java 的`Threads`和`Handler`类的包装器。查看示例[此处](https://github.com/davidlares/android-asynctask)
2.  **IntentService** 扩展在后台自己的线程上执行任务，并在完成后关闭它们。在此查看示例
3.  而`JobScheduler API`是管理后台任务的关键工具。这种特殊的 API 可以克服现代 Android 操作系统版本中的限制。此处查一个例子

## 奖金

有更多的方法来实现并发执行。后两种加分方式之一是安卓的`Loader framework`。它被定义为“一个执行数据异步加载的类”，基本上使异步加载活动或片段中的数据变得容易，并且可以监视源数据，以便在内容发生变化时提供新的结果。

第二个是由 SquareUp 团队构建的`Retrofit` API，它是执行网络请求的理想工具。默认情况下，API 本身包含多线程。你可以在这里查看一个正在运行的 Github 库，并对其进行改造