# Scala journals——正确处理并发:无线程锁无哭泣(第 2 部分)

> 原文：<https://levelup.gitconnected.com/scala-journals-do-your-concurrency-right-no-thread-lock-no-cry-part-2-fa4260737c9b>

这是前面[的延续，基本介绍](/scala-journals-futures-no-thread-lock-no-cry-part-1-2986081d525)。

今天我们将继续讨论 Scala 的并发编程。我将介绍实现并发性的主要方法，解释好的和坏的实践，以及一些真实的例子。然后，我将更多地谈论我在前一篇文章中承诺的`ExecutionContext`。

我们走吧！

顺便说一下，如果你还记得我在 Medium 上的第一篇文章什么是函数式编程，你会记得不变性非常重要。剧透警告:今天我们将看到一点半实际的一面，为什么。

![](img/5ed48ede71d553335f8392edb02963ef.png)

来源: [Unsplash](https://unsplash.com/photos/ESkw2ayO2As)

# 如何在 Scala 中实现并发？

让我们从主要故障开始。有几种方法可以在 Scala 中实现并发/异步计算——我将介绍最常见的几种*(目前，不管它们通常被认为是“好”还是“坏”的实践)*:

1.  **直接使用 JVM 并发 API**，也就是说:生成线程，使用`atomic`、`volatile`变量，锁定线程，使用`synchronised`语句。

**使用 Futures and Promises API** ，也就是说:使用例如`scala.concurrent.**Future**` (或者其他未来例如 Twitter 的未来)。`Future[T]`总体上封装了一个轻量级的、一次性的计算，在某个线程上运行。除了指定线程池(`Execution Context`)之外，您不必处理任何与并发性相关的事情。这意味着:您不需要处理上面为 JVM 并发 API 列出的任何技术。

1.  **使用 Scala 的并行集合**，这意味着:使用来自`scala.collection.parallel.immutable`的数据结构。这些集合上的所有转换(提醒:我说的“转换”是指例如`.map`、`.fold`)将默认并行执行。所有的实现都是抽象的。有多牛逼？！您还可以使用`.par`将非并行集合转换为并行集合，比如:`List(1,2,3,4).par`
2.  **使用 Actor 模型，**(Scala Actors 或其他实现，如 Akka Actors)。我邀请你在空闲时间阅读`Actor Model` [这里的](https://doc.akka.io/docs/akka/2.0.5/scala/actors.html#:~:text=The%20Actor%20Model%20provides%20a,correct%20concurrent%20and%20parallel%20systems.)。为了不使文章太长，我将在这里跳过细节。我会在某个阶段单独补充一篇关于`Actor Model`的文章。目前从`Actor Model`得到的主要启示应该是:参与者是接收、处理和发送信息的实体。演员的内心状态除了自己，谁也改变不了。
    也是最重要的:`Actor Model`是一个**系统设计选择**，**不是**只是一个类似于`thread`或`Future`的特设机制。仔细想想，演员和他们的交流实际上与我们人类很相似:你可以让你的朋友告诉你他们认为世界上最美丽的地方是什么(发送信息)，但你无法进入他们的大脑并亲自检查(改变演员的内部状态)。他们将有希望听到你(收到一条信息)，思考它(处理一条信息)，然后回应你的问题(发送一条回应信息)。然后你可以听到答案(收到一条信息)，思考它(处理一条信息)，然后…你知道我在说什么了吧！

# 那么我应该使用哪种并发方法呢？

这取决于你想做什么。你可以全部使用。让我们做和上面一样的列表，这次用用例。

1.  **直接使用 JVM 并发 API:** 有问题的选择和警告灯闪烁。在 JVM 并发 API 上操作是低级的，并且放弃了 Scala 提供的抽象的所有好处。它违背了函数式(因此也违背了声明式)编程的精神，因为它强迫你描述如何做事情(例如启动线程、递增`list A`中的所有数字、加入线程)，而不是描述做什么 ***** (例如映射到`list A`并递增`list A`中的所有数字)
    ，所以除非你正在编写自己的并发 API 库(在这种情况下，你不会阅读本文)**不要直接使用 JVM 并发 API！**
2.  **使用 Futures and Promises API:** 把`Future[T]`想象成一个抽象，它说:在你得到`T`之前会有一些延迟。如果你只是包装一个类似于`4 + 3`的计算，这可能接近于`0 ms`，但是如果你在世界的另一端调用一个过载的服务器，这也可能是`5000 ms`。
    对于可能需要一段时间的特定异步计算，使用`Future[T]`是一个很好的选择。用例:HTTP 调用，数据库读/写，通常是任何可能失败的长时间运行的计算。
    `Future[T]`将在**上运行一些**线程，它们不受你的控制，所以它需要一个线程池:这正是`Execution Context`所需要的！这是一个非常酷的机制，我将在下面详细描述。
    `Future[T]`在 Scala 代码中大量使用**并且大多数 Scala HTTP/db/计算繁重的库确实会返回一个`Future[T]`！**
3.  ****使用 Scala 的并行集合:** 处理这些集合会默认拆分成可用的线程。当你有一个**大规模的**实体集合，并且需要对每个实体进行长时间的计算时，使用这个。
    注意！这当然可以通过在非并行集合上执行`.map`来实现，并且在`Futures.` `Futures`中包装长时间的计算给你带来的好处是一旦计算完就可以立即访问每个结果。这使得短路和故障快速实现更加容易。另一方面，对于并行集合，您必须等待所有计算完成才能访问结果。
    但是记住！如果您使用并行集合，请注意！**它们确实是并行的**，所以如果你需要在数据结构中保持有序，你会很快遇到很多麻烦！**
4.  ****使用 Actor 模型:** 设计选择，这样你就不会像处理期货或平行系列那样，真的“选择”在日常工作中使用它。这是在设计新系统或考虑大规模重构时要考虑的事情——如果您的系统将有许多共享状态，将需要是健壮的，如果您能容易地识别您的系统将操作的消息/状态的类型，例如`OrderPlaced`、`OrderFailed`、`PaymentSuccessful`、`OrderShipped`，并且您能容易地想到像`EmailService`这样的组件，它们对不同的状态变化/消息有不同的反应，例如在`OrderFailed`给技术支持发送电子邮件，在`OrderShipped`给客户发送电子邮件——那么您就有一些很好的用例来实现用 actors 建模的系统**

# **虽然到目前为止还没有提到不变性…**

**好了，说吧:**

**如果你在一个`Future`中包装了一个对不可变对象进行操作的纯函数，那么被选择来计算最终值的线程将不必竞争对任何对象的写访问，因为它不会改变任何东西。所以不需要担心同步线程访问对象、锁定、同步等等。嘣！**

# **如约而至:未来执行上下文的细节**

**`Futures`包装将在某个阶段在某个线程上执行的计算。但这不会自动发生。**

**哪里有线程，哪里就必须有一些协调。JVM 线程映射到直接使用操作系统资源的系统级线程。启动一个线程意味着我们需要为它的调用堆栈分配内存，并从当前线程到新线程进行上下文切换。哇，这听起来已经很麻烦了。所有这些都不会神奇地发生，所以即使对于高科技`Futures`，也必须有一些协调机制…**

****ExecutionContext 宝贝！****

**在我们的应用程序中，我们可能有数百个`Futures`,这意味着我们需要某种机制来协调它们。
如果我们仔细看看`Future`的内部工作方式，我们会发现它实际上是一个`Runnable`(重述:`Runnable`在 JVM 世界中是一个可以在线程上执行的任务)。在运行`Future`之前，需要回答一个非常重要的问题:我应该在哪个线程上运行，一旦我返回，我的回调函数应该在哪个线程上运行？Scala 并发框架通过为我们提供一个`ExecutionContext`来帮助回答这个问题。
`ExecutionContext`是`Futures`如此优秀且易于合作的核心。简而言之，这个类为`Futures`提供了线程池，并在线程返回时协调分配和释放线程(类似于 Java 的`Executor`)。`ExecutionContext`是`Futures`所必需的——否则他们不知道从哪里获取线程来运行。
顺便提一下，当你仔细想想，`ExecutionContext`除了为`Futures`提供他们需要的上下文之外，还做了一些非常有用的事情。它实际上封装了所有讨厌的部分，让程序员专注于做什么而不是怎么做。你实例化`ExecutionContext`，当你坐下来享受返回`Future[Int]`的时候，它会照看任何与线程相关的东西。**

## **执行上下文的类型**

**有几种类型的`ExecutionContexts`,换句话说，有几种不同的方法来说明我们希望如何协调与线程的工作，也就是我们将使用哪个线程池。**

**1.**全局执行上下文** —最容易设置。**

**你唯一需要的是进口货。尽管对于企业应用程序来说，这不是最好的选择。全局执行上下文使用机器上 CPU 的数量作为工作线程的数量。这意味着底层线程池会根据您是在机器上运行代码还是在服务器上运行代码而变化，这可能会导致问题。使用方法:导入即可！**

**`import scala.concurrent.ExecutionContext.Implicits.global`**

**2.**基于 ForkJoinPool 的执行上下文** —“窃取工作”方法:**

**池中的所有线程都在竞争查找和执行提交给池的任务。分而治之的任务的最佳选择(不可变的、重递归的代码就是一个最好的例子)。`initialParallelism`定义了工人的数量。如何实例化:**

```
implicit val executionContext = ExecutionContext.fromExecutor(
 new java.util.concurrent.ForkJoinPool(initialParallelism)
)
```

**3.**fixed thread pool based execution context**:
使用一个线程池，该线程池重用指定(固定)数量的线程，并在其上执行任意数量的任务。如果提交了新任务，并且所有线程都很忙，那么这些任务将不得不在队列中等待，直到有一个线程可用。这是大多数真实场景的最佳选择。如何实例化:**

```
implicit val executionContext = new ExecutionContext {
 val threadPool = Executors.newFixedThreadPool(100)
}
```

**4.**CachedThreadPool based execution context:**
使用一个线程池，该线程池在必要时创建新线程，但会在旧线程可用时尝试重用它们。对于长时间运行任务的应用程序来说，这不是最好的选择，因为如果线程数量达到极限，它可能会破坏系统。如何实例化:**

```
implicit val executionContext = new ExecutionContext {
 val threadPool = Executors.newCachedThreadPool()
}
```

# **摘要**

**好吧，今天的旅程真不错！总结一下:**

*   **Scala 中实现并发的方法有很多:**避免** **JVM 并发 API** :级别太低，太容易出错。使用`Futures`代替轻量级的特别计算。**
*   **`ExecutionContext`很酷！它帮助`Futures`处理哪个线程以及何时运行它们。它抽象了所有与线程相关的处理。你只需要实例化`ExecutionContext`，给它一个线程池，你就可以使用`Futures`了。没有 ExecutionContext，未来就无法存在。**

**我希望你喜欢这篇文章！对于接下来的主题，我已经有了一些想法，但是如果你喜欢我的解释，并且有一些你不确定的东西——请留言，我可能很快会写下来！**

***)好玩的事实！由于 Scala 中的声明式编程鼓励方法链接，有时人们将这种风格称为“*一元风格*”。这是因为当你使用单子的时候，你的代码主要操作链式转换。但是要小心这个！根据你实际使用的 Scala 类型以及你如何处理 IO 和杂质，你的代码可能确实是一元的……或者只是表面上看起来像。因此，如果你还不确定单子是什么，暂时称它为“*声明性*可能是有意义的。**

**参考资料:
[学习 Scala 中的并发编程—第二版](https://www.goodreads.com/book/show/34388188-learning-concurrent-programming-in-scala---second-edition)作者 Aleksandar Prokopec。必读！**