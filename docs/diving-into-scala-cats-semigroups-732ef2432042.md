# 深入 Scala 猫—半群

> 原文：<https://levelup.gitconnected.com/diving-into-scala-cats-semigroups-732ef2432042>

![](img/10e687c6865ab71039550e09ee30b8a7.png)

[拍摄于](https://unsplash.com/@centelm?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的

对猫不熟悉？别担心，看看我之前的文章【Scala 猫入门就知道猫有多神奇了。

在这篇文章中，我们将讨论猫中半群的概念和实现。

# 什么是半群？

在函数式编程术语中,*半群*是一个用结合的二元运算封装聚合的概念。

半群类型类带有一个方法`combine`,它通过遵循结合性原则简单地组合相同数据类型的两个值。

`combine`方法构造为:

```
trait Semigroup[A] {
    def combine(x: A, y: A): A
}
```

并且可以实现为:

```
// The type class definition
import cats.kernel.Semigroup// Import type class instance for type Int
import cats.instances.int._// The combine implementation for Int is by default addition
val onePlusTwo = Semigroup[Int].combine(1, 2)
```

中缀语法也可用于具有`Semigroup`实例的类型:

```
import cats.implicits._1 |+| 2
```

# 结合性是半群的唯一法则

`combine`具有关联性，这意味着对于`x`、`y`和`z`的任何选择，以下等式必须成立。

```
combine(x, combine(y, z)) = combine(combine(x, y), z)
```

结合性允许我们以任何我们想要的方式划分数据，并且潜在地并行操作。

例如，对于一个给定的数字列表:`1, 2, 3, 4, 5`我们可以这样做:

```
// Could run in different threads
val group1 = Semigroup[Int].combine(1, 2)
val group2 = Semigroup[Int].combine(3, 4)
val group3 = Semigroup[Int].combine(group1, group2)
val total  = Semigroup[Int].combine(group3, 5)
```

# 编写自定义半群

`Int`的`combine`实现将添加两个参数。如果我们想把两个数相乘呢？我们可以编写自己的实现:

```
implicit val multiplicationSemigroup = new Semigroup[Int] {
  override def combine(x: Int, y: Int): Int = x * y
} // uses our implicit Semigroup instance above
val four = Semigroup[Int].combine(2, 2)
```

Cats 允许以更简洁的方式提供`combine` 的实现:

```
implicit val multiplicationSemigroup: Semigroup[Int] = (x: Int, y: Int) => x * y// or more succinctly
implicit val multiplicationSemigroup: Semigroup[Int] = (x, y) => x * y// or even
implicit val multiplicationSemigroup: Semigroup[Int] = _ * _// or even more verbose
implicit val multiplicationSemigroup = Semigroup.instance[Int](_ * _)
```

我们可以轻松地为 Scala 生态系统中所有常见类型的`Semigroup` 实例提供自己的`combine`实现。

# 具有串的半群

`String` 的 Cats 实现连接了两个参数。但是该实现不包括它们之间的空格。如果我想在字符串之间添加一个空格呢？

```
implicit val customStringSemigroup = Semigroup.instance[String](_ + " " + _) Semigroup[String].combine("john", "doe") 
// results: String = "john doe"
```

# 具有集合的半群

给定关联约束，我们可以从简单的`combine(x, y)`方法构建更有用的结构。

我们可以直接使用递归或者利用 Scala 的`fold()`来操作一组值:

```
def combineStrings(collection: Seq[String]): String = {
  collection.foldLeft("")(Semigroup[String].combine)
}
```

然而，仅仅给定`Semigroup`，我们不能笼统地写出上面的表达式。

# 半群的极限

如果我们试图为上面的表达式编写一个泛型方法`combineAll(collection: Seq[A]): [A]`，我们很快就会遇到问题，因为回退值将取决于`A` 的类型(对于`String`、`Int`、`””`等等)。

这个问题有一个解决方案，叫做*幺半群*。

我们将在下一篇文章中学习幺半群:
**深入 Scala 猫——幺半群**。

敬请期待！！！

# 参考

我所知道的最好的两个 Scala Cats 资源在这里:

*   猫库可以在 [GitHub](https://github.com/typelevel/cats) 获得
*   本书， [*高级 Scala 与猫*](https://underscore.io/books/advanced-scala/)

# 类似文章-

你也可以看看我关于 Scala Cats 系列的其他文章

*   【Scala 猫入门
*   [潜入斯卡拉猫——幺半群](/diving-into-scala-cats-monoids-82e744b9e518)
*   [潜入 Scala 猫——函子](/diving-into-scala-cats-functors-c957285d7009)
*   [函数编程中的函子](/functors-in-functional-programming-dfaba4cfb2ed)