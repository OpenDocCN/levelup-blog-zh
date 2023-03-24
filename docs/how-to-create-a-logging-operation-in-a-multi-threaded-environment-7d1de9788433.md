# 如何在多线程环境中创建日志记录操作

> 原文：<https://levelup.gitconnected.com/how-to-create-a-logging-operation-in-a-multi-threaded-environment-7d1de9788433>

![](img/2d5663bbc6d3a01dc259c86f5e072c8e.png)

*原载于*[*https://edward-huang.com*](https://edward-huang.com/scala/cats/programming/functional-programming/2020/01/27/how-to-create-a-logging-operation-in-a-multi-threaded-environment/)*。*

假设您正在编写如下计算斐波纳契数列的函数，并添加一个打印语句用于调试:

然后，运行以下函数:

```
def fib(n:Int) : Int = {
  if(n == 0 || n ==1) {
    println(s"base case : $n")
    n
  }
  else {
    println(s"add fib(n-1) + fib(n-2) $n")
    fib(n-1) + fib(n-2)
  }
}
```

在同步执行多个功能的情况下，它工作得很好:

```
fib(5)

*// interpreter* add fib(n-1) + fib(n-2) 5
add fib(n-1) + fib(n-2) 4
add fib(n-1) + fib(n-2) 3
add fib(n-1) + fib(n-2) 2
base case : 1
base case : 0
base case : 1
add fib(n-1) + fib(n-2) 2
base case : 1
base case : 0
add fib(n-1) + fib(n-2) 3
add fib(n-1) + fib(n-2) 2
base case : 1
base case : 0
base case : 1
fib: (n: Int)Int
res0: Int = 5
```

然而，如果函数是以异步方式包装的，就很难区分哪个日志与哪个步骤相关联。在这种情况下，我们如何以异步方式调试操作呢？

# 编写数据类型来拯救

Cats 有一个 writer 数据类型类，它可以通过将底层结果值附加到日志语句来帮助您，以便您可以异步理解日志语句。

在 Cats 中，Writer 数据类型定义为:`Writer[L, V]`。

l 是你想要的测井类型集合[幺半群](https://typelevel.org/cats/typeclasses/monoid.html)；在这种情况下，我们可以将 L 设为一个`Vector[String]`。

V 是函数运算的结果类型——在这种情况下，我们可以将 V 设置为`Int`,因为我们从斐波那契数列中返回一个整数类型。

# 初始化

一旦您创建了定义中的`L`和`V`，您就可以创建您的 Writer 数据类型，如下所示:

```
import cats.data.Writer
import cats.implicits._

Writer(Vector("log1", "log2"), 0)
```

或者

```
import cats.data.Writer
import cats.implicits._

0.writer(Vector("log1", "log2"))
```

如果你看到了 repl，或者结果，你会意识到它不是`Writer[L, V]`，而是返回一个`WrtierT[Id, L, V]`。是因为猫用类型别名从`WriterT`派生出`Writer`的值。在这篇文章中，我们将讨论如何使用 Writer。所以可以忽略细节，把类型`WriterT[Id, L, V]`当成`Writer[L, V]`。

# 有记录值但没有结果

如果有日志但没有结果，我们可以使用`tell`:

```
Vector("msg1", "msg2").tell()
```

我们还可以使用`run`同时提取输出和日志:

```
val writer = Writer(Vector("something"), 0)
val (log, result) = writer.run
```

# 提取结果值和日志类型

提取结果并用`value`和`written`分别记录:

```
val a = Writer(Vector("msg1"),0)
val log = a.written
val result = a.value

println(s"log: $log result: $result")
*// log: Vector(msg1) result: 0*
```

# 创作和改造作家

由于 Writer 是一个[单子](https://typelevel.org/cats/typeclasses/monad.html)，你可以用`map`和`flatmap`对 Writer 做一个操作。

`flatMap`将日志类型和来自源编写器的结果类型以及排序函数的结果结合在一起。

因此，一个好的做法是放置一个具有高效的附加和连接方法的日志类型，例如 Vector:

```
val res = for {
    a <- Writer(Vector("a"), 1)
    _ <- Vector("c").tell
    b <- 3.writer(Vector("3", "b"))
  } yield {
    println(s"a $a") *// 1
*    println(s"b $b") *// 3
*    a + b *// 4
*  }

println(res) *//WriterT((Vector(a, c, 3, b),4))*
```

请注意，`tell`方法将保留原编写器，并将“c”附加到源编写器，即“a”。

Writer 的结果是基于`yield`函数之后的计算结果。例如，如果在`yield`之后没有加法，只有`a`，则最终结果将是`WriterT((Vector(a,c,3,b),1))`。

# 转型作家

我们可以使用`mapWritten`将日志类型改为全大写:

```
*// .. take example from previous res example* val upperCaseLog = res.mapWritten(previousLog => previousLog.map(_.toUpperCase))
  println(upperCaseLog) 
  *// WriterT((Vector(A, C, 3, B),4))*
```

您也可以通过使用`mapBoth`来转换这两种类型:

```
val newWriterValueAndLog = res.mapBoth{ (log,res) =>
    (log :+ "appending z", res+12)
  }
println(newWriterValueAndLog)

WriterT((Vector(a, c, 3, b, appending z),16))
```

使用`swap`交换日志类型和结果:

```
val swappedWriter = res.swap
println(swappedWriter)
*// WriterT((4,Vector(a, c, 3, b)))*
```

最后但同样重要的是，使用`reset`重置 Writer 中的日志值:

```
val resetWriter = res.reset
println(resetWriter)

*// WriterT((Vector(),4))*
```

# 作家在行动

至此，您已经了解了什么是 Writer，现在让我们重构代码，将 Writer 融入其中:

首先，让我们添加一个超时函数来设置异步环境。

```
def timeout[A](body: => A):A = try {
    body
  } finally Thread.sleep(100)
```

然后，我们从 Writer 中设置 LogFib 的类型别名:

```
type LogFib[A] = Writer[Vector[String], A]
```

我们改变 Fib 函数来返回一个`LogFib[Int]`:

```
def fib(n:Int): LogFib[Int] = {
    timeout(
      if(n == 0 || n ==1) {
        n.writer(Vector(s"base case : $n"))
      }
      else {
        for {
          _ <- Vector(s"add fib(n-1) + fib(n-2) $n").tell
          fib1 <- fib(n-1)
          fib2 <- fib(n-2)
        } yield fib1 + fib2
      }
    )
  }
```

然后你可以这样运行它:

```
import scala.concurrent.duration._
  implicit val ec :ExecutionContext = scala.concurrent.ExecutionContext.Implicits.global
  val fibRes = Await.result(Future.sequence(Vector(
    Future(fib(5)),
    Future(fib(4)),
    Future(fib(3))
  ))
  , Duration.Inf)

  fibRes.toList.map(w => {
    val (logging, endResult) = w.run
    println(s"logging $logging endResult $endResult")
  })
```

# 外卖食品

*   编写器数据类型对于多线程环境中的日志记录操作非常有用
*   编写器日志与结果相关联。因此，这是记录多线程计算顺序的一个极好的方法。

所有的例子信息都在 [Github](https://github.com/edwardGunawan/Blog-Tutorial/tree/master/ScalaTutorial/catsWriterType) 中

# 喜欢这篇文章？

注册我的[时事通讯](https://edward-huang.com/subscribe/)每周获取此内容！