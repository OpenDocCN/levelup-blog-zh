# 为什么我的同事在他们的代码中使用 Thread.sleep(0)

> 原文：<https://levelup.gitconnected.com/why-does-my-colleague-use-thread-sleep-0-in-the-code-a3fd12dc98b7>

看似无用的代码其实很有用。

![](img/93d74d9ceaba1079a2ee09732341e04e.png)

最近看到一个同事的一段代码，很迷茫。他在代码中使用了线程睡眠方法`Thread.sleep(long millis)`,但是将线程睡眠时间设置为 0 秒。这是代码。

```
 int i = 0;
        while (i<10000000) {
            // business logic

            //prevent long gc
            if (i % 3000 == 0) {
                try {
                    Thread.sleep(0);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
```

睡觉 0 秒，不就是不睡觉吗？我第一反应是这个代码没用，但是看到他的评论引起了我的兴趣。让我感觉这段代码是作者专门为了优化而写的。为了了解原因，我又查了一遍 Java 文档。

> 使当前正在执行的线程休眠(暂时停止
> 执行)指定的毫秒数，取决于
> 系统定时器和调度程序的精度和准确度。线程
> 不会失去任何监视器的所有权。

然而，很明显，文档的描述无法回答我的问题，所以我向作者寻求帮助。然后作者告诉我用`Thread.sleep(0)`可以暂时释放 CPU 时间轴，然后我恍然大悟。这是一段非常有用的代码。

## 时间片循环调度算法

在操作系统中，CPU 有很多竞争策略。Unix 系统使用时间片循环调度算法。在该算法中，所有进程被分组到一个队列中。操作系统按顺序给每个进程分配一定的时间，即允许进程运行的时间。如果该进程在该时间片结束时仍在运行，CPU 将被剥夺并分配给另一个进程，如果该进程在该时间片内阻塞或结束，CPU 将立即切换。调度员所要做的就是维护一个就绪进程表。当进程用完时间片时，它将被移动到队列的末尾。

上面的代码中有一个死循环。作者希望一直用一个线程来处理业务逻辑。如果不使用`Thread.sleep(0)`主动放弃 CPU 时间片，线程资源就会一直被占用。众所周知，GC 线程的优先级较低，因此使用`Thread.sleep(0)`来帮助 GC 线程尝试竞争 CPU 时间片。但是为什么作者说长 GC 是可以预防的呢？这是关于 JVM 的垃圾收集原理。

## 安全点

以 HotSpot 虚拟机为例，JVM 不会在代码指令流中的任何位置暂停来启动垃圾收集，而是强制执行必须到达一个安全点才能暂停。换句话说，在达到安全点之前，JVM 不会停止 GC。

JVM 将在一些循环跳转和方法调用上设置安全点。但为了避免过多 safepoints 带来的沉重负担，HotSpot 虚拟机也有针对循环的优化措施。如果循环次数少，执行时间不宜过长。因此，默认情况下，使用 int 或更小数据类型作为索引值的循环不会与 safepoint 放在一起。这种循环称为可数循环。相应地，使用 long 或更大范围的数据类型作为索引值的循环被称为未计数循环，并将被放置在安全点。

然而，我们碰巧在这里有一个可数循环，所以我们的代码不会放在安全点。因此，GC 线程必须等到线程完成执行，并且可以执行到最近的安全点。但是如果使用`Thread.sleep(0)`，可以在代码中放置一个安全点。我在热点的`safepoint.cpp`里发现了以下评论。

```
// Begin the process of bringing the system to a safepoint.
  // Java threads can be in several different states and are
  // stopped by different mechanisms:
  //
  //  1\. Running interpreted
  //     The interpeter dispatch table is changed to force it to
  //     check for a safepoint condition between bytecodes.
  //  2\. Running in native code
  //     When returning from the native code, a Java thread must check
  //     the safepoint _state to see if we must block.  If the
  //     VM thread sees a Java thread in native, it does
  //     not wait for this thread to block.  The order of the memory
  //     writes and reads of both the safepoint state and the Java
  //     threads state is critical.  In order to guarantee that the
  //     memory writes are serialized with respect to each other,
  //     the VM thread issues a memory barrier instruction
  //     (on MP systems).  In order to avoid the overhead of issuing
  //     a memory barrier for each Java thread making native calls, each Java
  //     thread performs a write to a single memory page after changing
  //     the thread state.  The VM thread performs a sequence of
  //     mprotect OS calls which forces all previous writes from all
  //     Java threads to be serialized.  This is done in the
  //     os::serialize_thread_states() call.  This has proven to be
  //     much more efficient than executing a membar instruction
  //     on every call to native code.
  //  3\. Running compiled Code
  //     Compiled code reads a global (Safepoint Polling) page that
  //     is set to fault if we are trying to get to a safepoint.
  //  4\. Blocked
  //     A thread which is blocked will not be allowed to return from the
  //     block condition until the safepoint operation is complete.
  //  5\. In VM or Transitioning between states
  //     If a Java thread is currently running in the VM or transitioning
  //     between states, the safepointing code will wait for the thread to
  //     block itself when it attempts transitions to a new state.
```

而`Thread.sleep(long millis)`是一个本土的方法。

## 最后

`Thread.sleep(0)`不是无用的代码。sleep 方法可用于在 java 代码中放置一个安全点。它可以提前触发长循环中的 GC，防止 GC 线程长时间等待，从而避免延长 GC 时间的目标。写这个代码的人太强了。真的很牛逼。

[](/how-to-draw-a-technical-architecture-diagram-2d2c3b4a1d07) [## 如何绘制技术架构图

### 什么是架构图？为什么要画架构图？怎样才能画出一个简单易懂的…

levelup.gitconnected.com](/how-to-draw-a-technical-architecture-diagram-2d2c3b4a1d07) [](/seven-intellij-debug-tricks-that-every-java-developer-must-know-de26aaac736a) [## 每个 Java 开发人员都必须知道的七个 Intellij 调试技巧

### 你知道这些把戏吗？这些一定会提高你的开发效率。

levelup.gitconnected.com](/seven-intellij-debug-tricks-that-every-java-developer-must-know-de26aaac736a) [](https://medium.com/javarevisited/five-api-performance-optimization-tricks-that-every-java-developer-must-know-75324ee1d244) [## 每个 Java 开发人员都必须知道的五个 API 性能优化技巧

### 为什么你的 API 响应这么慢？也许你需要解决这些问题。

medium.com](https://medium.com/javarevisited/five-api-performance-optimization-tricks-that-every-java-developer-must-know-75324ee1d244)