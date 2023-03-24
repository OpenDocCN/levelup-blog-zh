# Monix 任务中的异步边界

> 原文：<https://levelup.gitconnected.com/asynchronous-boundary-in-monix-task-900995ba8a28>

最近一直在玩 [Monix](https://monix.io/) 。在本文中，我将关注 [Monix Task](https://monix.io/docs/current/eval/task.html) ，尤其是线程如何切换 Monix Task 的方法。

![](img/cd7c200539cb69302f27ca5a96833404.png)

# Monix 任务

Monix Task 在 Monix 中实现，Monix 是一个异步和反应式编程 Scala 和 Scala.js 库。

> Task 是一种数据类型，用于控制可能的惰性和异步计算，有助于控制副作用，避免不确定性和回调地狱。

Monix 还提供了流数据类型和用于管理并发性的纯功能实用程序，如 monix-reactive、monix-catnap 和 monix-tail。

目前最新的主要版本是 [3.3.0](https://monix.io/blog/2020/11/07/monix-v3.3.0.html) ，2020 年 11 月发布。

# 异步边界

异步边界是线程之间的上下文切换。为了实现高效的异步编程，我们必须引入一个带有适当线程池的异步边界。比如异步边界应该引入一个无界线程池用于阻塞执行，就像 [Daniel 的文档](https://gist.github.com/djspiewak/46b543800958cf61af6efa8e072bfd5c)和 [Monix 的文档](https://monix.io/docs/current/best-practices/blocking.html)说的那样。

Monix Task 有引入异步边界的有用方法。

# Monix 任务中的异步边界

现在，让我们看看如何在 Monix Task 中引入异步边界。

**调度器**

当*任务*运行时，需要 [*调度器*](https://monix.io/docs/current/execution/scheduler.html) 。这种关系类似于*执行上下文*和*未来*之间的关系。但是， *ExecutionContext* 不仅在 *Future* 运行时需要，在 *Future* 被定义以及 *Future* 的几乎所有方法被调用时也需要。

*调度器*是*执行上下文的子类。*所以，*调度器*有一个线程池，比如 *CachedThreadPool* ， *ForkJoinPool* 等。调度器*伴随对象*提供了为每个线程池创建*调度器*对象的有用方法。下面的代码是使用这些方法定义*调度器*对象的例子。你也可以像[这个链接](https://monix.io/docs/current/execution/scheduler.html#builders-on-the-jvm)一样指定 *ExecutionModel* 和 *ReportFailure* 。

虽然我没有详细解释*调度器**，但是调度器*也是对 *Java 的 ScheduledExecutorService* 的替代。

> Monix `Scheduler`的灵感来自[react vex](http://reactivex.io/)，是一个增强的 Scala [ExecutionContext](https://monix.io/docs/current/execution/scheduler.html#scala.concurrent.ExecutionContext) ，也是 Java 的[scheduled executorservice](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ScheduledExecutorService.html)的替代品，同时也是 Javascript 的 [setTimeout](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/setTimeout) 的替代品。

**任务转移**

这是切换线程池的基本方法，也就是说，引入一个异步边界。

如果没有参数，计算将转移到默认线程池。有了参数中的*调度器*，计算转移到*调度器*拥有的线程池。

*任务*的异步方法大多在方法内部调用`shift`方法。

## 用 Monix 的异步方法进行线程转移

我们来看看 Monix 的每个异步方法的线程移位。

下面的代码是一个简单的程序，它在一个*任务*上下文中执行三个任务。在生产中，应该进行错误处理，而不是调用`runSyncUnsafe`方法。

如您所见，这三个任务在主线程上执行，因为没有引入任何异步边界。另一方面， *Future* 总是引入一个带有线程池的异步边界。这会导致不必要的上下文切换开销。

*   **执行异步**

`executeAsync`方法引入了一个带有默认线程池的异步边界，这个线程池是`runSyncUnsafe`方法在执行之前使用的。在下面的代码中可以看到，`shift` 方法在这个方法中被称为*。*

```
final def executeAsync: Task[A] =
  Task.shift.flatMap(_ => this)
```

而且，调用`executeAsync` 和下面代码调用`evalAsync` 是一样的。

```
Task.evalAsync(a) <-> Task.eval(a).executeAsync
```

下面的代码使用的是`executeAsync` 方法。`heavyTask` 方法运行在默认线程池，而不是主线程上。

*   **异步边界**

`asyncBoundary`方法在执行后引入了一个异步边界。正如您在下面的代码中看到的,`asyncBoundary`方法可以指定一个*调度程序。*

```
final def asyncBoundary: Task[A] =
  flatMap(a => Task.*shift*.map(_ => a))final def asyncBoundary(s: Scheduler): Task[A] =
  flatMap(a => Task.*shift*(s).map(_ => a))
```

下面的代码使用的是`asyncBoundary` 方法。`heavyTask` 函数是在`asyncBoundary` 方法占用的线程池中运行的，而不是主线程，因为`asyncBoundary`方法是在此方法之前调用的。

正如你在上面的`executeAsync`和`asyncBoundary`方法中看到的这些代码，在引入异步边界之后，后续任务在同一个线程池上运行。这可能会导致线程池的意外使用。例如，在上面的代码中，`lightTask` 函数应该运行在默认的线程池上，而不是用于阻塞的线程池。在这种情况下，线程池应该切换回默认的线程池。

在下面的代码中，通过调用`shift` 和`asyncBoundary` 方法，线程池被切换回默认线程池。

*   **执行**

如果`forceAsync` 参数设置为 true，则`executeOn` 方法可以指定计算运行的线程池。

```
final def executeOn(s: Scheduler, forceAsync: Boolean = true): Task[A] =
  *TaskExecuteOn*(this, s, forceAsync)
```

从 3.0 版本开始，线程池变成了在`executeOn` 方法被调用后，`runSyncUnsafe`方法取的默认线程池。它防止我们导致线程池的非预期使用。

[](https://github.com/monix/monix/pull/670) [## 通过 alexandru Pull Request # 670 monix/monix，任务在分叉和异步边界方面变得更加智能

### 变化摘要:并行收集结果或创建竞争条件的任务操作现在分叉了…

github.com](https://github.com/monix/monix/pull/670) 

下面的代码使用的是`executeOn` 方法。`heavyTask` 函数是在线程池上运行进行阻塞的，那么`lightTask`函数是在默认的线程池上运行这个方法之后。

为了更深入地了解使用`forceAsync`参数方法的行为，我编写了下面的代码。

1.  将 executeOn 和 forceAsync 设置为 true 的 Task.eval

该任务正在线程池上运行，用于阻塞`executeOn` 方法指定的线程。任务完成后，线程池会迅速恢复到默认线程池。

*2。*将 executeOn 和 forceAsync 设置为 false 的 Task.eval

该任务和后续任务正在主线程上运行。

*3。*将 executeOn 和 forceAsync 设置为 true 的 Task.evalAsync

该任务正在线程池上运行，用于阻塞`executeOn` 方法指定的线程。任务完成后，线程池会迅速恢复到默认线程池。

*4。*将 executeOn 和 forceAsync 设置为 false 的 Task.evalAsync

该任务和后续任务正在线程池上运行，用于阻塞`executeOn`方法的执行。

*   **parMap2**

作为最后一个方法，我们来看看`parMap2` 方法的线程移位。建议使用`parMap2` 方法并行执行两个任务。这种方法也适用于错误处理和取消。

> 如果其中一个任务失败，那么所有其他任务都将被取消，最终结果将是失败。

在下面的代码中，`parMap2` 方法需要两个任务。每个任务并行运行，内部任务运行在指定的线程池上。

# 结论

Monix Task 有有用的异步方法，但是每个行为都是不同的。所以我们必须分别使用这些方法。

感谢您的阅读！