# 如何在 Java 中使用 Executor 框架

> 原文：<https://levelup.gitconnected.com/how-to-use-the-executor-framework-in-java-58a610d20b87>

## Java Executor 线程管理完全指南—第 1 部分

![](img/1548407e04a5e3ee2474081b912ebea3.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Héctor J. Rivas](https://unsplash.com/@hjrc33?utm_source=medium&utm_medium=referral) 拍摄

在 Java 5 之前，线程的创建和管理只能在应用层内部完成。创建线程对象需要大量内存。所以在大规模的应用中，如果继续创建大量的线程对象，会造成很大的内存开销。因此，最好将线程的创建和管理与应用程序的其余部分分开。

因此，Java 引入了 Executor 框架作为解决方案。Executor 框架包含一系列用于有效管理多线程的特性。你不需要每次用 Executor 创建新的线程，因为它允许你在需要的时候使用已经创建的线程。因此，使用 Executor 将在 Java 应用程序中节省大量内存，同时也节省了您的宝贵时间。

在本文中，您将了解 Executor 框架、线程池、用于创建线程池的不同方法，以及如何使用它们管理线程。让我们现在开始吧。

# 执行器接口和执行器类

执行器框架有三个执行器接口:`Executor`、`ExecutorService`和`ScheduledExecutorService`。

`Executor`是一个简单的接口，包含一个名为`execute()`的方法来启动一个由`Runnable`对象指定的任务。

`ExecutorService`是`Executor`接口的子接口，增加了管理线程生命周期的功能。它还包括`submit()`方法，类似于`execute()`方法，但用途更广。`submit()`方法的重载版本可以接受一个`Runnable`和一个`Callable`对象。`Callable`对象类似于`Runnable`对象，只是由`Callable`对象指定的任务也可以返回值。因此，如果我们将一个`Callable`对象传递给`submit()`方法，它将返回一个`Future`对象。`Future`对象可用于检索`Callable`返回值，并管理`Callable`和`Runnable`任务的状态。

`ScheduledExecutorService`是`ExecutorService`的子接口。它添加了在代码中调度任务执行的功能。

除了上面提到的三个接口，executor 框架还提供了一个名为`Executors`的类，它包含了创建不同类型的 Executor 服务的工厂方法。我们可以使用这个类和接口创建线程池。现在让我们看看什么是线程池以及如何创建它们。

# 线程池

线程池是一组`Runnable`对象和一组持续运行的线程的集合。这个`Runnable`对象的集合被称为工作队列。持续运行的线程检查新工作的工作查询，如果需要完成新工作，它们将执行工作队列中的一个`Runnable`对象。要使用 Executor 框架，我们需要创建一个线程池，并将任务提交给它执行。Executors 类中主要有四种方法用于创建线程池。让我们分别用例子来讨论它们。

## newSingleThreadExecutor()

在这个例子中，我们提交了 5 个任务。但是因为我们使用的是`newSingleThreadExecutor()`方法，所以一次只会创建一个新线程和一个任务。其他 4 个任务正在等待队列中等待。一旦一个任务被线程完成，下一个任务将被该线程选择并执行。`shutdown()`方法等到当前提交给执行器的任务完成后关闭执行器。但是，如果您想立即关闭执行程序而不等待，请使用`shutdownNow()`方法。

## newFixedThreadPool()

这和之前的例子是一样的，但是我们使用的是`newFixedThreadPool()`方法。这个方法允许我们创建一个线程数量固定的池。因此，在代码中，当我们提交 5 个任务时，将创建 3 个新线程，并执行 3 个任务。另外两个任务正在等待队列中等待。只要一个线程完成了一个任务，下一个任务就会被这个线程选择并执行。

## newCachedThreadPool()

当我们使用这种方法创建线程池时，线程池的最大大小被设置为 Java 中的最大整数值。根据需求，该方法将创建新的线程，如果需求减少，并且线程空闲时间超过 1 分钟，该方法将拆除线程。

在这个例子中，`newCachedThreadPool()`方法最初会创建 5 个新线程并处理这 5 个任务。队列中没有等待的任务。如果一个线程空闲时间超过一分钟，这个方法就会把它关闭。**因此，如果您想要比** `newFixedThreadPool()` **方法更好的排队性能，这种方法是一个不错的选择。** **但是如果你想限制并发任务的数量，对于资源管理，用** `newFixedThreadPool()`就可以了。

## newScheduledThreadPool()

`newScheduledThreadPool()`方法创建一个线程池，该线程池可以调度任务在指定的延迟后运行或定期运行。这个方法返回一个`ScheduledExecutorService`。在这个线程池中，有三种方法可以用来调度任务:`schedule()`、`scheduleAtFixedRate()`和`scheduleWithFixedDelay()`。让我们看一个如何使用`schedule()`方法实现这个线程池的例子。

从示例中可以看出，schedule 方法有三个参数，即任务、延迟和延迟的时间单位。`schedule()`方法用于在固定延迟后调度任务。`scheduleAtFixedRate()`方法用于在固定延迟后调度任务，然后定期执行该任务。`scheduleWithFixedDelay()`方法用于在初始延迟后调度任务，然后在前一个任务完成后以固定延迟执行任务。这三种方法适用于不同的场景。

这就是线程池的全部内容，现在让我们看看如何使用 executor 框架的一些重要注意事项。

# 重要注意事项

*   不要将正在等待其他任务结果的任务排队。这可能会导致死锁情况。
*   线程池必须在最后通过调用`shutdown()`方法显式终止。如果不这样做，程序将继续运行，永远不会结束。如果关机后再给执行程序发送一个任务，就会抛出一个`RejectedExecutionException`。
*   使用线程进行长期操作时要小心。这可能导致线程永远等待，并最终导致资源泄漏。
*   您需要了解有效调优线程池的任务。如果任务非常不同，那么对不同类型的任务使用不同的线程池以便对它们进行适当的调优是有意义的。

本文到此为止。在本文中，我没有讨论如何使用 Executor 框架中的`Callable`和`Future`，我将在下一篇文章的[中讨论。我希望您已经理解了 Java ExecutorService 和线程池。](/how-to-use-java-callable-and-future-5d79ecb47c8b)

感谢您的阅读，祝您编码愉快！

# 参考

*   [Java 并发(多线程)—教程](https://www.vogella.com/tutorials/JavaConcurrency/article.html)
*   [关于执行者的 Java 文档](https://docs.oracle.com/javase/tutorial/essential/concurrency/executors.html)
*   [Java 中的线程池](https://www.geeksforgeeks.org/thread-pools-java/#:~:text=Method%20Description%20newFixedThreadPool(int)%20Creates,()%20Creates%20a%20single%20thread.)