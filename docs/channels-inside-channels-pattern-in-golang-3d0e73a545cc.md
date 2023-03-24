# 古浪沟中沟模式

> 原文：<https://levelup.gitconnected.com/channels-inside-channels-pattern-in-golang-3d0e73a545cc>

![](img/09618d6e5af3090fc356229395db7aad.png)

照片由 [chuttersnap](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

作为 IBM Cloud 日常工作的一部分，有时我会用 Golang 编写代码。这篇文章是关于一个我发现很有用的技术。

Golang 的频道很棒。当涉及到流程内部的交流时，他们需要解决各种问题。有很多教程展示了基于渠道+go-routine+上下文的解决方案。模式包括扇入、扇出、流水线、速率限制器、负载平衡器等。然而，当我开始从零开始创建代码时，有时我会很纠结，尤其是在请求-响应的情况下。看起来我仍然应该使用互斥体，但是我不想这样做。这篇文章是关于如何不这样做，而是把通道放在通道里面。

# 问题是

首先这听起来很奇怪，所以让我展示一个真实的例子。想象一下，你有一份数据，随时都在变化。您可以分配一个在 go-routine 中运行的所有者方法来进行更新。假设您的流程是一个网络服务，当客户端请求到来时，主题数据通过它的 API 提供。这意味着将会从相同的数据结构中读取数据，同时在它自己的 go-routine 中可能会发生变化。让我们来看一个虚拟的例子:

如您所见，`Run()`函数每秒都在改变数据。相同结构的`Get()`函数能够在被调用时读取数据。之后是一个没有任何断言的测试用例，但还是有用的。它产生了`Run()`函数，休眠一秒钟并读取数据。Golang 工具链中有一个非常有用的工具，它能够检测数据竞争。用这个命令试试:`go test . -race`。结果是意料之中的，我们有一场严重的数据竞赛:

```
➜ go test . -race
==================
WARNING: DATA RACE
Write at 0x00c00009e070 by goroutine 9:
  github.com/libesz/datarace.(*Data).Run()
      /Users/gergo/go/src/github.com/libesz/datarace/datarace_test.go:22 +0x213Previous read at 0x00c00009e070 by goroutine 8:
  github.com/libesz/datarace.TestConcurrent()
      /Users/gergo/go/src/github.com/libesz/datarace/datarace_test.go:29 +0x96
  testing.tRunner()
      /usr/local/Cellar/go/1.13.4/libexec/src/testing/testing.go:909 +0x199Goroutine 9 (running) created at:
  github.com/libesz/datarace.TestConcurrent()
      /Users/gergo/go/src/github.com/libesz/datarace/datarace_test.go:34 +0x7a
  testing.tRunner()
      /usr/local/Cellar/go/1.13.4/libexec/src/testing/testing.go:909 +0x199Goroutine 8 (running) created at:
  testing.(*T).Run()
      /usr/local/Cellar/go/1.13.4/libexec/src/testing/testing.go:960 +0x651
  testing.runTests.func1()
      /usr/local/Cellar/go/1.13.4/libexec/src/testing/testing.go:1202 +0xa6
  testing.tRunner()
      /usr/local/Cellar/go/1.13.4/libexec/src/testing/testing.go:909 +0x199
  testing.runTests()
      /usr/local/Cellar/go/1.13.4/libexec/src/testing/testing.go:1200 +0x521
  testing.(*M).Run()
      /usr/local/Cellar/go/1.13.4/libexec/src/testing/testing.go:1117 +0x2ff
  main.main()
      _testmain.go:44 +0x223
==================
--- FAIL: TestConcurrent (1.00s)
FAIL
FAIL    github.com/libesz/datarace      1.325s
FAIL
```

它完美地报告了读取和写入可能同时发生，并可能导致数据损坏。问题实际上是，在您的应用程序中可能有多个并发任务想要访问相同的数据。你永远不知道在你的所有者 go-routine 中数据是如何写入的，而读操作将被调度到操作系统线程中。这是典型的多写多读用例，也是解决方案进入锁定算法的地方(如何使用读写互斥，等等)。).这里连 Golang 教程都在暗示互斥，而语言哲学是: [*不通过共享内存来交流；而是通过*](https://golang.org/doc/effective_go.html#sharing) *通信共享内存。*

# 一些初步尝试

因此，我们希望在上述用例的代码中创建一个没有互斥或其他 sync 原语用法的通用模式。渠道应该总是有帮助的，对吧？:)

但是为了通过信道发送任何东西，发送方和接收方都必须知道它的处理程序。它应该是`Data`结构的一部分吗？假设我们在结构中有一个`UpdateChan chan int`字段，所有者 go-routine 可以在其中宣布变更。我们立刻面临多重问题:

*   如果没有人真正对更新感兴趣，发送者部分将被阻止。如果我们创建一个缓冲通道，那么过时的更新将首先被使用。
*   如果请求是特别的(即，当某人做一个 API 请求时)，信息推送到信道也应该按需完成。

我们能否使用一个通道来发送数据读取请求，然后在同一通道上发回数据？嗯，最好不要。频道是类型化的，这是 Go 的一大特色。这意味着数据应该有一个具体的数据类型，最好避免`struct{}`加讨厌的强制转换。这也意味着发送者和接收者在任何时候都不应该被交换来进行双向通信。

似乎我们需要两个通道来解决这个问题…一个通道发送读请求，另一个通道将数据发送回请求者的 go-routine。大概是这样的:

```
type Data struct {
 secretOfTheSecond int
 ReadRequest chan struct{}
 ReadResponse chan int
}
```

我们越来越近了。如果任何 go-routine 想要数据，它将任何东西发送到`ReadRequest`通道，并开始监听`ReadResponse`通道。`Run()`等待任何推入`ReadRequest`通道的数据，并将当前数据推入`ReadResponse`通道。

但是我们现在有了一个新问题…

一个频道可以被多个 go-routine 同时自由收听。如果多个接收者对来自响应通道的更新感兴趣，则只有其中一个接收者能够读取更新。当然，如果有多个接收者，他们都应该发送数据请求，但是哪个响应是针对谁的，等等..我们认为，这不是防弹的。

响应通道应归数据请求者所有，不得共享。

# 在渠道中发明渠道

当然这不是我发明的。通道是一等公民，是围棋中的一种基本数据类型。作为声明的一部分，它们被赋予另一种任意的数据类型，以后可以传输。也就是说，一个通道可以保存任何数据类型，也可以是一个通道！优雅。

我们的新数据结构可以是这样的:

```
type Data struct {
 secretOfTheSecond int
 readRequest chan chan int
}
```

现在，我们可以为该结构重新实现`Get()`方法，以在读取器的上下文中创建一个独占通道(类型为`int`),并将其发送到`readRequest`通道中。数据所有者 go-routine 可以调度这个事件，并可以提取数据请求者创建的通道来发送响应。

完整的示例如下所示:

如我们所见，我们不再有数据竞争:

```
➜ go test . -race
ok      github.com/libesz/datarace      2.200s
```

作为增强的一部分，我们开始将我们的`Run()`方法改为一个小的事件循环(为了清晰起见，没有实现错误处理)。它将用数据更新序列化所有传入的请求。您可以通过添加多个不同的读(或写！)请求，它们各自的通道位于结构中的通道成员字段中。

我发现这个图案非常干净优雅。您可以将它引入多个用例。当然，在底层，通道使用较低级别的同步原语，如互斥体和共享内存，但我们不一定要亲自动手处理这些东西:)。

如果您有一个对性能和时间非常敏感的应用程序，您可能想在应用上面的想法之前阅读另一篇文章。这是一个关于 Golang 信道性能的详细基准:[https://syslog . ravelin . com/so-just-how-fast-are-channels-anyway-4c 156 a 407 e 45](https://syslog.ravelin.com/so-just-how-fast-are-channels-anyway-4c156a407e45)