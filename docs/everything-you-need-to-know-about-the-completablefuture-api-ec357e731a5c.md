# 关于 CompletableFuture API 您需要知道的一切

> 原文：<https://levelup.gitconnected.com/everything-you-need-to-know-about-the-completablefuture-api-ec357e731a5c>

## 使 Java 中的异步编程令人兴奋的 API！

![](img/174180f0755d22c25ca244282910036e.png)

照片由[普里西拉·杜·普里兹](https://unsplash.com/@priscilladupreez?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/happy-with-laptop?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

有几次，我被要求开发高性能的应用程序。事实上，最常见的问题之一是如何提高应用程序的性能。

提高应用程序性能的最佳方法之一是编写异步编程，这种编程可以导致多个计算并行进行。

虽然早在 2004 年发布 Java 5 时，Java 就已经引入了`**Future**`接口来支持异步编程，但是使用 Future 有一些缺点，这使得它在现实生活场景中的使用不太理想。

# 未来界面的局限性

1.  *期货*不能手动完成。
2.  无法并行执行多个*期货(*或结果 *)* 然后将结果组合在一起。
3.  没有针对*未来*的异常处理构造。
4.  *未来*没有机制来创建可以链接在一起的多个处理阶段。需要手动完成。
5.  *Future* 没有通知你一个 API 完成的机制。

幸运的是，随着 Java 8 的发布，`**CompletableFuture**`解决了上述所有问题，并提供了一种更好的 Java 异步编程方法。

# 那么，CompletableFuture 有什么特别之处呢？

异步编程就是通过在单独的线程而不是主应用程序线程上运行所有任务来编写非阻塞代码，并让主线程了解进度、完成状态或任务是否失败。

Java 中的`**CompletableFuture**` API 支持异步编程。它实现了[](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Future.html)*和[*completion stage*](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletionStage.html)接口。*

# *CompletableFuture API 的优势*

1.  *如果远程 API 服务在使用时被关闭，您可以手动完成将来的操作来检索数据。*
2.  **CompletableFuture* API 允许链接多个 API，从而允许您创建异步工作流。*
3.  *它提供了异常处理机制。*
4.  *它提供了将多个期货组合成单个 *CompletableFuture* 的机制。*
5.  *它允许在响应可用时调用 API 的回调函数。*

# *创造一个完美的未来*

*CompletableFuture 最简单的例子如下所示:*

*使用无参数构造函数，可以创建尽可能简单的 CompletableFuture。*

```
***CompletableFuture<String> completableFuture = new 
CompletableFuture<String>();***
```

*为了得到 CompletableFuture 的结果，调用了`**CompletableFuture.get()**`。这个 get()方法一直阻塞到未来完成。*

*因此，为了解决这个问题，`**CompletableFuture.complete()**`可以用来手动完成未来。*

```
***CompletableFuture.complete("some dummy data from Future")***
```

*等待这个 Future 的所有客户机将收到指定的结果，对上述方法的后续调用将被忽略。*

*CompletableFuture 有 50 多种不同的方法来组成、组合和执行异步计算步骤以及处理错误。*

*这里我们将介绍一些我在项目中广泛使用的最常见的方法。*

# *运行异步任务的 CompletableFuture*

*运行异步任务主要有两种静态方法。*

## *运行异步()*

****如果你想异步运行一些后台任务，又不想从任务中返回任何东西，那么使用*** `**CompletableFuture.runAsync()**`。*

*由于这个静态方法接受一个 [*Runnable*](https://docs.oracle.com/javase/7/docs/api/java/lang/Runnable.html) 对象并且不返回值，所以它返回*CompletableFuture<Void>*。重载版本也接受 Executor 作为第二个参数。*

1.  **completablefuture . run async(Runnable)**
2.  **completablefuture . run async(Runnable，Executor)**

*在获取线程名称的时候，你会注意到 CompletableFuture 在一个从全局[*forkjoinpool . common pool()*](http://ForkJoinPool.commonPool())获得的线程中执行了这个任务。*

## *供应同步()*

****如果你想*** ***异步运行一些后台任务，想从那个任务返回什么，那么就用*** `**CompletableFuture.supplyAsync()**` **。***

*它接受一个 [*供应商< T >*](https://docs.oracle.com/javase/8/docs/api/java/util/function/Supplier.html) 并返回一个*CompletableFuture<T>*其中 *T* 是通过调用给定供应商获得的值的类型。它还有把*执行器*作为第二个参数的版本。*

1.  **completablefuture . supply sync(供应商< T > )**
2.  **completablefuture . supply sync(供应商< T >，执行者)**

*这里你会注意到线程的名字是 *pool-1-thread-1* 而不是[*forkjoinpool . common pool*](http://ForkJoinPool.commonPool())*。这是因为我们使用 Executor 创建了一个线程池，这样任务就可以从我们自己的线程池中执行。**

*CompletableFuture API 中的所有方法都有两个版本——一个有 Executor 作为第二个参数，另一个没有。*

# *对结果进行转换和处理*

*所以`**CompletableFuture.get()**`方法是阻塞的，它一直等到未来完成后才返回完成后的结果。*

*接下来呢？您希望结果被转换或进一步处理，对吗？*

*你可以在回调函数中添加更多的逻辑。事实上，要构建异步应用程序，您需要添加一个回调，当异步计算完成时，该回调将被自动调用。*

*在 CompletableFuture 中添加回调函数的好处是，我们不必等待结果—只需在回调函数中添加逻辑，它就会自动执行。*

*主要有三种方法来附加回调。*

## *然后应用()*

****如果要处理和转换 CompletableFuture 的结果，那么使用*** `**thenApply()**` ***。****

*它以一个 [*函数< T，R >*](https://docs.oracle.com/javase/8/docs/api/java/util/function/Function.html) 作为自变量。*函数< T，R >* 是一个简单的函数接口，表示一个接受类型 *T* 的参数并产生类型 *R* 的结果的函数。*

*`**thenApply()**`也可以用来将一系列的变换串联起来，一个接一个地执行。*

## *然后接受()*

****如果你不想从回调函数中返回任何东西，只想在未来完成后执行一些代码，那么使用*** `**thenAccept()**`。*

*`***CompletableFuture.thenAccept()***`接受一个 [*消费者< T >*](https://docs.oracle.com/javase/8/docs/api/java/util/function/Consumer.html) 并返回一个*CompletableFuture<Void>*。它能接触到它所依附的未来的结果。*

## *然后运行()*

**类似于* `**thenAccept()**` ***，如果你不想从回调函数中返回任何东西，只想执行一些未来完成后的代码，那么就使用*** `**thenRun()**`。*

*然而，`**thenAccept()**`可以访问它所附属的 CompletableFuture 的结果，而`**thenRun()**`却连未来的结果都不能访问。它将一个 *Runnable* 作为参数，并返回一个*CompletableFuture<Void>*。*

****由于*** `**thenAccept()**` ***和*** `**thenRun()**` ***都是消费者，它们往往被用作回调链中的最后一个回调。****

*CompletableFuture 提供的所有回调方法都有两个异步变量，可以帮助并行执行计算。*

```
***thenApply(Function<? super T,? extends U> fn)****thenApplyAsync(Function<? super T,? extends U> fn)****thenApplyAsync(Function<? super T,? extends U> fn, Executor executor)***
```

# *组合 CompletableFutures 的结果*

*Java 8 的发布带来的最好的事情之一是函数式编程的引入。它遵循*单子*设计模式。*

> *“Monad 是一种软件设计模式，其结构将程序片段(函数)组合在一起，并将它们的返回值包装在一个具有额外计算的类型中。”—维基百科*

*CompletableFuture API 使您能够在一系列计算步骤中组合 CompletableFuture 实例。这种链接的结果是一个 CompletableFuture，可以进一步链接和组合。*

## *然后撰写()*

****如果你想从一个远程 API 服务获取数据，并使用这些数据，你想从另一个 API 获取一些其他数据，你应该使用*** `**thenCompose()**` ***。****

**thenCompose()* 方法接收一个函数，该函数返回同类型的另一个对象。*

## ***然后合并()***

****如果你想将两个独立运行的期货组合起来，然后在两个期货都完成时对结果进行操作，其中一个期货的结果依赖于另一个，那么你应该使用*** `**thenCombine()**` **。***

# *异常处理*

*在一个完美的世界里，一切都会按照计划运行，不会有任何问题、错误或异常。嗯，那只是假设。*

*在软件开发中，异常可能并且将会发生。因此，处理它们是很重要的，否则它会破坏我们的系统。幸运的是，CompletableFuture API 中有异常处理机制。*

## *异常()*

****如果要记录并返回未来发生的异常的默认值，那么使用*** `**exceptionally()**`。*

*还有其他方法，如`**handle()**`和`**completeExceptionally()**`，用于不同场景的异常处理。*

# *结论*

*如前所述，CompletableFuture API 中有 50 种方法可以满足不同的用例，探究每一种方法将会使本文变得冗长。*

*最近几年我一直在研究异步系统。如果你是第一次学习，你肯定会觉得它令人望而生畏。然而，你会开始喜欢这种编程方法，因为随着时间的推移，它变得越来越令人兴奋。*

*如果你喜欢读这篇文章，你可能也会发现下面的文章值得你花时间去读。*

*[](https://medium.com/illumination/this-ai-generator-creates-viral-motivational-but-cringy-linkedin-posts-85ffd4902a45) [## 这个人工智能生成器网站创建了病毒式的、激励性的、但令人生厌的 LinkedIn 帖子

### LinkedIn 上无人问津的成功病毒工具。

medium.com](https://medium.com/illumination/this-ai-generator-creates-viral-motivational-but-cringy-linkedin-posts-85ffd4902a45) [](/9-incredible-websites-that-every-developer-should-bookmark-1534d52f3f7d) [## 每个开发者都应该收藏的 9 个不可思议的网站

### 这些网站不仅会帮助你的软件开发之旅，还会帮助你的内容…

levelup.gitconnected.com](/9-incredible-websites-that-every-developer-should-bookmark-1534d52f3f7d) 

如果你喜欢阅读有助于你更好地学习、生活和工作的故事，可以考虑 [*成为*](https://viveknaskar.medium.com/subscribe) *的订阅者。成为会员后，你可以无限制地阅读 10000 篇故事、文章和作家。每月只要 5 美元。* [*如果你使用我的链接*](https://viveknaskar.medium.com/membership) *注册，我将获得一点佣金，帮助我写更多的文章。*

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**将像你这样的开发人员安置在顶级创业公司和科技公司**](https://jobs.levelup.dev/talent/welcome?referral=true)*