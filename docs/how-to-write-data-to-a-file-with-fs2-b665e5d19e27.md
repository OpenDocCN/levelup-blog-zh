# 如何使用 FS2 将数据写入文件

> 原文：<https://levelup.gitconnected.com/how-to-write-data-to-a-file-with-fs2-b665e5d19e27>

![](img/9de5a6fc9299500beb354e38335f0c2c.png)

上周，我试图在工作中调查死信队列(DLQ)的原始数据。我想做的功能之一是从 SQS DLQ 民意调查的来源，并将这些数据写入一个文件供进一步调查。我认为使用 FS2 将是一个很好的用例。

FS2 是一个轻量级的流库，可以帮助您完成许多数据处理功能。通常，我们希望从上游获取一些数据，并将其写入文件中，以便进行任何调查。

我想分享我们如何创建一个简单的[类型类](https://edward-huang.com/functional-programming/2020/01/02/wtf-is-a-type-class/)来用 FS2 将数据写入文件系统。

我们将通过向文件写入任何值的最简单的用例来分解它。然后，我们将探讨如何合并队列来减少写入文件的反压力。

让我们从定义我们的函数开始。

在 fs2 [指南](https://fs2.io/guide.html)中，已经有一个我们希望如何写入外部文件系统的示例。

代码指定我们将从文件中读取所有内容，并对某个函数`fahrenheitToCelsion`进行过滤。然后，将其编码成一个字节，写入`testdata/celsius.txt`。

`through`将一个流合并到另一个流中，`text.utf8Encode`返回一个`Pipe[F[_], I+, O-]`，相当于`Stream[F,I] => Stream[F,O]`。

因此，我们得到了我们的第一个问题的答案！

我们可以编写初始函数，接收一个上游数据，并将其写入文件。让我们创建`toFile(fileName:String, upstream:Stream[IO, String]): Stream[IO, Unit]`:

我们需要一个阻塞器`writeAll`，因为它是一个会`block`线程的操作。因此，cats-effect 提供了一个专用的线程池`Blocker[IO]`来显式处理阻塞[操作](https://typelevel.org/cats-effect/concurrency/basics.html#blocking-threads)。

到目前为止看起来不错。然而，我们可以将`toFile`作为`Pipe`，因为它需要`upstream:Stream[IO,String]`作为依赖项。让我们重构我们的`toFile`来返回一个`Pipe`:

然后，我们可以像这样运行这个操作:

现在我们已经有了写文件的基本操作，让我们通过使用队列来减轻写文件的反压力。

本质上，调用者想要调用`write(item: String)`，这个函数将处理把这些项目写到一个文件中。让我们从定义函数参数开始:

我们想创建一种方法，当每次调用者调用`write(item)`时，它将同时将项目写入一个队列，并且将有另一个线程同时轮询该队列中的值并将这些值写入文件。

我们如何创建内部队列呢？

幸运的是，FS2 有一个并发包，它实现了一个队列来帮助创建一个内部队列。

实现队列时有两个部分——入队和出队。

根据您的应用程序，有时您希望将其中一部分留给调用者来完全控制它。

在这个场景中，我们将把`enqueue`封装成调用方的`write`，并通过写入文件的值来实现出列。

# 从 FS2 队列中出列

让我们先实现我们的`dequeue`方法。下面的代码片段相当于我们之前创建的队列和管道流的功能。

我们创建一个`Queue[IO, Option[Either[Throwable, String]]]`的队列。

然后，我们通过类型为`None`的`enqueue1`创建一个资源，如果没有更多来自上游的值，该资源将关闭队列。如果你不使用`NoneTerminatedQueue`，这通常是[的变通方法](http://www.aimplicits.com/posts/2018-05-09-fs2-queue-stop/)。

`Stream.bracket`内的值为下游。如果它得到一个`None`，它将终止，这意味着我们在队列中没有剩余的数量。

`write(item:String)`将`item`排入内部`Queue`:

最后，我们可以将入队和出队连接在一起，方法是将出队生成到另一个线程中，同时让调用者访问`WriteToFile`实例。

这里的关键是`start`方法，它将生成一个纤程并在后台运行`constantPoll`。如果移除`start`,操作将按顺序进行。

我们通过让用户调用`WriteToFile.create`来重构我们的函数。用户需要提供队列和文件目的地——如下所示:

因此，我们的`WriteToFile`函数看起来像这样:

然后，我们可以编写一个简单的程序来利用我们的实现，如下所示:

最后，执行程序:

```
program.unsafeRunSync
```

# 结论

首先，我们创建了`toFile`方法，该方法获取一个流块，将其编码成字节码，并将其写入文件。

然后，我们试图围绕`WriteToFile`创建一个包装器，封装所有的复杂性，并在后台使用 FS2 原语`Queue[F,A]`来处理大量数据。呼叫者可以呼叫`write(item: String): F[Unit]`并为他们完成工作。

最后，我们将所有东西连接起来，解开复杂的逻辑，将我们的`WriteToFile`特征连接到`toFile`，并创建了一个库，使用户能够将任何值写入外部文件。

我们利用 FS2 API 用一小段代码编写了一个复杂的逻辑。如果没有 API 的所有组合器，并发创建一个内部队列将会很有挑战性并且容易出错。

所有源代码都是[这里](https://github.com/edwardGunawan/Blog-Tutorial/tree/master/ScalaTutorial/fs2/src/main/scala/WriteToFile)。

**感谢阅读！如果你喜欢这篇文章，请随意订阅我的时事通讯中的**[](https://edward-huang.com/subscribe/)****以获得关于科技职业的文章、有趣的链接和内容的通知！****

**你可以关注我，也可以在 [Medium](https://medium.com/@edwardgunawan880) 上关注我，获取更多类似的帖子。**

***原载于【https://edward-huang.com】[](https://edward-huang.com/fs2/functional-programming/programming/scala/2020/10/11/how-to-write-data-to-a-file-with-fs2/)**。*****