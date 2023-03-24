# 如何在 Scala 中实现函数列表

> 原文：<https://levelup.gitconnected.com/how-to-implement-a-functional-list-in-scala-203131815d12>

![](img/f63eda9e90aba9ce8301a4c7401cfa13.png)

最初发表于[https://edward-huang.com](https://edward-huang.com/functional-programming/scala/2020/04/14/how-to-implement-functional-list-in-scala/)

函数式编程近来越来越受欢迎。函数式编程与命令式编程的一个重要区别是，您不能更新变量或修改可变的数据结构。让所有数据都不可变会有很多好处。

然而，在带来巨大好处的同时，也伴随着一些成本。例如，将这种范式应用到您的团队中是一个更高的学习曲线，也是您如何定义数据结构的另一个范式转变。

当我们需要定义一个函数式数据结构时，会出现许多问题——我们如何定义数据结构并对其进行操作？我们如何使每一个数据结构变得纯净？

在这篇文章中，我喜欢分享如何在 scala 中定义一个广泛使用的数据结构，List，它相当于 Java 中的一个单向链接的 List。我们从如何将数据结构视为不可变的思考过程开始，并继续定义`next`函数，它将列表中的一个节点链接到下一个节点。

# 直觉

函数式数据结构使用纯函数进行操作——它不能就地改变数据，也不会产生任何副作用。因此，为空列表(Nil)创建一个函数列表对于其不变性来说与整数 3 或 4 是一样的。当我们对一个整数进行运算时，比如说`3+4`，我们会得到一个新的整数`7`。我们没有改变整数 3 或整数 4 的值，因为它是不可变的。

功能列表是相同的。

当我们将两个列表`List(1) ++ List(2)`连接在一起时，我们将返回一个新的`List(1,2)`列表，而不改变`List(1)`或`List(2)`。

这是否意味着从前两个操作到新操作有很多深层拷贝？

令人惊讶的是，事实并非如此。

因为每个元素都是不可变的，所以我们可以重用它。当我们将两个列表连接在一起时，我们通过让一个列表指向另一个列表来返回一个新列表。叫做[数据共享](https://stackoverflow.com/questions/16430376/what-does-sharing-refer-to-in-the-implementation-of-a-functional-programming-lan)。通过这样做，我们可以更有效地实现函数，而不必担心下面的代码会修改我们的数据。

通常，数据结构是递归的——当数据结构为空时，它们有一个基本情况，如`Nil`表示空列表，`Leaf`表示树。然后，它们有另一种类型，表示由值组成的数据结构。对于 List，它是`Cons`，包含一个成员变量和一个将当前节点连接到下一个节点的函数。在二叉树的情况下，`Branch`相当于树中具有`left`和`right`并连接到下一个`Branch`的节点。我们用这种直觉来建立我们的函数列表。

# 履行

我们如何实现一个列表？

在 Java 中，我们创建一个类 LinkedList 和一个在 LinkedList 内部操作的内部类节点。该节点有一个指向下一个节点的`next`成员变量。

我们通过创建 LinkedList 来创建函数列表，这是一个`MyList`。

```
sealed trait MyList[+A]  {
    *// create a right associative function
*    def ::[B >: A](a:B):MyList[B]
}
```

我们创建顶层 MyList 空列表，`Nil`和`Cons`将实现。创建`sealed trait`的原因是为了让我们的列表模式稍后可以与`Cons`和`Nil`匹配。另一个原因是创建一个 sum 类型 [ADT](https://edward-huang.com/functional-programming/2019/12/30/what-is-an-adt-algebraic-data-types/) 。

让我们创建`Nil`:

```
case class Cons[+A](head:A, tail:MyList[A]) extends MyList[A] {
  override def ::[B >: A](a: B): MyList[B] = Cons(a, head :: tail)
}
```

和`Cons`:

```
case class Cons[+A](head:A, tail:MyList[A]) extends MyList[A] {
  override def ::[B >: A](a: B): MyList[B] = Cons(a, head :: tail)
}
```

注意`Cons`和`Nil`都伸出`MyList`。`Cons`中的 head 相当于 Java 中的值 Node，`tail`是指 Java 中节点中的 next。

从上面的代码中可以观察到，当我们定义`::`时，有一种递归的性质。当我们定义`5 :: List(3)`时，`Cons`递归调用`tail`上的`::`，直到它碰到基础用例`Nil`。`Nil` l 通过将`::`中的参数附加到`Cons(a,Nil)`来创建`Cons`。

List 中的递归性质在其他函数数据结构中也是如此。基本类型`Nil`调用中间类型`Cons`，并从中间类型构造基本元素。中间函数递归地调用自己，并让基本类型处理基本场景。这两种类型都扩展了由数据结构组成的特征。

让我们为新列表的数据构造函数创建`MyList`:

```
object MyList{
  def apply[A](a:A*): MyList[A] = if(a.isEmpty) Nil else Cons(a.head, apply(a.tail: _*))
}
```

然后，我们可以尝试在 main 函数中测试我们的列表:

```
object Main extends App {
  val a = 5 :: Nil
  val b = 7 :: 6 :: a
  println(b)

}
```

# 主要外卖

*   函数式数据结构使用数据共享，而不是每次对它们进行操作时都进行深度复制。
*   数据结构是递归的。总有基本类型和中间类型将容器连接起来形成一个新的结构。
*   通常，基类型通过实现中间类型的函数来创建自己的函数。在列表的情况下，`Nil`从`Cons`创建`::`。

本教程上的所有源代码都是[这里](https://github.com/edwardGunawan/Blog-Tutorial/blob/master/ScalaTutorial/functionalDS/src/main/scala/MyList.scala)。

**感谢阅读！如果你喜欢这篇文章，请随意订阅我的时事通讯，每周都会收到关于科技职业的文章、有趣的链接和内容。**

你也可以在 [Medium](https://medium.com/@edwardgunawan880) 上关注我更多类似的帖子。