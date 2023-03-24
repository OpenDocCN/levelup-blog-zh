# 带重入锁的线程同步

> 原文：<https://levelup.gitconnected.com/thread-synchronization-with-re-entrant-lock-bcbefeaa3dfc>

## 利用 Java 重入锁实现线程同步和实际同步的影响。

![](img/c6b0de136a968498df75e2da33f7dff8.png)

照片由[夏羽·加尼翁](https://unsplash.com/@metriics?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/threads?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在 Java 并发编程中，当涉及到多线程执行时，线程同步有着特殊的地位。本文将讨论使用 Java 重入锁来保持同步机制，而不是传统的线程同步。

## 什么和为什么？

在 Java 中，同步指的是控制多个线程访问共享资源的能力。在多线程概念中，多个线程试图同时访问共享资源，会产生不可预知的效果。线程间的可靠通信需要同步。

当多个进程必须同时运行时，就需要同步。同步的主要目标是通过利用互斥来共享资源，而不会互相干扰。操作系统中进程交互的协调是另一个目标。解决同步问题的最强大和最广泛使用的工具是信号量和监视器。信号量内置于大多数操作系统中。

## 锁定概念

Java 语言中的 *synchronized* 关键字用于创建同步机制。它构建在锁定机制之上，由 Java 虚拟机(JVM)处理。synchronized 关键字仅适用于方法和块；不包括类和变量。Java 中的 synchronized 关键字生成一个临界区，它是一个代码块。要进入临界区，线程必须首先获得适当对象的锁。

## 但是，

synchronized 关键字是在 Java 中实现线程同步的标准技术。虽然 synchronized 关键字支持一些基本的同步，但它在应用中有相当大的局限性。例如，一个线程只能接受一次锁。同步块没有等待队列，因此如果一个线程退出，任何其他线程都可以获得锁。这可能会导致另一个线程在很长一段时间内资源不足。
因此，**重入锁**被用于在同步中允许更多的灵活性。

## **什么是重入锁？**

ReentrantLock 类实现了锁接口，并确保访问共享资源的方法是同步的。对 lock 和 unlock 方法的调用围绕着操纵共享资源的代码。这为当前工作线程提供了对共享资源的锁定，并防止任何其他线程这样做。

ReentrantLock，顾名思义，允许线程多次进入一个资源上的锁。当线程最初进入锁时，设置保持计数为 1。在解锁之前，线程可以重新进入锁定模式，并且保持计数每增加一次。对于每个解锁请求，保持计数递减 1，当保持计数达到 0 时，资源被解锁。

可重入锁还提供了一个公平性参数，它使锁遵循锁请求的顺序，这样一旦一个线程解锁了资源，锁就被给予等待时间最长的线程。通过向锁的构造函数提供 true，可以启用这种公平模式。
下面的代码展示了这些锁是如何使用的:

即使在方法体中引发了异常，也总是在 finally 块中调用 unlock 语句，以确保锁被释放(try 块)。

## ReentrantLock()的方法

1.  lock() —如果共享资源最初是空闲的，调用 lock()方法会将持有计数加 1，并授予线程锁。
2.  unlock() —使用 unlock()方法时，保存的计数减一。当计数接近零时，资源被释放。
3.  tryLock() —如果没有其他线程持有该资源，对 tryLock()的调用返回 true，并且持有计数增加 1。如果资源不可用，该方法将返回 false，线程将离开而不是被阻塞。
4.  tryLock(长超时，TimeUnit 单位)—在退出之前，线程等待一段特定的时间(由方法的输入决定)，以获取资源上的锁。
5.  lock interruptible()—如果资源是空闲的，这种方法会获取锁，同时允许线程在获取资源时被另一个线程中断。这意味着，如果当前线程正在等待锁，而另一个线程正在请求锁，则当前线程将被中断，并将在没有获得锁的情况下返回。
6.  getHoldCount() — getHoldCount()方法返回资源上当前持有的锁的数量。
7.  isHeldByCurrentThread() —如果当前线程锁定了资源，此函数返回 true。

*你可以在这个* [*库中了解我使用重入锁的方式。*](https://github.com/RaviduShehan/Shared-Printer-Problem-using-Java-reentrant-lock)

当您使用可重入锁时，请记住，

*   可能会忘记调用 *finally* 块中的 unlock()方法，从而导致程序问题。在线程退出之前，确保锁被释放。
*   用于构建锁对象的公平性参数降低了程序的整体公平性。

## 结论

ReentrantLock 是同步的更好替代方案，因为它提供了许多 synchronized 所没有的特性。然而，这些明显优势的可用性不足以使 ReentrantLock 成为默认的同步方法。根据您是否需要 ReentrantLock 提供的灵活性来做出选择。

## 参考

1.  通过大量学习在 Java 中实现同步
2.  docs.oracle.com 的 ReentranLock 类

编码快乐！！！！