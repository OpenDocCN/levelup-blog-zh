# 如何使用 Java 可调用和未来

> 原文：<https://levelup.gitconnected.com/how-to-use-java-callable-and-future-5d79ecb47c8b>

## Java Executor 线程管理完全指南—第 2 部分

![](img/c7d3e45fa92f8e9fc1696ce72b24b5f8.png)

Joshua Sortino 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在我之前的[文章](/how-to-use-the-executor-framework-in-java-58a610d20b87)中，我讨论了如何将 Runnable 对象用于 Executor 服务。在本文中，您可以学习如何使用可调用对象和未来对象进行线程管理。

使用 Runnable 对象定义的任务不能返回结果。那么，如果您想从提交给线程池的任务中返回一个结果，该怎么做呢？解决方案是使用可调用对象，而不是可运行对象。可调用对象返回的结果称为未来对象。使用这个 Future 对象，我们可以了解可调用任务的状态。Java 中的 Callable 和 Future 接口都提供了线程管理的方法。所以让我们看看如何在我们的代码中使用这些方法。

# 请求即付的

`Callable`接口有一个名为`call()`的方法，它应该包含由线程执行的代码。它类似于`Runnable`接口中的`run()`方法，但与`run()`方法不同的是`call()`方法抛出一个检查过的异常。让我们看一个使用`call()`方法的简单例子。

# 将来的

使用`submit()`方法，您可以提交一个`Callable`对象给 executor 服务来执行。但是，该方法不知道提交任务的结果何时可用。因此，它返回一个`Future`对象，当任务结果可用时，该对象可用于检索任务结果。如果你熟悉 JavaScript `Promises`，你会发现`Futures`和`Promises`非常相似。`Future`接口提供了一个`get()` 方法，可以等待`Callable`完成然后返回结果。有一个重载版本的`get()`方法，您可以在其中指定等待结果的时间。

```
Future.get(long timeout, TimeUnit unit)
```

这有助于避免当前线程被长时间阻塞。还可以使用`isDone()`方法来检查任务是否完成。如果任务完成，则返回 true，否则返回 false。

让我们看一个使用`ExecutorService`执行`Callable`任务并使用未来接口的`get()`方法获得结果的例子。如果你在理解下面例子中的执行器服务和线程池时有任何困难，请参考我以前的[文章](/how-to-use-the-executor-framework-in-java-58a610d20b87)，我在其中讨论了这些概念。

Java Future 还提供了一个`cancel()`方法，该方法接受一个布尔参数来取消相关的可调用任务。它尝试取消任务的执行，如果成功取消则返回 true，否则返回 false。如果你运行`cancel(true)`，那么当前正在执行任务的线程将被中断。您可以使用`isCancelled()`方法来检查任务是否被取消。还有，取消任务后，`isDone()`总会返回 true。

您可以通过将一组`Callable`对象传递给`ExecutorService`接口中的`invokeAll()`方法来执行多个任务，该方法返回未来对象的列表。

```
List<Callable<String>> taskList = Arrays.asList(task1,task2,task3);
List<Future<String>> futures = executorService.invokeAll(taskList);
```

`invokeAny()`方法也接受`Callables`的集合，执行它们，并返回单个任务第一次成功执行的结果(如果已经成功执行过)*。*

```
List<Callable<String>> taskList = Arrays.asList(task1,task2,task3);
String result = executorService.invokeAny(taskList);
```

Java 还有一个名为`FutureTask`的具体类，它实现了 Runnable 和 Future，方便地结合了两种功能。下面是创建一个`FutureTask`对象的例子。可以通过向其构造函数提供一个`Callable`来创建该对象。

```
FutureTask<Integer> futureTask = new FutureTask<>(callable);
```

使用`execute()`方法可以执行`FutureTask`对象。大多数情况下，您不需要在应用程序代码中使用`FutureTask`，因为您可以通过提交一个`Callable`任务，然后使用返回的`Future`对象来完成同样的事情。您可以根据自己的需求选择使用哪种方法。

以上是对 Java 中 Callable 和 Future 工作原理的理解。好了，希望你学到了新的东西，希望以后能给你带来更多有趣的文章。

感谢您的阅读和快乐编码！

# 参考

*   [Java ExecutorService 指南](https://www.baeldung.com/java-executor-service-tutorial)
*   [Java Oracle docs 接口未来](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/Future.html)
*   [Java Oracle docs 接口可调用](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/Callable.html)