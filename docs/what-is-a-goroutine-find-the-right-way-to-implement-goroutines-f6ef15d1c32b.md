# 什么是 Goroutine？找到实现 goroutines 的正确方法🔥

> 原文：<https://levelup.gitconnected.com/what-is-a-goroutine-find-the-right-way-to-implement-goroutines-f6ef15d1c32b>

![](img/3f780fa18b0cc28fa0782593226702a3.png)

go routines-主题

# 为什么要去？

Go 已经广泛应用于 Kubernetes、Docker、influence DB 等大多数流行的制作系统中。如此大规模地采用围棋主要有四个原因。

*   它简单而优雅
*   提供大规模内置并发
*   旨在多核上运行
*   这是超快的性能🚀

Goroutine 是 Go 中实现并发的一种方式🔥。

# 什么是 goroutine？

goroutine 是由 Go 运行时管理的轻量级线程。不像操作系统线程通常在 **MBs 范围内消耗内存才能正常工作，**这些 goroutines 只在**KBs 范围内消耗内存**。所以对于 go 运行时来说，创建 goroutines 比创建并发线程更容易、更快。go routine 不是实际的线程，它们被复用到实际的 OS 线程中，甚至你可以假设这些 go routine 是 OS 线程上的一种抽象层。

由于其**非常小的内存占用**，go 运行时很容易在几毫秒内创建**数千个 goroutines。但是创建数以千计的操作系统线程严重依赖于**内存和 CPU** ，所以与 goroutines 相比，它显然很慢。**

此外，Goroutine 没有本地存储，因此 go routine 的实现比 OS 线程更便宜，因此**go routine 在启动时间方面要快得多**。

在`go`的情况下，上下文切换也比 OS 线程上下文切换更快，因为 OS 线程是大量资源。

除此之外 **Go 还有另一个最酷的功能，即频道**🔥。通道是与 goroutines 通信的内置方式。在 OS 线程的情况下，这种通信是你能想到的最困难的事情。

这就是 go 如何实现比传统编程语言更好的并发性，这使得 **go 成为大多数用例**的默认选择语言。

# goroutine 的语法

goroutine 最难的部分是理解它，而不是它的真正实现。因此，在深入实际实现之前，请花时间去理解它。

在其他编程语言中，您可能会使用`async await`模式来实现并发(Node.js 和 python - async await)。但是在这里它甚至比那更简单。只需在函数前缀中添加`go`关键字(例如:`go someFunctionName`，现在 go runtime 在并发模式下执行相同的函数。看看这有多酷😆。

```
go addTwoNumbers(1, 2)
```

# 实现一个没有例程的程序

下面是将两个数字相加的代码片段。`addition`功能有一个`sleep`命令来模拟加工时间。

如果你使用`go run sequential.go`命令执行程序，你将得到如下输出(2 个数相加的结果)。

![](img/a68a1782c7e8d23f09195e12622a1e46.png)

顺序输出

# 用程序实现一个程序

现在让我们通过将`go`添加到调用者的前缀来将这种顺序执行转换为并发执行。下面是片段。

如果您现在使用此`go run concurrency_without_wg.go`命令执行，您会立即注意到**加法功能的输出没有显示。**

![](img/80dc55419f3b807437870e1b1e6405d9.png)

无等待并发组

如果您已经做到了这一步，那么您已经成功地在并发模式下执行了第一个程序🔥。

> 但是为什么输出会有出入呢？

这是因为 go runtime 没有等待 goroutines 完成它们的执行。所以您需要显式地更新 goroutines 的细节以运行时运行。这就是`wait groups`发挥作用的地方。这正是等待小组要解决的问题。

# 什么是等待组？

WaitGroup( `sync.WaitGroup`)是一种机制，用于等待 goroutines 集合完成它们的任务。golang 中主要有 3 个助手函数来实现这个特性。

*   添加()
*   等待()
*   完成()

`Add(5)`方法接受一个整数作为参数。这个整数表示 go 运行时在这个集合中可用的 goroutines 的数量。`Wait()`告诉 go 运行时在这个位置等待，直到 goroutines 集合完成执行。方法用于通知 go 运行时这个特定的 goroutine 已经完成了它的执行。

下面是带有 1 个 goroutine 的代码片段。更深入的理解请参考评论。

如果您现在执行，您将得到如下所示的输出。

![](img/2349619bf39e5839403342b0ab32188e.png)

并发输出

# 摘要

您已经成功实现了 goroutines🔥，但是如果没有通道(一种与 goroutines 交流的方式),这个主题将永远不会完成。

要了解`Channels`的最新消息，你可以关注这个博客。

我会💝听听你的反馈和意见，看看你能用它做些什么。如果你突然想到什么地方，请随意评论。我随时都有空。

如果你觉得这篇文章对其他人有帮助，那么请考虑点赞。

你可以在 [github](https://github.com/rahul-yr/learn-go-concepts.git) 找到完整的代码

# 分级编码

感谢您成为我们社区的一员！更多内容见[级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在改造理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)