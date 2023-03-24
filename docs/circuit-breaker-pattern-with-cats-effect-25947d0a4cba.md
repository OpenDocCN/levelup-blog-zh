# 具有猫效应的断路器图案

> 原文：<https://levelup.gitconnected.com/circuit-breaker-pattern-with-cats-effect-25947d0a4cba>

![](img/2fc3d0b1ad350f0b8b92a97a12c10e48.png)

最近，我对 Scala 中的函数库 [Cats Effect](https://typelevel.org/cats-effect/) 很感兴趣。为了用 Cats Effect 提高我的编码技能，特别是并发状态管理，我用这个库实现了一个断路器。断路器是软件开发中的设计模式之一。在微服务的背景下，这种设计模式很流行。

在这篇文章中，我想解释一下断路器模式以及如何用猫效应来实现它。所有完整的代码都在我的 Github 仓库里。

[](https://github.com/Hiroki6/SimpleCircuitBreaker) [## hiroki 6/简单断路器

### 在 GitHub 上创建一个帐户，为 Hiroki6/SimpleCircuitBreaker 的开发做出贡献。

github.com](https://github.com/Hiroki6/SimpleCircuitBreaker) 

# 断路器模式

[断路器模式](https://en.wikipedia.org/wiki/Circuit_breaker_design_pattern)是软件开发的一种设计模式。

在分布式系统中，每个服务都与另一个服务通信。然而，有时一个服务会一直向另一个服务发送请求，直到请求超时，即使该服务有问题并且没有响应。这种情况可能会导致线程耗尽，最终整个系统以及依赖于它的其他系统可能会停止。

断路器是这种情况下的解决方案。它充当服务之间的代理。这个代理监控请求，并根据最近的失败次数决定是否应该发送请求。因此，断路器是遵循这些状态的状态机:

*   关闭的

请求被直接发送到服务。如果请求失败，断路器会累计失败次数。当数量达到定义的阈值时，断路器断开。

*   打开

请求会立即返回错误。经过一段时间后，断路器变成半开。

*   半开的

请求被直接发送到服务。如果请求成功，断路器再次闭合。否则，断路器保持断开。

# 具有 Cats 效果的实现

正如我之前解释的，断路器有一个内部状态。就实现而言，这种状态必须得到适当的管理，即使请求是异步发送的。换句话说，断路器内部有一个可变的引用，这个引用必须是原子的。

为了达到这个要求， [Cats Effect](https://typelevel.org/cats-effect/) 在[*Cats . Effect . concurrent*](https://typelevel.org/cats-effect/concurrency/)*模块中提供了一些并发编程的好东西。在该模块中， [*Ref*](https://typelevel.org/cats-effect/concurrency/ref.html) 适用于这种状态管理。它有一个纯函数性的、并发的、无锁的可变引用。这个可变引用被保存为 AtomicReference。从下面的代码可以看出，它们的操作如 *get* 、 *modify* 都是原子执行的。*

```
*final private class SyncRef[F[_], A](ar: AtomicReference[A])(implicit F: Sync[F]) extends Ref[F, A] {
  def get: F[A] = F.delay(ar.get)def set(a: A): F[Unit] = F.delay(ar.set(a))def update(f: A => A): F[Unit] = modify { a =>
    (f(a), ())
  }def modify[B](f: A => (A, B)): F[B] = {
    [@tailrec](http://twitter.com/tailrec)
    def spin: B = {
      val c = ar.get
      val (u, b) = f(c)
      if (!ar.compareAndSet(c, u)) spin
      else b
    }
    F.delay(spin)
  }
}*
```

*接下来，让我们一步步来看看这个实现。这个实现的灵感来自于这两个模块: [*Glue。断路器*](https://hackage.haskell.org/package/glue-core-0.6.3/docs/Glue-CircuitBreaker.html) ，Haskell 的一个模块， [*断路器——单子*](https://github.com/YBogomolov/circuit-breaker-monad) ，打字稿的一个模块。*

## *连接*

**断路器*特性有*运行*和*获取状态*方法*。**

*   **运行**

*当使用断路器发送请求时，这是客户端使用的主要方法。参数是向另一个微服务发送请求的执行。在该方法中，根据断路器的状态来决定是否应该发送请求。当断路器打开时，该方法立即返回包含在 *IO 中的错误。**

*   **获取状态**

*该方法自动返回内部状态。这种状态可以读取，但不能从外部更改。*

*断路接口*

*断路器对象可以由伴随对象*的 *create* 方法创建。*在该方法中，状态被表示为【F，BreakerStatus】的 *Ref。**create*返回包装在类型构造函数 *F[_]* 中的断路器对象，因为可变状态是内部创建的，这是一个效果*。*在下面的例子中，它被包裹在 *IO 中。**

**断路器*允许客户使用相同的断路器或分开的断路器。正如您在下面的例子中看到的，在通过调用*平面图*创建的上下文中，断路器是共享的。否则，将单独创建和共享每个状态。正是由于引用的透明性，*引用*才有*。*该规范使我们很容易理解断路器状态在哪里共享。在 [Fabio 的演示](https://vimeo.com/294736344)中，详细解释了 *Ref* 的这种引用透明性。*

```
*val circuitBreaker: IO[CircuitBreaker[IO]] = 
  CircuitBreaker.*create*[IO](*breakerOptions*)

def toServiceA1(circuitBreaker: CircuitBreaker[IO]): IO[String] = *???* def toServiceA2(circuitBreaker: CircuitBreaker[IO]): IO[String] = *???* def toServiceB(circuitBreaker: CircuitBreaker[IO]): IO[String] = *???*def separateBreaker = 
  circuitBreaker.flatMap(toServiceA1) >> circuitBreaker.flatMap(toServiceB)

def sameBreaker = circuitBreaker.flatMap { c =>
  toServiceA1(c) >> toServiceA2(c)
}*
```

*另一方面，这个*断路器*对象没有考虑多节点服务器的用例。这意味着断路器状态不会在服务器之间共享。为了满足这一要求，应该使用持久存储。*

> *在多节点(集群)服务器中，上游服务的状态需要反映在集群中的所有节点上。因此，实现可能需要使用持久存储层，例如网络缓存(如 [Memcached](https://en.wikipedia.org/wiki/Memcached) 或 [Redis](https://en.wikipedia.org/wiki/Redis) )或本地缓存(基于磁盘或内存)来记录外部服务对应用程序的可用性。*

***断路器闭合***

*当断路器闭合时，*调用关闭的*方法执行客户端取的*体*。如果执行成功，则返回响应。如果执行失败，失败次数会随着更新状态并自动获得结果的*修改*而增加。当失败次数达到阈值时，断路器断开。*

*CallIfClosed*

***刀闸打开***

*当断路器打开时， *callIfOpen* 方法检查断路器打开后是否经过了特定时间。经过一段特定时间后，表示断路器处于半开状态，并且*主体*处于*方式*和*方式*方式*。*否则*、*断路器将保持打开，并立即返回错误。*

*卡利福彭*

***断路器半开***

*当断路器半开时， *canaryCall* 方法试图执行*体。*如果执行成功，断路器将再次闭合。*

*CanaryCall*

## *运行断路器*

*下面这个例子展示了如何使用*断路器*和*[*http4s*](https://github.com/http4s/http4s)*。http4s 也依赖于 Cats 效果，所以使用这个库和 *CircuitBreaker 很容易。*在下面的例子中，一个*断路器*对象被共享。你也可以在每个上下文中通过调用 *flatMap* 来单独使用*一个断路器*对象，就像我之前解释的那样。***

*带 http4s 的断路器*

*而且，你可以通过我实现的测试代码看到*断路器*对象的行为。这个测试代码是用 [cats-retry](https://cb372.github.io/cats-retry/docs/) 实现的，实现了一个重试机制，这是一个用于重试可能失败的动作的库。我在之前的文章中已经写了关于这个库的内容。*

*下面的代码是测试代码中的一个测试用例。证明执行失败一次后断路器打开，定义为 *maxBreakerFailures。**

*电路断路器测试*

# *结论*

*很多库比如 doobie，http4s 都在内部使用 Cats 效应。所以通过使用这些库，你可以用函数式编程实现整个系统。另外，在自己实现一个模块方面，这个库非常有用，因为它提供了并发编程、状态管理等强大的模块。*

*如果我的代码有问题，或者你有另一种实现具有猫效应的断路器的方法，请通过[hirokifujino0108@gmail.com](mailto:hirokifujino0108@gmai.com?subject=Circuit Breaker with Cats Effect Feedback)告诉我。*

*感谢您的阅读！*

## ***参考文献***

*   *[Scala Italy 2018—Fabio la Bella—纯 FP 中的共享状态:当一个状态单子不会做](https://vimeo.com/294736344)*
*   *[功能世界中的断路器](/circuit-breaker-in-a-functional-world-9c555c8e9527)*
*   *[功能编程中的共享状态](https://typelevel.org/blog/2018/06/07/shared-state-in-fp.html)*