# Scala 期刊—数据操作

> 原文：<https://levelup.gitconnected.com/scala-journals-part-3-data-manipulation-289be03a8b0>

![](img/51a8c4646f9d28aecd96e3e6bca39111.png)

数据操作和转换是一件大事。既然我已经分享了递归的重要性，但也提到了实现某些目标的其他方法，那么让我们深入研究一下。这将是关于数据操作和转换的迷你系列的第一篇文章。我现在将主要关注**集合**——这个主题还有其他重要的方面，但是让我们首先从最直观的东西开始。

记住这一点，让我们快速回忆一下 Scala 基本集合:

*   目录
*   地图
*   一组
*   元组
*   选项(要么是空的，要么只有一件物品，从阿尔文·亚历山大[到这里](https://alvinalexander.com/scala/using-scala-option-some-none-idiom-function-java-null))

我发现我通常想对集合做一些基本的事情(除了获取大小等琐碎的事情之外)。):

*   获取符合特定条件的项目
*   对每一项都做些什么
*   对满足特定条件的项目做某事
*   检查是否有满足特定条件的项目

记住这一点，让我们从`map`、`flatMap`、`collect`和`filter`开始。

# 地图

你可以在所有的收藏上调用`map`。它将传递的函数应用于每个元素，并返回转换后的元素的集合。

```
def divideByTwo(number: Int) = number / 2  
List(10, 20, 30).map(divideByTwo) // List(5, 10, 15)
```

…这个函数也可以是匿名的，你会**非常**经常看到这样的代码:

`List(10, 20, 30).map(nr => nr / 2) // List(5, 10, 15)`

…或者更常见的是对匿名函数使用语法糖，如下所示:

`List(10, 20, 30).map(_ / 2) // List(5, 10, 15)`

当然，`map`中使用的函数可以是从(在我们的例子中)`Int`到任何其他类型的函数——天空是你的极限。所以让我们试试从`Int`到`String`的函数？

`List(10, 20, 30).map(_.toString) // List("5", "10", "15")`

**总结**你可以把`map`想成“把这个函数应用到这个集合的每个元素上，并返回结果”。

**重要提示—选项**的地图

`map`对`Option`有特殊的用法——它只会执行`Some`上传递的功能:

```
val someString: Option[String] = Some("hello") 
val noneString: Option[String] = None someString.map(_.length) // Some(5) 
noneString.map(_.length) // None
```

您已经看到它对于错误处理是多么方便了吗？将会有一个关于错误处理的独立帖子，而`Option`将会是这一集的明星之一。

# 平面地图

`flatMap`无非是先叫`map`再叫`flatten`。为什么会有用？

在集合中，`flatMap`是一个救命稻草，当你发现自己有一种嵌套类型，比如`List[List[_]]`或者`Option[Option[_]]`。

假设我们得到一个非直观类型的用户列表`List[List[String]]`:

`val allUsers = List(List("ann", "betty"), List("caro", "ciara"))`

假设我们需要将所有的名字列为一个列表，但是所有的名字都要大写。为了让最终代码更具可读性，我们可以先创建一个小函数:

`def listOfStringUpCase(list: List[String]) = list.map(_.toUpperCase)`

所以我们现在可以`map`，对吗？

```
val upperCasedListsOfUsers = allUsers.map(listOfStringUpCase) 
// List(List("ANN", "BETTY"), List("CARO", "CIARA"))
```

…不幸的是，这又给我们留下了一个`List[List[String]]`。所以现在是时候`flatten`——“合并”我们嵌套的`List`并以单个`List`结束:

```
upperCasedListsOfUsers.flatten 
// List(ANN, BETTY, CARO, CIARA)
```

但是`flatmap`呢？我们可以用`flatMap`代替所有这些噪音，得到一个非常直观和优雅的解决方案:

```
allUsers.flatMap(listOfStringUpCase) 
// List(ANN, BETTY, CARO, CIARA)
```

**总结**
你可以把`flatMap`想成“把这个函数应用到这个集合的每个元素上，然后把结果扁平化，返回转换后的集合”。

**重要提示—用于**和`**List[Option[_]]**`的平面图

`flatMap`对`List[Option]`有一个特殊的用法——它将`None`的值“移除”(flatten):

```
List(Some("hello"), None, None, Some("world")) .flatMap(_.map(_.toUpperCase))  
// List(HELLO, WORLD)
```

..不像`map`:

```
List(Some("hello"), None, None, Some("world")) .map(_.map(_.toUpperCase))  
// List(Some(HELLO), None, None, Some(WORLD))
```

但是对于上面的`map` -如果我们随后使用`flatten`，结果显然与上面的`flatMap`相同

```
List(Some("hello"), None, None, Some("world")) .map(_.map(_.toUpperCase)).flatten 
// List(HELLO, WORLD)
```

# 过滤器

这很简单— `filter`做了您认为它会做的事情——它返回满足给定谓词的项目:

```
List(1, 2, 3, 4).filter(_ > 3) // List(4)
```

# 收集

一个`filter`和`map`的“混合体”。语法可能看起来有点复杂，因为`collect`采用了部分函数(关于部分函数的帖子将很快发布):

```
List(1, 2, 3, 4).collect {     
   case i if(i > 3) => i * 3 
 }  
// List(12)
```

**摘要**
您可以将`collect`视为“从该集合中过滤值，并对匹配的值应用该函数”

## 实际上，你多久用一次这些来代替循环等等？

**每一天**，无一故障。我认为`map`、`flatMap`和`filter`是你最先学习的东西。我记得我的第一个 Scala 代码，我所说的第一个是指第一行。我准备好了这个华丽的 for 循环，有人在我的代码审查中指出，我的 9 行代码可以替换为不到 10 个字符…当然，我被说服了！

这里有一些关于如何使用我今天解释的方法解决一些简单问题的想法，在你的头脑中把它们与不使用`map`、`flatmap`、`filter`或`collect`的解决方案进行比较。下面的解决方案更简洁，不是吗？

## 列出所有奇数

```
val list = List(1, 2, 3, 4) list.filter(_ % 2 != 0)
```

## 找出句子中最短单词的长度

```
val sentence = "How long is the shortest word?" 
sentence.split(" ").map(_.length).min 
// 2
```

## 复制列表中的所有元素

```
val list = List(1, 2, 3, 4) list.flatMap(element => List(element, element))
```

## 将所有以 A 开头的名称改为大写，并只返回它们

```
val list = List("anna", "brad", "aga", "steve") 
list.collect {     
  case name if(name.startsWith("a")) => name.toUpperCase 
 }
```

## 最后一个音符

当然，如果你想解决以上所有问题，你也可以使用 for 循环和命令式方法。不过，我永远不会鼓励这样做。Scala 提供了极其强大的集合 API，这就像把扫雪机挂在法拉利上一样。就我自己而言，我想充分利用 Scala，我喜欢代码看起来如此干净，你可以得到如此丰富的表达。拥有这些一行解决方案是多么令人满意…