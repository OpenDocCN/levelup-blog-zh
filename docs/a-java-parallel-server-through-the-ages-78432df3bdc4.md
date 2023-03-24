# 一个千古流传的 Java 并行服务器

> 原文：<https://levelup.gitconnected.com/a-java-parallel-server-through-the-ages-78432df3bdc4>

## 从线程到线程池再到未来再到 CompletableFuture

![](img/9a8edfacf27d1da7e1222c1ca070fc1e.png)

ID 111231236 BawegPhotos | Dreamstime.com

**注:**本文改编自我的书 [*函数式与并发编程:核心概念与特性*](https://www.fcpbook.org) ，由 [Addison-Wesley](https://www.informit.com/store/functional-and-concurrent-programming-core-concepts-9780137466573) 出版。具体参见第 26 章*函数式并发编程*。

# 背景

用 Java 编写了一个服务器:

服务器接受一个传入的连接(第 4 行)，从一个套接字读取一个请求(第 5 行)，执行三个任务(第 6–8 行)，汇总这些任务的结果(第 9 行)并回复客户端(第 10 行)。

这个服务器完全是顺序的:一次处理一个请求，并且每个请求都是顺序处理的。本页的主题是此服务器的并行化，使用不同的编程风格。

# 用传统的方法创建线程

通过为每个请求创建和启动一个新线程，可以并行处理独立的请求:

请求处理代码被移到一个单独的方法中。对于每个传入的连接，都会创建一个线程并开始执行该方法。(注意，这个方法是由多个线程进入的，如果请求不是独立的，需要小心对共享数据的并发访问；这不是本帖的重点。)

每个请求的处理仍然是顺序的，但是可以并行处理多个请求。我们还改进了服务器的健壮性:如果在处理请求时出错，相应的线程会终止，但其他请求(当前的和未来的)仍然可以被处理。

# 使用线程池

先前方法的一个缺点是它可能会创建大量线程。这是不希望的，因为线程创建(和拆除)是有成本的，并且太多线程同时运行会影响服务器的性能。相反，*线程池*可用于限制线程数量*和*重用现有线程来处理请求:

对代码所需的修改是最小的；特别是，方法`handleRequest`根本没有改变。所有的改变就是用`exec.execute(() -> handleRequest(socket))`代替`new Thread(() -> handleRequest(socket)).start()`。这里，`exec`被选作 16 个工作线程的池。然后，服务器可以并行处理多达 16 个请求。额外的请求将在`ExecutorService`中排队，并在完成之前的任务后被工作线程获得。线程池可以根据使用的服务和硬件进行定制:最小和最大线程数、线程终止前的空闲时间、队列大小和类型(FIFO、优先级)等。考虑到使用线程池是多么容易，没有理由坚持按需创建线程的风格。本文剩余的所有代码都将使用线程池。

# 在请求中添加并发性

在前面的实现中，请求是并行处理的，但是每个请求是在一个线程中顺序处理的。假设请求任务`A`、`B`和`C`是独立且耗时的。为了提高性能，可以在每个请求中并行处理它们。这可以通过在请求处理线程中创建三个线程来完成，即 *fork* 阶段，然后等待它们完成结果聚合，即 *join* 阶段。这种 *fork/join* 模式(有时也称为 *scatter/gather* )也可以使用线程池来实现:

方法`serve`不变。在方法`handleRequest`中，对`taskA`、`taskB`和`taskC`的调用是由线程池中的工作线程并行执行的。方法`submit`返回一个类型为`Future`的对象，该对象可用于检索每个任务的输出。方法`Future.get`阻塞调用线程，直到任务完成。然后，它返回任务产生的值，或者在任务失败时抛出异常。对`get`的三次调用共同实现了 *fork-join* 的 *join* 部分(对`submit`的调用实现了 *fork* 部分)。我们现在有了一个服务器，可以并行处理请求*和请求的*部分，所有这些都在同一个线程池中。

# 最小化阻塞

上面的代码使用池中的一个工作线程来等待其他工作线程的结果(通过调用`get`)。这是不希望的，至少有两个原因:

*   *工作线程利用不足*:假设一个线程池有 6 个工作线程，2 个请求几乎同时到达。假设每个任务`A`、`B`和`C`用 1 秒钟完成，其他计算时间可以忽略不计。我们希望这 6 个工作人员并行处理所有 6 个任务(一个请求中的`A1`、`B1`和`C1`，另一个请求中的`A2`、`B2`和`C2`，并在 1 秒钟后完成这两个请求。相反，每个请求可能需要 2 秒钟才能完成，因为 2 个工作线程被阻塞调用`get`，而只有 4 个工作线程可用于运行 6 个`A/B/C`任务。在更复杂的场景中，甚至可能会出现*死锁*的情况，所有工作线程都在相互等待。
*   *性能*:阻塞需要暂停和唤醒线程，这需要运行时开销。特别是，上下文切换往往会导致缓存未命中，可能会对性能产生严重影响。

由于这些原因，最新的趋势是尽量减少线程阻塞。其思想是，在所有三个任务完成之后，线程可以调度`dataA`、`dataB`和`dataC`的聚合，而不是等待数据。

服务器的非阻塞版本可以编写如下:

在这个实现中，任务通过稍微不同的机制提交到线程池，以便获得类型为`CompletableFuture`而不是`Future`的值。Java 8 中引入了这种更丰富的未来形式。

方法`thenApply/thenApplyAsync`实现了一种形式的*映射*:给定一个`T`类型的未来和一个从`T`到`U`的函数，它们产生一个`U`类型的未来(有时称为原始未来的*延续*)。当类型`T`的值变得可用时(即，当原始 future 完成时)，函数的计算可以开始。当该函数终止时，产生一个类型为`U`的值，并完成第二个未来。函数是运行在与原来的线程相同的线程中，还是运行在池中的新线程中，由后缀`Async`控制。在服务器的情况下:

```
var dataA = request.thenApplyAsync(Process::taskA);
```

获取一个`CompletableFuture<Request>`(将在从套接字读取请求时完成)并调度对`taskA`的调用以产生一个`CompletableFuture<Adata>`(将在构建请求*并对其应用*函数`taskA`后完成)。该调用是非阻塞的，此时不会调用`taskA`。通过使用`thenApplyAsync`，我们确保三个线程将并行运行`taskA`、`taskB`和`taskC`。(更准确地说，所有三个任务将被并发提交给线程池。只有当池中有足够的线程运行它们时，它们才会并行运行。)

聚合多个期货稍微复杂一点。给定一个`T`类型的未来和一个从`T`到 `*U*`类型的*未来的函数，方法`thenCompose`通过将该函数应用于第一个未来产生的值来产生一个`U`类型的未来。注意，使用`thenApply`将会产生一个* `*U*`类型的*未来。方法`thenCompose`在函数式语言中通常被称为`flatMap`(它将未来的未来简化为简单的未来)。*

变量`response`的类型为`CompletableFuture<Response>`，方法`thenAccept`用于调度回调以将响应写入套接字。(`thenApply`和`thenAccept`的区别在于`thenAccept`接受`void`类型的函数，产生一个没有有用值的未来。)

方法`thenApply`、`thenCompose`、`thenAccept`、...仅在潜在未来成功时执行任务。方法`onComplete`更通用，可用于调度无论未来正常终止还是异常终止都将运行的代码。在通话中:

```
reply.whenComplete((v, e) -> ...);
```

如果成功，变量`v`将包含未来产生的值，如果失败，变量`e`将包含异常(其中一个`v`或`e`将为空)。这里使用方法`onComplete`来关闭套接字(无论请求是否被成功处理)并记录异常(如果有的话)。

注意，方法`handleRequest`只调度回调和延续。它本身不执行任何请求处理，也不进行任何阻塞调用。因此，它可以由接受线程(快速)运行，而不需要创建异步任务。方法`serve`因此被简化:

# Scala 中的同一个服务器

这种非阻塞编程风格在 Scala 中比在 Java 中看起来更好:

Scala 代码看起来更好的主要原因是由于`for/yield`循环结构，这里使用它将三个未来聚合成一个。(Scala 代码的其他优势包括:模式匹配、η转换和`Future.failed`方法。)循环`for/yield`实际上被编译成对`map`和`flatMap`的调用，与 Java 版本非常相似。代码可以写成:

# 三个任务以上？

在服务器示例中，需要将三个期货的结果聚合成一个期货。这一步的 Scala 代码看起来比 Java 代码好，但是两者都不能很好地推广到更多的任务(在 for 循环中嵌套调用 50 个`thenCompose`或 50 个`->`是不切实际的)。一个更通用的方法是将一个未来列表组合成一个列表的未来，然后使用`thenApply/map`将任何需要的聚合应用到这个列表。

具体来说，问题是将类型为`List<CompletableFuture<T>>`的值转换为类型为`CompletableFuture<List<T>>`的值。在 Scala 中，方法`Future.sequence`就是这样做的。然而，在 Java 中，没有实现这种转换的标准库方法。使用方法`thenCombine`可以很容易地实现它，该方法从类型`T`的未来、类型`U`的未来和从`(T,U)`到`V`的函数产生类型`V`的未来:

这种实现是非阻塞的，不涉及任何额外的线程。

# 用承诺自己去做

在某些情况下，`thenApply`、`thenCompose`、`thenAccept`、`thenCombine`的组合...是不够的，需要从零开始创造新的未来。未来可以在没有值的情况下被创建，并在稍后被赋予一个值，通常来自不同的线程。这种期货通常被称为承诺。例如，`thenApply`方法可以实现为:

# 结束语

这篇文章中的代码使用(并滥用)了 Java 10 的`var`构造，试图不被各种未来类型的 Java ( `Future`、`FutureTask`、`CompletableFuture`和`CompletionStage`)分散注意力。这也使得 Java 代码更接近 Scala 实现。此外，大多数方法都假定抛出`UncheckedIOException`而不是`IOException`，因为`supplyAsync`、`thenApply`、`thenAccept`、...无法抛出检查过的异常，并且在 lambdas 中使用`try/catch`块会使代码更难执行。

这种编程风格依赖于对高阶方法的非阻塞调用，如期货上的 *map* 和 *flatMap* ，有时被称为*函数式并发编程*。与其他“功能”方面一样，这种编程风格在 Scala 中比在 Java 中更好。Java 函数接口臃肿(`CompletableFuture`有 60 个公共方法，相比之下 Scala 的`Future`有 23 个方法)，部分原因是 Java 仍然需要处理原语类型和`void`方法(Java 的类型`Function`、`Consumer`、`UnaryOperator`、`IntFunction`、`ToIntFunction`、`DoubleFunction`、`IntToDoubleFunction`、`DoubleToIntFunction`、`Predicate`、`IntPredicate`、`DoublePredicate`、`IntBinaryOperator`、`IntConsumer`等)。都对应 Scala 中的`Function1`)。

最后，我们还应该提到，通过使用 Java 的`ForkJoinPool`，也可以在不阻塞线程的情况下实现`fork/join`模式，这是在 Java 7 中添加的。在实践中，`ForkJoinPool`往往不实用，而`CompletableFuture`风格更受欢迎，即使它需要一些时间来适应。

**关键词:** Java，线程，线程池，未来，函数式并发编程。