# Scala 期刊—数据操作(续)

> 原文：<https://levelup.gitconnected.com/scala-journals-part-4-data-manipulation-continued-99622b13c14a>

![](img/ec4756ac9cf20f6fd2b2ca62f36baa26.png)

在这篇文章中，我将通过展示聚集/累积方法——折叠和减少来继续数据操作。在大数据的背景下，您可能听说过“映射并减少”，您已经知道什么是`map`，现在是时候了解一下`reduce`了。

在之前的一篇文章中，我展示了`map`和`flatMap`——将函数应用于给定集合并返回该集合的两种方法。但是，如果我们想聚集或积累一些东西，而不一定要改变我们的收藏呢？换句话说，如果我们想要，例如，得到一个给定集合中所有整数的和，该怎么办？`fold`和`reduce`派上了用场。让我们开始吧。

# reduce 和 fold 有什么区别？

一个简单的答案是:`reduce`累加一个结果，`fold`用一个起始值累加一个结果。如果我们在一个列表上调用`reduce`,我们需要提供一个函数——这个函数将被计算前两个元素，这个结果将被用作下一次计算的第一个参数。调用`fold`是相同的，不同之处在于我们提供了一个起始值，该值将用于第一个元素的第一次评估。

用句子来解释有点复杂，所以在实践中看起来是这样的，请注意说明评估如何发生的注释:

```
val list = List(1, 2, 3, 4, 5)

 list.reduce(_ + _) // 15
 // 1 + 2 (first and second element)
 // 3 + 3 (result of above + third element)
 // 6 + 4 (result of above + fourth element)
 // 10 + 5 (result of above + fifth (last) element)
 // 15

 list.fold(0)(_ + _) // 15
 // 0 + 1 (start value + first element)
 // 1 + 2 (result of above + second element)
 // 3 + 3 (result of above + third element)
 // 6 + 4 (result of above + fourth element)
 // 10 + 5 (result of above + fifth (last) element)
 // 15

 list.fold(1)(_ + _) // 16
 // 1 + 1 (start value + first element)
 // 2 + 2 (result of above + second element)
 // 4 + 3 (result of above + third element)
 // 7 + 4 (result of above + fourth element)
 // 11 + 5 (result of above + fifth (last) element)
 // 16
```

显然，函数`_ + _`可以是对给定集合的两个元素有效的任何函数，这里是:对类型`Integer`的两个元素有效的任何函数。

另外，提醒一下，`_ + _`是元组`(a, b) => a + b`上匿名函数的语法糖

# 什么时候减，什么时候折？

知道何时持有，何时弃牌。

当我看到`reduce`旁边的`fold`时，我的前两个问题是:

1.  如果他们做几乎一样的事情，他们之间真正的区别是什么??我怎么知道什么时候用哪个？
2.  **为什么我需要一个“起始值”？**

让我试着解释一下。

用一个简单的术语来回答第一个问题——当您的结果与集合中的元素类型相同时，使用`reduce`。例如:

*   得到一个`List[Int]`中所有元素的总和——结果将是`Int`
*   获取一个`List[Int]`中所有元素的最大值——结果将是`Int`

当您的结果与集合中的元素类型不同时，或者如果您希望将一个起始值作为第一个元素传递时，请使用`fold`。例如:

*   将整数列表转换为字符串。(`reduce`在这里不起作用，因为它期望最终结果是`Int`，而不是上面提到的`String`)

```
val list = List(1, 2, 3, 4, 5)
list.fold("")(_.toString + _.toString) // "12345" (*)
list.fold("0")(_.toString + _.toString) // "012345"
```

至于关于“起始值”的第二个问题——正如你在上面看到的，在集合中它将被用来告诉编译器我们期望什么类型。如果我们将上面的内容改为:

```
val list = List(1, 2, 3, 4, 5) 
list.fold(0)(_.toString + _.toString) 
// start value is Int but elements in the function are String. Start value type dictates the return type 
// error: type mismatch; found   : String // required: Int
```

…代码无法编译。

# 向左折叠和向右折叠

无需深入细节，有两种`fold`变体:

`**foldLeft**`从左到右“遍历”你的收藏，是*尾递归*和`**foldRight**`从右到左“遍历”，是*头递归*。(还记得尾递归 vs 头递归吗？现在你知道`foldRight`可能会在一个大集合上爆炸！)

`foldLeft`在实践中:

```
val list = List(1, 2, 3, 4, 5)
list.foldLeft(0){ (a, b) => {
    println(s"a ($a) + b ($b)")
    a + b
}} 

// a (0) + b (1)
// a (1) + b (2)
// a (3) + b (3)
// a (6) + b (4)
// a (10) + b (5)
// res19: Int = 15
```

`foldRight`在实践中:

```
val list = List(1, 2, 3, 4, 5)
list.foldRight(0){ (a, b) => {
    println(s"a ($a) + b ($b)")
    a + b
}} 

// a (5) + b (0)
// a (4) + b (5)
// a (3) + b (9)
// a (2) + b (12)
// a (1) + b (14)
// res20: Int = 15
```

你可以看到结果是一样的。现在花点时间想想如果我们的函数不是加法而是减法，结果会有什么不同——结果肯定会不同！此外，知道了头尾递归之间的区别，你能明白如何使用`foldLeft`来尝试并充分利用折叠会更有意义吗？

# 实际上，你多久使用一次？

尽你所能，这是一种快速、无痛苦的计算累积的方法，它可以用来以一种聪明、快捷的方式解决一些众所周知的问题。除了在集合上的工作，`fold`和`reduce`可以作为一种错误处理的形式，因为它可以取代模式匹配(更多关于错误处理的内容在另一篇文章中):

```
Some(12).fold(0)(_ + 10) // 22 None.fold(0)(_ + 10) // 0 List().fold(0)(_ * _) // 0
```

在上面的例子中，你可以看到折叠一个`None`或一个空列表会导致“起始值”的返回。

你也可以使用`fold`来变换`Either`，不管它是`Left`还是`Right`。`fold`在`Either`上的实现方式是模式匹配的捷径:

```
val eitherRight: Either[String, Int] = Right(2) 
val eitherLeft: Either[String, Int] = Left("problem") eitherRight.fold(l => "left", r => r.toString) // "2" eitherLeft.fold(l => "left", r => r.toString) // "left"
```

把上面的折叠想成“如果这是左的，那么计算我传递的第一个函数，但是如果这是右的，那么计算我传递的第二个函数”。

这里也有一些关于如何使用我今天解释的方法解决一些简单问题的想法，在你的头脑中把它们与不使用`fold`或`reduce`的解决方案进行比较。

## 从整数列表中获取最大值

```
val list = List(1, 2, 3, 4, 8) list.reduce(_ max _)
```

## 获得句子中最短的单词

```
val sentence = "Which word is the shortest?" sentence.split(" ").reduce{
   (one, two) => if (one.length < two.length) one
                 else two
   }    // "is"
```

## 还原列表

```
val list = List("anna", "brad", "aga", "steve") list.foldLeft(List[String]()){
    (one, two) => two :: one
 } 
// List[String] = List(steve, aga, brad, anna) 
```

# 最后一个音符

我怎么强调在小代码任务上练习都不为过，比如在[codewars.com](http://codewars.com)上练习，或者在`fold`和`reduce`上练习。这是思考问题的另一种方式——不用深入细节，我可以保证，随着时间的推移，你会发现它们是函数式编程中非常重要的概念。也就是说，有时你会注意到你可以用一个看起来更简单的图案来代替你的`fold`或`reduce`——然后这完全取决于你。就我自己而言，如果我的`fold`或`reduce`看起来过于复杂，我可能会选择看起来更简单的解决方案来提高可读性。

*(*)在这种情况下* `*"”*` *是用于连接操作的字符串的标识元素。*

*当 identity 元素通过操作与任何元素配对时，它返回该元素。*

*在实践中:对于整数的加法，因为 1 + 0 = 1，所以恒等式为 0。对于乘法来说是 1，因为 4 * 1 = 4。对于字符串来说，使用标识可能是一个有问题的选择，但是对于我们这个简单的例子来说，它是有效的。*