# 破解组合递归之谜

> 原文：<https://levelup.gitconnected.com/cracking-the-combination-recursive-riddle-64b92d99c6f5>

![](img/277b553391d315f1bbca2412fee35388.png)

*原载于【https://edward-huang.com】[](https://edward-huang.com/functional-programming/programming/scala/algorithm/jobs/2020/02/14/cracking-the-combination-recursive-riddle/)**。***

*在 19 世纪后期，数学家使用归纳法定义函数的原理。阿砣·戴德金德(1889 年)使用归纳法定义并证明了他的关于正整数的第五公理。后来，他的正整数的第五个原理被称为原始递归。从那以后，递归的概念在数学的基础中起了重要的作用。*

*归纳法证明是一种通过证明计算的每一步来证明概念、理论或算法有效的方法。事情是这样的——你可以证明一个任意的陈述 n，首先证明当 n 为 1 时该陈述为真，然后假设当 n = k 时它也为真，并证明它对 n = k+1 有效。*

*递归源于归纳的概念。递归函数的工作原理是通过调用自身将问题分解成更小的部分。然后，是基本情况，即 n = 1 时。*

*因此，通过将更重要的问题语句分解为更小的问题，可以推导出很多算法逻辑。而组合就是其中之一。*

*当我学习 Scala 编程语言和函数式编程时，我意识到函数的每个调用都是递归的。你想循环遍历一个列表并得到列表的最后两个元素，你必须使用模式匹配并不断调用你自己，直到它遇到列表的最后一个元素，并将最后一个元素返回到先前的调用堆栈。*

*组合问题是我们在软件工程师面试问题中经常遇到的经典递归问题。当受访者屏住呼吸，绞尽脑汁思考如何着手解决问题时，问题就来了。因此，对于一个采访者来说，这是一个正确的问题，通过他们的受访者的大脑，看看他们如何解决一个复杂的问题。*

*让我们来做一个例子——创建一个组合函数，返回整数列表的所有可能组合。*

```
*val lst = List(1,2,3)*
```

*如果我们将列表分解为只有 1 个元素' List (1 ' ',它可能有哪些组合？*

# *选择还是不选择*

*答案是 2。为什么？因为你可以选择其中一个元素，或者不选择。*

*根据这一观察，我们现在可以开始弄清楚这个概念如何转化为代码。*

```
*def combination(len:Int, i:Int lst:List[Int]):List[List[Int]] = {
  *// here you either choose the value or you didn't
*  val includedList = lst.head :: combination(len, i+1, lst.tail)   val notIncludedList = combination(len, i+1, lst.tail) *// skip the current head* }*
```

*注意，在上面的代码中，不管发生了什么，index `i`的值总是递增，将指针移动到列表中的下一个元素。在一次递归调用中，我们包含了选择的值`lst.head`。另一方面，我们不包括`lst.head`，跳过当前值。*

*每个递归函数都需要有一个基本用例。当我考虑基本情况时，首先想到的是当列表为空时会发生什么？我应该返回什么样的值？*

*在这种情况下，如果列表为空，函数应该返回一个空列表，因为我们没有什么可选择的。*

*函数变成了这样:*

```
*def combination(len:Int, i:Int, lst:List[Int]):List[List[Int]] = {
  if(len == i) {
    List(Nil)
  }
  else {
      val includedList = combination(len, i+1, lst.tail).map(lst.head :: _) *// choose the current head
*      val notIncludedList = combination(len, i+1, lst.tail) *// skip the current head
*      includedList ::: notIncludedList
  }
}*
```

*上面的代码在技术上是可行的，因为它产生了正确的输出。然而，在 Scala 中，我们可以省略`i`值，通过遍历列表的`tail`来遍历列表。让我们更简洁地重构上面的工作解决方案。*

```
*def combination(lst:List[Int]): List[List[Int]] = {
  if(lst == Nil) {
    List(Nil)
  } else {
    val includedList = combination(lst.tail).map(lst.head :: _)
    val notIncludedList = combination(lst.tail)
    includedList ::: notIncludedList
  }
}*
```

*并将其与模式匹配相结合:*

```
*def combination(lst:List[Int]) : List[List[Int]] = lst match {
  case Nil => List(Nil)
  case h:rest => combination(rest).map(h :: _) ::: combination(rest)
}*
```

*这将是查找列表中所有子集的最简单形式。所以`List(1,2,3)`才会有`List(List(1), List(2), List(3), List(1,2), List(1,3), List(2,3), List(1,2,3), List())`。*

*就是这样！这是组合的基本结构。大多数其他组合问题在进一步计算算法时涉及一些约束。大多数动态规划算法都可以从组合算法中派生出来。*

*例如，流行的[背包问题](https://en.wikipedia.org/wiki/Knapsack_problem)，如果袋子没有装满，你要么选择这个当前值包含在一个集合中，要么跳过它。一旦你找到了所有的组合，你就从你选择的组合中计算出了最大的价值。我们可以通过创建一个迭代方法来进一步导出所有的模式，或者用`LazyList`来记忆计算。*

# *外卖:*

*   *用归纳法从证明中推导出一个递归算法。*
*   *所有迭代计算解都可以从递归函数中导出。*
*   *组合的两个主要活动是选择或不选择。*

# *引人深思的事*

*在观察阶段，`lst.head`与组合的其余部分一起前置。然而，在解决方案的最后，我使用了`map`函数在结果中添加头部。有区别吗？*

*本教程所有的源代码都是[这里](https://github.com/edwardGunawan/Blog-Tutorial/tree/master/ScalaTutorial/combinationTutorial)。*

## *感谢阅读！如果你喜欢这篇文章，请随意[订阅](https://edward-huang.com/subscribe/)我的时事通讯，接收关于科技职业的每周文章、有趣的链接和内容！*

*你可以关注我，也可以关注我在[媒体](https://medium.com/@edwardgunawan880)上的更多帖子。*