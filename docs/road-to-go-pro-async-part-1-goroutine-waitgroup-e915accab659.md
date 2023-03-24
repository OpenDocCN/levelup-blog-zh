# Pro-Async 之路第 1 部分:Goroutine 和 WaitGroup

> 原文：<https://levelup.gitconnected.com/road-to-go-pro-async-part-1-goroutine-waitgroup-e915accab659>

*开始前的一个小更新。你可能想知道我到底在哪里，为什么我停止更新这个 Road to Go Pro 系列。2021 年，我居住的城市被冠以全世界最锁的城市。我对生活和工作感到紧张。不确定、失望和疲劳是我 2021 年生活的主旋律。简而言之，我既没有好的精神状态，也没有正确的心态去继续这个系列。所以我决定休息一段时间，给自己一些时间让自己振作起来。现在，2022 年已经到来。我要回到中号。我感谢你的支持和耐心。我希望在这一年里，我们将一起踏上一段伟大的旅程。*

*常用的东西:你可以在这个* [*资源库*](https://github.com/songx23/RoadToGoPro) *中找到本教程使用的代码。你可以在这里* *找到 Road to Go Pro* [*的全部内容。如果你错过了最后一个，可以通过这个*](https://medium.com/@songx/road-to-go-pro-f9d1f8a51fad) [*链接*](https://medium.com/digio-australia/road-to-go-pro-unit-test-69591a553412) *找到。好吧，我们开始吧。*

# 异步处理

在这个故事中，我们将探索如何使用 Go 来处理异步处理。Go 拥有出色的开箱即用并发支持。它以处理多个“线程”的高效率而闻名。

由于并发是一个相当大的讨论主题，我们将在两个故事中讨论并发。在这一部分中，我们将了解 goroutines 和等待组。

# 戈鲁廷斯

![](img/560792f459c26c1f7c6fed9b1b9660b0.png)

照片由 [amirali mirhashemian](https://unsplash.com/@amir_v_ali?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

那么什么是线程呢？

> 线程是程序中一个单独的顺序控制流。

到目前为止，我们写的所有东西都是顺序执行的。我们先做 X，再做 y，一个顺序程序在一个线程中运行(在这个故事中我们称之为“主 goroutine”)。Go 对线程使用了一个稍微不同的术语，它被称为“goroutines”而不是“threads”。goroutine 的定义是:

> goroutine 是由 Go 运行时管理的轻量级线程。

为什么 Go 使用了与广泛使用的“线程”不同的名称？好问题，我们将在后面的部分探讨它们的区别。目前，goroutine 只是一个线程的表示。

启动一个 goroutine 非常容易，你需要使用的关键字是`go`。没错，就是`go`。让我们想象一下，我们有一组 6 个困难的数学计算要解决。每个计算需要 10 秒钟来解决。如果我们按顺序处理它们，将需要`6x10=60`秒来完成。如果每个计算都相互独立，我们可以通过使用并发来加快速度。如果我们并行处理这 6 个计算，我们可以将总处理时间缩短到大约 10 秒。下面是数学解题程序的顺序版本和异步版本。

顺序加工

— vs —

异步处理

你发现区别了吗？一看就不是很明显。在第 11 行，我们在调用计算函数之前使用了 go 关键字。这就是如何在单独的 goroutine 中创建和运行函数。就这么简单。在上面的例子中，我们并行运行了 6 个单独的 goroutines。现在，让我们检查控制台中打印出的内容。运行顺序示例后，您可以看到解决方案已排序。另一方面，在运行异步示例后，我们得到了随机排序的解决方案。如果您运行几次异步示例，我们可以看到程序以不同的顺序打印出解决方案。

```
// Sequential output
math problem solved for input: 0.
math problem solved for input: 1.
math problem solved for input: 2.
math problem solved for input: 3.
math problem solved for input: 4.
math problem solved for input: 5.// Asynchronous output
math problem solved for input: 4.
math problem solved for input: 2.
math problem solved for input: 3.
math problem solved for input: 5.
math problem solved for input: 0.
math problem solved for input: 1.
```

## Goroutines 与 Threads

在我们探讨 goroutines 和 threads 之间的区别之前。我想澄清一点:我们这里说的“线程”是操作系统线程。

与 goroutines 相比，OS 线程相对较重。他们有一个大的固定堆栈大小。这就是为什么我们只能在一台计算机上运行有限数量的线程。它们也依赖于硬件。您的 CPU 内核数量将影响您可以使用的操作系统线程数量。然而，goroutines 不是由操作系统管理的，而是由 Go 运行时管理的。它没有操作系统线程所具有的限制。这就是 goroutines 轻量级和快速的原因。Go runtime 为 goroutines 对堆栈进行分段，并根据需要增加它们的大小。此外，Go 运行时执行调度，而不是操作系统，因此它可以将 goroutines 复用到 OS 线程上。这由一个名为`GOMAXPROCS`的运行时变量控制，它的默认值是机器的 CPU 核心数。

Go 还为 goroutines 相互通信提供了另一种处理机制。它被称为“通道”，我们将在下一个故事(第二部分)中了解它。线程之间的通信不是一件容易的事情。因此，对于 OS 线程来说，通信的成本是昂贵的。

在处理异步处理时，我们需要小心竞争条件和死锁。Go 帮助我们做了很多开箱即用的同步，它甚至有工具来帮助检测竞争条件(也将在下一个故事中涉及)。而我们需要在使用 OS 线程时主动避免竞争情况。

这些是线程和 goroutines 之间的主要区别。我用 C#、Java/Kotlin 和 Go 开发过异步处理器。依我拙见，Go 提供了三者中最好的开发体验。

# 等待组

好吧，让我们快速回顾一下。我们对工作负载进行了并行处理，以加快处理速度。顺序示例需要大约 60 秒来运行，异步示例需要大约 10 秒来运行。这是我们的期望，让我们从代码中得到一些准确的读数来证明这些期望是正确的。

为了计算处理时间，我们可以简单地在 for 循环前后添加时间戳。开始和结束时间戳的差值就是处理时间。

有持续时间的顺序加工

在运行这个更新的顺序结果后，我们将得到类似于`Total time spent: 60008 ms`的读数。这与预期相符。让我们应用同样的方法来计算异步示例的处理时间。

带持续时间的异步处理

哎呀，运行程序后，我们得到了一个意外的输出:`Total time spent: 0ms`。那好得令人难以置信。为什么会有这样的结果？

我们在不同的程序中执行计算功能。第 14 行不会等待所有的计算 goroutines 完成，它会在 for 循环完成后立即执行。由于启动 goroutine 的速度很快，所以运行 for 循环的时间不到 0.5 ms(四舍五入到 0 ms)。在异步示例中应用相同的方法不会反映真实的处理时间。我们需要为程序引入一个块来等待所有 goroutines 完成。Waitgroup 正是为此而构建的。

首先，我们需要创建一个等待组。对于您想要等待的每个 goroutine，您需要在启动 goroutine 之前向 waitgroup 添加一个计数器。这是通过调用`Add`函数完成的(第 14 行)。一旦我们在 waitgroup 中注册了所有的 goroutines，我们就可以在任何我们想要的地方添加这个块，在我们的例子中，我们想要在 for 循环完成之后等待。为了告诉 waitgroup 一个 goroutine 已完成，我们只需调用`wg.Done()`来将 goroutine 从 waitgourp 中取出。这个函数是线程安全的，所以您不需要担心竞争条件和死锁。在执行下面的代码之前，`wg.Wait()`调用等待 waitgroup 中的所有 goroutines 被取出。

使用等待组的常见模式是:

*   在主 goroutine 中调用`wg.Add(1)`。
*   在 goroutines 中运行的函数开始时调用`defer wg.Done()`。我们在这里使用`defer`,这样我们不会忘记将 goroutines 从等待组中取出。如果我们错过了`wg.Done()`，`wg.Wait()`的呼叫就会永远挂断。

我以前在使用 waitgroups 时犯了一个错误。如果我们在运行于 goroutines 内部的函数中调用`wg.Add()`，我们可能会再次得到“0 ms”的结果。原因是主 goroutine 可能会在后台 goroutine 有机会向 waitgroup 添加计数器之前完成。因此，当调用`wg.Wait()`时，它不会阻塞程序，因为它看不到在 waitgroup 中注册的任何 goroutines。

等待组常见问题

在 waitgroup 的帮助下，我们让应用程序做了我们想做的事情。并且并行求解数学计算的处理时间看起来有点像`Total time spent: 10006ms`。这证明我们的预期是正确的。

# 下一步是什么？

![](img/f3575f48da5c2dd5cfac44aa0d78e564.png)

这是 2022 年走 Pro 的第一段路。

我们开始了解 goroutines 和 waitgroups 的基本知识。在下一篇文章中，我们将探讨通道、锁和竞争条件检测。敬请关注。第 2 部分将带给您更多关于 goroutines 的激动人心的知识。

如果你遇到任何问题或者需要帮助，请在下面留下你的评论。随时欢迎反馈。感谢您的阅读！

如果你想支持我，你可以通过这个推荐链接成为一个中等会员。谢谢你。

[](https://songx.medium.com/membership) [## 通过我的推荐链接-宋雪加入媒体

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

songx.medium.com](https://songx.medium.com/membership)