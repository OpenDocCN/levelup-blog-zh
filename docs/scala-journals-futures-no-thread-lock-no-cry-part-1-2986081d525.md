# Scala 期刊——未来:没有线程锁就没有哭泣(第 1 部分)

> 原文：<https://levelup.gitconnected.com/scala-journals-futures-no-thread-lock-no-cry-part-1-2986081d525>

欢迎阅读关于 Scala 中并发和非阻塞代码基础的一系列文章。鉴于这个话题的广泛性，我需要把它分成几个部分。我将主要关注`Future`,因为它代表了实现并发性的最基本的构件。我很快会谈到高级主题，但今天我们将从非常简单的开始:我将重点关注阻塞与非阻塞的基础知识以及`Future`的基础知识。

![](img/c6d5f57747e5acf90fd8c407b10548ce.png)

来源: [Unsplash](https://images.unsplash.com/photo-1586864387634-2f33030dab41?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1650&q=80)

# 屏蔽还是不屏蔽

首先让我们谈谈阻塞和非阻塞。简单地说，当你的代码强迫程序暂停直到它返回时，你的代码就是阻塞的，因此它阻塞了下一个代码的执行。如果您所做的只是将整数相加，这不是一个大问题，但是请考虑您有两个非常长的计算，可能是一些网络或数据库分析调用，如下所示:

```
val firstComputation = 2 // ...this takes 1000 ms to complete
val secondComputation = 4 // ...and this takes 3000 ms to completeval completelyUnrelatedThing = sendEmail() // 2 ms
```

在上面的伪代码中`completelyUnrelatedThing`只需要 2 毫秒，即使它根本不依赖于`firstComputation`或`secondComputation`，也会在至少 4000 毫秒后执行。所以简单来说，我们的总执行时间是`4002 ms`。

如果你决定改变并编写你的代码`non-blocking`，你基本上要做的是允许`firstComputation`、`secondComputation`和`completelyUnrelatedThing`并行运行。这可能意味着，如果我们上面的三个计算在三个线程上运行，这意味着我们可以将响应时间减少到`3000ms`(再次简化)。您也没有阻塞线程，这意味着您耗尽内存/线程的可能性要小得多。

# 介绍…期货！

让我们从 Scala 文档中的一个定义开始:

> *“期货代表一个当前可用或不可用的值，但在某个时间点将可用，或者如果该值不可用，则为例外。”*

换句话说，一个`Future`代表一个在**未来**可用的值，因此得名。如果有人告诉你他们会在未来的某个时候给你写信，那么有些事情是你不能/不应该做的，例如:

*   在他们写完信并寄出之前，你不能查看这封信(让我们也暗示“在它真正到达之前”)
*   你不应该停下你的生活，只是盯着邮箱，直到邮递员送来信

把那想象成`Future[Letter]`。现在把它想象成`Future[Int]` …

当我第一次遇到`Futures`时，这让我很困惑——如果一个方法返回`Future[Int]`,我需要得到那个`Int`来用它计算一些东西，我该如何访问它呢？这取决于你如何得到那个`Int`你要么阻止要么不阻止，换句话说:你要么以错误的方式，要么以正确的方式使用期货。让我们涵盖一些非常基础的内容:

*   `Future`有两种可能的结果:一个`Success`或者一个`Failure`。第一个保存计算值。后者通常与异常相关，它包含计算遇到的问题。
*   一个`Future`类型包装了另一个将来会出现的类型。你不只是与`Future`单独合作。`Future`需要一个类型参数，它将在某个阶段返回什么。比如:
    `Future[Int]`:未来某个阶段会有一个 Int 返回。
    `Future[String]`:未来某个阶段会返回一个字符串
    `Future[List[Customer]]`:未来某个阶段会返回一个客户列表
*   `Futures`内部使用`Promises`(下面再多讲一点`Promises`)
*   `Future`定义时开始执行。

```
val result: Future[Result] = getResult() // already executing!
```

*   `Future` **不应该被**挡住。这意味着**除非**你需要保持主线程活动**或者**你正在编写测试代码，否则在任何情况下你都不应该调用`Await`来“获取值”:

这不好，你挡住了:

```
Await.ready(getPrice(product), 4 seconds) 
// you are actively waiting, in other words: staring at the letter box if the postman is there yet!
```

这是很好的，非常经典的用法。您没有阻止:

```
// whenever getPrice returns then with the returned value we will call "getSimilarProducts" 
getPrice(product).flatMap(price => getSimilarProducts(price))
```

在`flatMap`上，记住:任何`flatMap` / `map`的链也可以被翻译成[以便理解](http://for comprehension)，如果你的转换链很长的话，这会使你的代码可读性更好。

如果您需要显式错误处理，这也很好，因为您没有阻塞:

```
getPrice(product) match {
  case Success(price)   => continue(price)
  case Failure(problem) => handleProblem(problem) 
}
```

回调，你没有阻挡。但是要注意，`onComplete`会丢弃返回值，返回`Unit`。在使用回调之前，请检查它们的返回类型！

```
getPrice(product).onComplete {
  case Success(price)   => continue(price)
  case Failure(problem) => handleProblem(problem) 
} // return Unit
```

点击了解更多关于`Futures`和[的信息。](https://www.scala-lang.org/api/current/scala/concurrent/Future.html)

# 我也听说过承诺，它像未来吗？

编号`Promise`由`Future`在内部使用。每次你创建一个`Future`你就创建了一个`Promise`。一个`Future`没有一个`Promise`就无法工作，但是当我们每天和`Futures`一起工作时，这一点对我们来说几乎是隐藏的。A `Promise`是`Future`的返回值的实际来源。简而言之:`Promise`被写入，但实际读取的是`Future`。有少量非常专业的用例，我们希望获得如此低的控制级别，以便直接使用`Promises`。然而，由于这是足够罕见的，我决定现在跳过进入细节。将来我可能会在另一篇文章中讨论这个话题(没有双关语的意思)。

# 接下来…

在下一篇文章中，我将深入探讨什么是`ExecutionContext`，为什么当你想使用`Futures`时总是需要它，以及如何选择一个正确的。我还会谈到为什么我们有时会听到 Scala 在并发性方面比其他语言更好。

敬请期待！