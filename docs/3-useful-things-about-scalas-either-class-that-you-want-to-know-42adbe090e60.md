# 关于 Scala 的两个类，你想知道的 3 件有用的事情

> 原文：<https://levelup.gitconnected.com/3-useful-things-about-scalas-either-class-that-you-want-to-know-42adbe090e60>

![](img/56217538ec2523d5a99a1b35e4c5078f.png)

【https://edward-huang.com】原载于[](https://edward-huang.com/monad/programming/functional-programming/scala/2020/03/19/3-useful-things-about-either-that-you-want-to-know/)

*这是我在学习 Scala 时学到的一种新的类。随着我对函数式编程了解的越来越深入，越来越深刻，要么不仅仅是一个存储 left 和 right 两个值的容器，而是一个单子。*

*使用任一类型构造函数的原因之一是在运行程序时不会产生任何令人惊讶的输出。没有这两者，你的功能是不可预测的。抛出异常时，您可能会遇到一些意想不到的副作用。例如，不是意外地爆发异常，而是可以返回左(失败情况)或右(成功情况)。因此，该函数对调用者来说是可预测的，让调用者知道可能发生的潜在结果。*

*在本文中，我想分享一些有用的特性，我认为了解函数式编程是有益的。*

# *两者都是单子*

*在 Scala 2.1.1 和更早的版本中，许多人都不认为是单子，因为它没有 map 和 flatMap 方法。*

*这使得很难组成用于理解的序列。*

```
*val either1: Either[Exception, Int] = Right(1)
val either2: Either[Exception, Int] = Right(2)

for {
  one <- either1.right
  two <- either2.right
} yield one + two*
```

*然而，在 Scala 2.1.1 中，两者都被重新设计成具有类似函数的特性。*

*现代人要么认定右边是成功案例。因此，它支持地图和平面图。*

*这让理解变得更加愉快:*

```
*val either1 : Either[Exception,Int] = Right(1)
val either2: Either[Exception, Int] = Right(2)

for{
  one <- either1
  two <- either2
} yield one + two*
```

*它要么从不偏不倚的类型转变为右偏。*

# *两者都是右倾*

*要成为 Monad 类型，您需要能够对值应用 map 或 flatMap。然而，在 [ADT](https://edward-huang.com/functional-programming/2019/12/30/what-is-an-adt-algebraic-data-types/) 中，两者都被归类为 Sum 类型。这意味着它有两个可能的值，一个右，一个左。另一个类似的单子类型包括未来(成功，失败)，选项(一些，没有)。这些单子持有两个值，通常是快乐和悲伤的场景。*

*该函数应该应用哪个值？*

*这就是右倾偏见出现的原因。右偏意味着像`map`和`flatMap`这样的函数只适用于“右侧”或“快乐场景”，而不影响另一侧。*

*直到 Scala 2.12，两者都是无偏的，这意味着函数的`map`和`flatMap`不知道应用哪个值。你必须使用“右可投影”来使其右偏并且*可平面映射*。*

*无偏差适合于验证功能。但是，对于右偏，这两种方法都可以非常方便地创建操作序列。*

*例如，以验证一个对象为例。*

```
*case class CarEngine(sound: String)*
```

*你想验证这个引擎声，看看是不是一辆车。如果不是`vroom`的声音，可以通过抛出一个错误来完成。然后，如果它是一个有效的汽车引擎，您希望返回该对象的实例。*

```
*def validCarEngineSound(car:CarEngine): Either[Exception, CarEngine] = {
  car.sound.toLowerCase match {
    case "vroom" => Right(car)
    case _ => Left(new Exception("not a valid car sound"))
  }
}*
```

*现在，右偏的好处是在验证之后做一些其他的操作，推迟错误处理。*

*假设您想要连接`CarEngine`的声音并将其添加到 Toyota 类，因为 Toyota 需要将其汽车引擎声音限制为某种方式。*

```
**// TOYOTA class define somewhere in the class* val engine = CarEngine("vroom")
val result = validCarEngeinSound(engine).map{ case CarEngine(sound) => wireToToyota(sound) }

result match {
  case Left(err) => println(err.getMessage)
  case Right(_) => println("The engine is correct and is wired to Toyota")
}*
```

*如果该值无效，那么它将延迟该值并返回结果。然而，如果它成功了，那么它就执行`map`函数中的值。*

# *折叠*

*最后一个是在任何一个上折叠列表的操作都很棘手。*

*举个例子:*

```
*List(1,2,3).foldLeft(Right(0)) { (accumulator, num) => 
  if(num > 0) {
    accumulator.map(_ +1)
  } else {
    Left("Negative. Stopping!")
  }
}*
```

*因为 Left 和 Right 都是其中一个的子类型，所以它抛出一个错误，指出该类型不匹配。因此，您需要为`Right.apply`指定类型参数，这样编译器就可以将左边的参数推断为`Nothing`。*

*最好是你来铸造它们。Cats 提供了一个智能构造函数，用`asRight`将左右值包装成任意一个。*

```
*implicit class EitherOps[A](v:A) {
  def asLeft[B]: Either[A,B] = Left(v)
  def asRight[B]: Either[B,A] = Right(v)
}

List(1,2,3).foldLeft(0.asRight[String]) { (accumulator, num) => 
  if(num > 0) {
    accumulator.map(_ +1)
  } else {
    Left("Negative. Stopping!")
  }
}*
```

*另一个有用的 foldLeft 提供了一个左和一个右的场景。*

*这取自 scala 库函数定义。*

```
*type Either[+A,+B]
def fold[C](fa:A => C, fb:B => C):C*
```

*当值为 a 时，它应用一个`fa`,当值为 b 时，它应用`fb`。当您想在不进行模式匹配的情况下进行左右赋值时，这很方便。*

# *外卖食品*

*   *两者都是 monad，具有 map 和 flatMap 功能。我们现在没有注意到，在它变成单子之后，它变得多么方便。*
*   *任何一个都是右偏的，这意味着如果值是“正确的”或“令人满意的场景”，方法就可以执行`map`和`flatMap`。它没有触及“左”的场景。*
*   *文件夹是编写任一模式匹配函数执行的另一种方式。*

# ***感谢阅读！如果你喜欢这篇文章，请随意订阅我的时事通讯，每周都会收到关于科技职业的文章、有趣的链接和内容。***

*你也可以在 [Medium](https://medium.com/@edwardgunawan880) 上关注我更多类似的帖子。*