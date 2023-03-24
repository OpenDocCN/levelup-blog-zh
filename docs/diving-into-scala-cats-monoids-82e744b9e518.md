# 潜入 Scala 猫——幺半群

> 原文：<https://levelup.gitconnected.com/diving-into-scala-cats-monoids-82e744b9e518>

![](img/1da08a2a639c498cc48a5c24f20f3e15.png)

照片由[克里斯·巴尔巴利斯](https://unsplash.com/@cbarbalis?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

今天我们将再次深入 Scala Cats 来发现幺半群。如果你还没有读过我关于[陷入 Scala 猫-半群](/diving-into-scala-cats-semigroups-732ef2432042)的帖子，我建议你现在就去读。

# 什么是幺半群？

*幺半群*扩展了*半群*，并为给定类型添加了一个默认值或回退值。`Monoid`类型类有两种方法——一种是半群的`combine`方法，另一种是执行恒等运算的`empty`方法。

在关于[半群](https://blog.knoldus.com/diving-into-scala-cats-semigroups/)的帖子中，我们看到了一个使用半群和 Scala 的`fold()`对一组值进行运算的例子:

```
def combineStrings(collection: Seq[String]): String = {
  collection.foldLeft("")(Semigroup[String].combine)
}
```

我们讨论了半群的限制，其中我们不能为上面的表达式编写一个通用方法`combineAll(collection: Seq[A]): [A]`，因为回退值将取决于`A` 的类型(对于`String`， `0`对于`Int`，等等)。

幺半群通过引入一个单位/空元素提出了一个解决这个缺点的方法。

`Monoid`的签名可以指定为:

```
trait Monoid[A] extends Semigroup[A] {   
  def empty: A 
}
```

# 单位元如何解决半群的局限性？

我们在`combineStrings`中传递的空`String` 称为标识或空值，我们可以将其视为后备值或默认值。它解决了半群的缺点。

现在，我们可以使用幺半群轻松地提供泛型方法*combineAll(collection:Seq[A]):[A]*的实现:

```
def combineAll[A](collection: Seq[A])(implicit ev: Monoid[A]): A = {
  val monoid = Monoid[A]
  collection.foldLeft(monoid.empty)(monoid.combine)
}
```

# 幺半群保持结合律和同一律

由于半群遵循结合律，同样的规则加上一些附加物也适用于幺半群。

`combine` 操作必须是关联的，并且`empty`值应该是`combine`操作的标识:

```
combine(x, empty) = combine(empty, x) = x
```

例如:

```
// Integer addition using 0 as an identity (Rules valid)
1 + 0 == 0 + 1 == 1// Integer multiplication using 1 as an identity (Rules valid)
2 * 1 == 1 * 2 == 2// Integer multiplication using 0 as an identity (Rules invalid)
2 * 0 == 0 * 2 != 2
```

所以很明显`empty` /identity 元素依赖于上下文，而不仅仅是类型。这就是为什么`Monoid` (和`Semigroup`)实现不仅特定于类型，而且特定于`combine` 操作。

Cats 允许将幺半群组合在一起以形成更大的幺半群，并编写更一般化的函数，这些函数将采用可组合的东西而不是一些具体的类型。

在接下来的文章中，我们将了解更多关于猫的核心概念。
敬请期待！！！

# 参考

我所知道的最好的两个 Scala Cats 资源在这里:

*   猫库可以在 [GitHub](https://github.com/typelevel/cats) 获得
*   本书， [*高级 Scala 与猫*](https://underscore.io/books/advanced-scala/)

# 类似文章-

你也可以看看我在 Scala Cats 系列的其他文章——

*   【Scala 猫入门
*   [潜入标量猫—半群](/diving-into-scala-cats-semigroups-732ef2432042)
*   [深入 Scala 猫——函子](/diving-into-scala-cats-functors-c957285d7009)
*   [函数编程中的函子](/functors-in-functional-programming-dfaba4cfb2ed)