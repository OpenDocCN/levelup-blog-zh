# Java 虚拟线程:数百万线程唾手可得

> 原文：<https://levelup.gitconnected.com/java-virtual-threads-millions-of-threads-within-grasp-e0a4d26548ba>

![](img/ca1d991fce233f334d92b467116acc87.png)

Java 中的虚拟线程是 Java 语言中期待已久的特性，我们终于可以在 Java 19 的预览版中使用它了。虚拟线程被添加到 [JEP-425](https://openjdk.org/jeps/425) 作为织机项目的一部分。Project Loom 的目标是在 Java 中实现高吞吐量、轻量级的并发模型，虚拟线程是实现这一目标的核心部分。

# 为什么虚拟线程很重要？

在 Java 的早期，最初的线程模型类似于虚拟线程，因为线程是在用户空间中管理的，不直接使用操作系统线程。然而，在 Java 1.2 版本中，这种情况发生了变化。现代的并发模型是,`java.lang.Thread`对象只是操作系统线程的一层薄薄的包装。老实说，这多年来一直很好地服务于这门语言。然而。它确实有它的问题。

当今最常见的现代服务器线程模型被称为“每请求线程”在这个模型中，为处理请求创建了一个预先分配的处理线程集合。当 Java 服务器等待请求时，线程处于“暂停”状态，不占用 CPU 资源。一旦一个请求进入服务器，一个线程被分配给该请求，并且该线程专用于该请求，直到它被完全处理。这种线程模型很容易理解，因此很容易维护。不利的一面是，如果您分配了 100 个线程用于处理，并且有一个额外的请求进来，那么最后一个需要等待处理。如果所有 100 个分配的线程都在持续处理，那么无论如何都不会有额外的能力来处理第 101 个请求，所以这是合理的。问题在于，在实际应用中，线程几乎总是在某个时刻等待。在等待的这段时间里，没有完成任何有用的事情，但是可以被处理的额外请求被阻塞了。

因此，如果我们的线程正在等待，也许我们可以制造越来越多的线程，以便总是有请求可用。这带来了几个问题。

*   一个操作系统只能管理有限数量的线程。
*   创建和管理线程需要对操作系统进行系统调用，这很慢。
*   操作系统线程占用大量内存来存储它的堆栈(在 Java 中这是 1MB)

由于这些原因，我们不能随心所欲地创建尽可能多的 OS(或 Project Loom 世界中所谓的“平台”)线程。创建和管理线程是昂贵的，这就是为什么我们有线程池的最佳实践。我们只需支付一次线程创建和内存开销，并反复重用这些有限的线程。

虚拟线程彻底颠覆了这一切。虚拟线程是在 JVM 中管理的线程。正因为如此，内存使用量非常小，而且是动态的。上下文切换也没有成本，因为这都是在用户空间中完成的。最后，线程不需要占用固定大小的内存，因此在一个特定的 JVM 中可以创建数百万个虚拟线程。

# 虚拟线程是如何工作的

对我来说，虚拟线程最好的一点是它们是如何实现的。纵观 Project Loom 的历史，对于如何实现该功能，有许多选择。他们最终得出的解决方案令人印象深刻。虚拟线程只是我们已经使用多年的同一`java.lang.Thread`的新实现。这意味着在有意义的地方过渡到使用虚拟线程应该比它是一个具有不同接口的全新类要容易得多。这无疑需要更多的工作，但我认为这是值得的。还需要对阻塞 API 进行更新，以便向可以转移控制的虚拟线程基础设施发出信号。也许与你的假设相反，虚拟线程不知道阻塞方法，阻塞方法知道虚拟线程，并在它们[将要阻塞](https://twitter.com/pressron/status/1529194803317055489)时向虚拟线程发出信号。这已经在以前的 Java 版本中的几个 refactor JEPs 中出现过，现在我们得到了一些回报。

与目前的平台线程相比，存在一些限制。这些在未来可能会改变，但这是目前的状态。

*   虚拟线程总是守护线程。这意味着 JVM 在退出之前不会等待虚拟线程完成。
*   您不能在虚拟线程上设置线程优先级。
*   所有虚拟线程都属于一个特殊的“VirtualThread”线程组。
*   虚拟线程上不实现`stop`、`suspend`和`resume`方法。

既然我们已经定义了虚拟线程接口，那么它们是如何工作的呢？尽管 JVM 管理虚拟线程，但是要执行虚拟线程，它们需要在 OS 线程(平台线程)上运行。默认情况下，JVM 将创建一个专门用于运行虚拟线程的 FIFO ForkJoin 线程池(这不同于 JVM 中已经存在一段时间的常见 ForkJoin 线程池)。这个线程池的线程数量将与您正在执行代码的系统上的处理器数量相同(不过这是可配置的)。操作系统负责调度平台线程，而 JVM 负责调度虚拟线程。当虚拟线程中运行的代码调用 JDK API 中的阻塞操作时，不同虚拟线程之间的所有传输都会发生。发生这种情况时，运行时会执行一个非阻塞操作系统调用，并自动挂起虚拟线程，直到阻塞操作完成。特定虚拟线程被调度到(或*安装*到)的平台线程(在文档中称为*载体线程*)可能在虚拟线程的整个生命周期中改变。虚拟线程和平台线程之间没有永久的关系，只有特定虚拟线程运行时的临时关系。

# 虚拟线程在运行

## 内存使用

首先，每个人都在谈论如何创建数百万个虚拟线程，所以让我们来试试吧。首先，我们将看看平台线程会发生什么。

创建一百万个操作系统线程的代码

虽然我的机器在执行了 60k 左右的线程后没有耗尽内存，但我还是放弃了执行，因为尝试加速线程运行需要太长时间。

将此与虚拟线程选项进行比较:

创建一百万个虚拟线程的代码

使用几乎相同的代码，这几乎立即完成了线程的旋转！

## 提高利用率

前面的例子很有趣，但不是很有用。让我们考虑一些更有用的东西。我从 Alexa 排名列表中收集了前 250 个网站，我将让我的电脑访问每个网站的登录页面。这更接近于一种真实的应用程序，在这种应用程序中，与 web 服务的交互完成了大量的工作，同时我们仍然可以快速搜索。

实现的核心部分如下所示:

用于查询热门 URL 的代码

平台线程和虚拟线程版本之间的唯一区别在于，平台线程的`ExecutorService`是用`Executors.newFixedThreadPool(Runtime.getRuntime().availableProcessors())`生成的，而虚拟线程的`ExecutorService`是用`Executors.newVirtualThreadPerTaskExecutor()`创建的，顾名思义，T3 会为传递给它的每个任务创建一个新的虚拟线程。

比较我机器上两个系统的运行时间，我发现虚拟线程的执行时间缩短了 25–50%。这些都是顶级网站，因此反应相当快，即使如此，能够充分利用虚拟线程的等待时间会产生令人印象深刻的结果。

## 虚拟线程还有什么不能改进的吗？

在这一点上，似乎没有什么是虚拟线程不能改进的。可以理解的是，情况并非如此。虚拟线程并不能让你的代码更快(除了更快地创建线程之外，但这可能是你的应用程序运行时间中微不足道的一部分)。它的目的是提高你的应用程序的吞吐量。因此，它不是让一个特定的处理路径更快，而是让您在相同的时间内处理更多的项目。你可以把这想象成虚拟线程在*等待*时非常棒。如果没有等待或阻塞，例如文件 I/O 或网络，虚拟线程不会降低执行时间。

让我们来看一个例子。对于[这个实验](https://github.com/kylec32/JavaVirtualThreadsExperiments/tree/main/src/main/java/com/scaledcode/vthreads/primegeneration)。我们将同时使用虚拟线程和平台线程，使用一个简单、低效的算法来计算一定数量的素数。用两种类型的线程执行这段代码会产生几乎相同的结果。因为该功能是 CPU 限制的，并且没有在虚拟线程中产生控制的阻塞调用点，所以它们的行为几乎是相同的。

值得注意的是，尽管平台线程和虚拟线程的行为是相似的，但在这种情况下，它们在调度方面有很大的不同。对于操作系统负责调度线程的平台线程，它将抢占一个线程，给所有线程一个“公平”的 CPU 量。目前，虚拟线程不会以公平的名义做任何事情。虚拟线程只有在完成执行或进行阻塞调用时，才会从其载体线程中卸载。[这篇文章](https://www.morling.dev/blog/loom-and-thread-fairness/)更详细地介绍了调度公平性和虚拟线程目前是如何工作的。值得注意的是，最初实现了为虚拟线程提供公平性的机制，但是移除了。似乎 go 中的 Go 例程最初也有同样的问题，但后来被修复了，也许 Java 也有类似的过程。

# 可能遇到的问题

当一个虚拟线程遇到一个阻塞调用时，有两种主要的方式使它无法从它的载体线程中卸载。第一个是当一个载体线程被*捕获*时，它阻塞了虚拟线程和载体平台线程。这种捕获行为可能会在遇到某些操作系统限制(尤其是文件操作)以及 JVM 中的限制(例如`Object.wait()`)时发生。如果检测到被捕获的线程，JVM 将在线程被捕获时临时创建另一个平台线程。这导致虚拟线程线程池中的线程数量暂时多于处理器数量。有一个配置可以设置可以添加到这个线程池的线程数量的上限。

虚方法可能遇到麻烦的另一种情况是当它调用本地方法、外部方法或`synchronized`块时。这导致虚拟线程被*固定*到载体线程。固定线程不会从载体线程中卸载，否则它会卸载。JVM 不试图补偿被钉住的线程，因为据信开发人员如果愿意可以避免钉住。通过提供`-Djdk.tracePinnedThreads` JVM 标志，可以在虚拟线程锁定时请求 JVM 输出堆栈跟踪。如果您提供`full`作为标志的值，您将收到一个完整的堆栈跟踪，例如:

```
Thread[#40,ForkJoinPool-1-worker-8,5,CarrierThreads]
 java.base/java.lang.VirtualThread$VThreadContinuation.onPinned(VirtualThread.java:180)
 java.base/jdk.internal.vm.Continuation.onPinned0(Continuation.java:398)
 java.base/jdk.internal.vm.Continuation.yield0(Continuation.java:390)
 java.base/jdk.internal.vm.Continuation.yield(Continuation.java:357)
 java.base/java.lang.VirtualThread.yieldContinuation(VirtualThread.java:370)
 java.base/java.lang.VirtualThread.parkNanos(VirtualThread.java:532)
 java.base/java.lang.VirtualThread.doSleepNanos(VirtualThread.java:713)
 java.base/java.lang.VirtualThread.sleepNanos(VirtualThread.java:686)
 java.base/java.lang.Thread.sleep(Thread.java:451)
 com.scaledcode.vthreads.pinnedthreads.ClassThatPins.incrementer(ClassThatPins.java:6) <== monitors:1
 com.scaledcode.vthreads.pinnedthreads.Runner.lambda$main$0(Runner.java:12)
 java.base/java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:577)
 java.base/java.util.concurrent.ThreadPerTaskExecutor$ThreadBoundFuture.run(ThreadPerTaskExecutor.java:352)
 java.base/java.lang.VirtualThread.run(VirtualThread.java:287)
 java.base/java.lang.VirtualThread$VThreadContinuation.lambda$new$0(VirtualThread.java:174)
 java.base/jdk.internal.vm.Continuation.enter0(Continuation.java:327)
 java.base/jdk.internal.vm.Continuation.enter(Continuation.java:320)
```

如果提供了`short`的值，则只打印导致固定发生的堆栈帧，如。

```
Thread[#24,ForkJoinPool-1-worker-1,5,CarrierThreads]
 com.scaledcode.vthreads.pinnedthreads.ClassThatPins.incrementer(ClassThatPins.java:6) <== monitors:1
```

# 可观察性和工具

围绕虚拟线程的可观察性和工具是该功能的设计者保持现状的另一个地方。在虚拟线程中设置断点没有问题，因为它们仍然只是运行在一个平台线程上。其他工具如 Java Flight Recorder 也已经集成到虚拟线程中。因为现有的线程转储格式没有准备好使用虚拟线程创建大量的线程，所以现有的线程转储功能此时将只转储平台线程。`jcmd`增加了一个额外的功能，允许转储所有线程，包括虚拟线程，以一种更好地为大量线程准备的格式。总的来说，尽管虚拟线程仍处于预览阶段，但该工具已经处于一个很好的位置，这主要是因为虚拟线程的工作方式与平台线程非常相似。

## 要记住的事情

虚拟线程仍然是 preview 中的一个特性，因此，即使您使用的是 Java 19，也不会立即可用(`--enable-preview`标志必须传递给 JVM)。即使有预览标志，虚拟线程也不应该在生产系统中使用。

虚拟线程的使用将需要思想上的转变，因为在 Java 中只有操作系统管理的线程。在历史上，线程池是处理线程的最佳实践，而对于虚拟线程，线程池现在是一种反模式。虚拟线程是廉价的，只要有工作要做，你就应该放心地创建它们，然后相信调度程序会处理剩下的工作。

开发人员使用线程池的另一个原因是通过只允许一定数量(线程池的大小)的线程访问下游资源来保护下游服务。这种实践仍然可以用虚拟线程来实现，但是这种机制可以通过信号量或其他类似的进程来实现。

另一个要注意的事项是要注意经常访问的代码中的`synchronized`块。如上所述，一个同步块将*把一个虚拟线程*固定到它的载体上，不允许它像预期的那样卸载，并在它被阻塞时获取其他有用的工作。相反，考虑使用类似`ReentrantLock`的东西来促进相同的行为。这并不意味着您必须在使用虚拟线程之前从代码中移除所有的`synchronized`块。特别是启动任务和其他低容量呼叫可能完全可以使用`synchronized`模块。利用`jdk.tracePinnedThreads` JVM 标志来检测和分析代码的哪些部分(如果有的话)存在导致线程锁定问题的风险。

最后要记住的是考虑限制线程局部变量的使用。对于虚拟线程的设计者来说，线程局部变量仍然适用于虚拟线程，但它们占用了堆栈中的内存，当虚拟线程被卸载和安装时，必须来回复制这些内存。当您有可能拥有数百万个虚拟线程时，这可能会很快增加。JVM 本身做了一些改变，减少了线程局部变量的使用，从而有助于实现这些目标。有一些想法是关于如何在一个有数百万线程的世界中模仿一个线程局部变量的行为，但是这些仍然处于早期 JEP 提议阶段。

# 结论

虽然已经过了很长时间，但 Java 中的虚拟线程开始感觉触手可及。我认为当前虚拟线程的设计很吸引人，并且在向后兼容性方面非常类似 Java。虽然它们可能不会立即在生产系统中使用，但是我们在它们的实现中看到的前进的进展是有希望的，并且在我看来代表了 Java 的一个持续的光明的未来。

我的实验代码可以在这里找到:

[](https://github.com/kylec32/JavaVirtualThreadsExperiments) [## GitHub-kylec 32/JavaVirtualThreadsExperiments

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/kylec32/JavaVirtualThreadsExperiments) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)