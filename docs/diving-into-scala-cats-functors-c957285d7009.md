# 深入 Scala 猫——函子

> 原文：<https://levelup.gitconnected.com/diving-into-scala-cats-functors-c957285d7009>

![](img/e99ea2ae39b7c18da1c2d9575d55576b.png)

照片由[凯特·斯通·马西森](https://unsplash.com/@kstonematheson?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

如果你不熟悉类型类的概念，我建议你阅读我的另一篇文章来解释它们。Cats 库广泛使用了类型类，对其有基本的理解是本文的前提。

在以前的文章中，我们谈到了[半群](/diving-into-scala-cats-semigroups-732ef2432042)和[幺半群](/diving-into-scala-cats-monoids-82e744b9e518)，它们是让我们将相同类型的值组合在一起的抽象。

在这篇文章中，我们将看看**函子**，它允许我们在不知道容器本身的任何实现细节的情况下操作容器内部的值。

# 什么是函子？

就函数式编程而言，*函子*就是可以被映射的东西，或者我们可以称之为*可映射*。

它基本上是一种抽象，允许我们
在一个上下文中表示操作序列，例如集合、期货和期权。

Cats 的类型类为可映射类型提供了一个基类，允许我们为期货、期权、列表等编写通用代码。

标准 Scala 没有基本类型/特征来代表以下内容:

```
def calcBudget(orders: List[LineItem]) = orders.map(...)
def calcBudget(maybeOrder: Option[LineItem]) = maybeOrder.map(...)
def calcBudget(eventualOrder: Future[LineItem]) = eventualOrder.map(...)
```

然而，使用 cats 的`Functor`类型类，我们可以编写一个通用方法，如下所示:

```
def calcBudget(order: Functor[LineItem]) = order.map(...)
```

# 函子的定义

一个`Functor` 是一个`F[A]`型带地图操作 `(A => B) => F[B]`:

```
package catstrait Functor[F[_]] {
  def map[A, B](fa: F[A])(f: A => B): F[B]
}
```

它接受初始的`F[A]`作为转换函数的参数。

让我们用`Functor`重写`calcBudget`:

```
import cats.Functorcase class LineItem(price: Double)def calcBudget[F[_]](order: F[LineItem])(implicit ev: Functor[F]): F[LineItem] = {
  Functor[F].map(order)(o => o.copy(price = o.price * 1.2))
}
```

`Functor[F].map(...)`方法基于`F[_]`的类型进行参数化。这里的`F[_]`是指任何一个*类型构造器*(一个类型包装另一个类型)都有一个`map`方法，比如`Option`、`List`、`Future`等。

隐式参数`ev: Functor[F]`意味着我们必须有一个*类型的类实现*，它允许我们把`F`当作一只猫`Functor`。

# 函子定律

如果我们正在创建自己的函子，那么`Functor` 必须遵循这些定律。

*   *身份*:如果我们传递一个`identity`函数给`Functor map`方法，那么`Functor` 必须取回原来的`Functor`。
*   *合成*:如果两个函数像`(f andThen g` ) `(x)`或`map(f).map(g)` 一样顺序执行，那么输出必须等于`g(f(x))`。

# 仿函数类型类和实例

函子类型类是`cats.Functor`。Cats 库为我们提供了内置的函子和预定义类型的实现，如`Option`、`List`、`Future`等。

到目前为止一切顺利，让我们尝试使用类型类实现`Functor`的`List`:

```
import cats.Functorobject ListFunctor {
  def transformList(list: List[Int]): List[Int] = {
    Functor[List].map(list)(_ * 2)
  }
}val list: List[Int] = List(1, 2, 3, 4, 5)
val transformedList = List(2, 4, 6, 8, 10)
assert(ListFunctor.transformList(list) == transformedList)
```

现在让我们尝试使用`Option`的类型类实现`Functor`:

```
import cats.Functorobject OptionFunctor {
  def transformOption(option: Option[Int]): Option[String] = {
    Functor[Option].map(option)(_.toString)
  }
}val option: Option[Int] = Some(10)
val transformedOption = Some("10")
assert(OptionFunctor.transformOption(option) == transformedOption)
```

现在让我们尝试使用类型类实现`Functor`中的`Future`:

```
import cats.Functorobject FutureFunctor {
  def transformFuture(future: Future[Int]): Future[Int] = {
    Functor[Future].map(future)(_ + 1)
  }
}val future: Future[Int] = Future{10}
val transformedFutureResult = 11
FutureFunctor.transformFuture(future).map(result => assert(result == transformedFutureResult))
```

每当我们为`Future`调用一个`Functor` 时，编译器将
通过隐式解析定位`futureFunctor` ，并在调用位置递归搜索一个
`ExecutionContext` 。这是资料片看起来的样子:

```
// We write this:
Functor[Future]// The compiler expands to this first:
Functor[Future](futureFunctor)// And then to this:
Functor[Future](futureFunctor(executionContext))
```

# 仿函数语法

通过使用`Functor` 类型类，我们可以抽象出任何可以映射的东西。我们不局限于标准库中的类型，我们可以向我们自己的类型添加一个 map 方法。

Cats 允许我们通过使用*语法*或*扩展*方法的概念来做到这一点。

这个概念是使用隐式类实现的，并允许我们编写`order.map(...)`而不是`Functor[F].map(order)(...)`。我们也可以通过指定类型`F[_]`为`Functor`来删除隐式参数:

```
import cats.syntax.functor._ //for mapdef calcBudget[F[_]: Functor](order: F[LineItem]): F[LineItem] = {
  order.map(o => o.copy(price = o.price * 1.2))
}
```

但是我们也可以构建一个高阶函数，它可以处理任何类型并执行任何映射操作:

```
def withFunctor[A, B, F[_]](item: F[A], op: A => B)(implicit ev: Functor[F]): F[_] = Functor[F].map(item)(op)val lineItemsList = List(LineItem(10.0), LineItem(20.0))
val result = FunctorSyntax.withFunctor(lineItemsList, calcBudget)
assert(result == List(LineItem(10.0), LineItem(20.0)))
```

当然，这是一个人为的例子，因为`withFunctor`在简单的内联调用中没有增加任何值，但是它说明了一点，即`A, B & F`可以是任何东西，只要方法的调用者:

*   提供证据证明`F` 是一个`Functor` (一个实现)
*   知道如何从`A` 映射到`B`

# 摘要

在本文中，我们已经了解了 Scala Cats 库提供的*函子*。*函子*代表排序行为。我们讨论了为什么我们需要*函子*以及如何定义它们。对于一个要成为*仿函数*的类型类，它必须遵守两个*法则*:首先，它应该能够组合两个*映射*调用。其次，使用*标识*函数进行映射应该没有影响。我们看到，我们可以为 Scala 生态系统*中的所有*可映射*类型编写*仿函数*类型类实现，如* *期货*、*选项*、*列表等*。我们不局限于标准库中的类型，我们还可以通过使用*语法*或*扩展*方法的概念，将*映射*方法添加到我们自己的类型中。

在接下来的文章中，我们将了解更多关于猫的核心概念。
敬请期待！！！

# 参考

我所知道的最好的两个 Scala Cats 资源在这里:

*   猫库可以在 [GitHub](https://github.com/typelevel/cats) 获得
*   本书， [*高级 Scala 与猫*](https://underscore.io/books/advanced-scala/)

# 类似文章-

你也可以看看我在 *Scala Cats* 系列上的其他文章

*   【Scala 猫入门
*   [潜入标量猫—半群](/diving-into-scala-cats-semigroups-732ef2432042)
*   [潜入斯卡拉猫——幺半群](/diving-into-scala-cats-monoids-82e744b9e518)
*   [函数式编程中的函子](/functors-in-functional-programming-dfaba4cfb2ed)