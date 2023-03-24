# 注意 Java 集合中的空值

> 原文：<https://levelup.gitconnected.com/watch-out-for-nulls-from-javas-collections-d3a90b939af>

![](img/8d1c6817993b3c9807eb37f976e4d52c.png)

[于(](https://unsplash.com/@yu62ballena?utm_source=medium&utm_medium=referral))在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

通常，当我得到一个`NullPointerException`时，那是因为我在我的 Java 源文件中犯了一些愚蠢的错误，我可以很容易地改正。不过，有时候，这是因为一些 Java 集合允许空值从缝隙中掉出来，直到它们引起问题时才被发现。

考虑一下`java.util.HashMap<K, V>`。它提供了一种非常方便的方法来将键对象映射到值对象。调用 map 的`put()`来添加一个键-值对，调用`get()`来通过键获取 a 值。

举个简单的例子，假设你正在开发一个日历程序，它每天都会给出一段鼓舞人心的引言。您已经创建了一个`Quotation`类，它提供了所有必要的功能，并且符合所有的最佳实践，包括空安全的最佳实践。

程序中的适当类创建了一个`HashMap<LocalDate, Quotation>`。获得今天的灵感引语可能是在散列表上调用`get(LocalDate.now())`的简单事情。

假设`Quotation`类提供了一个`formatAsHTML()`函数，然后您可以使用它将励志名言作为带有适当 HTML 标记的`String`发送给 JavaFX `WebView`。一切都如你所愿。

当散列映射被调用来给出一个没有键的值时会发生什么？它应该做一些类似 throw `NoSuchElementException`的事情(和`HashMap<K, V>`一样，来自于`java.util`大杂烩)。

但事实并非如此。它只是悄悄地返回 null。这几乎总会导致一个`NullPointerException`错误，并带有一个没有价值的异常消息和一个令人困惑的堆栈跟踪。

我在开发我的扫雷器实现时遇到了这个问题([源代码和测试在 GitHub](https://github.com/Alonso-del-Arte/minesweeper) 上)。它最终会有一个图形用户界面。

但是现在，你可以在命令行上玩这个游戏。如果你注意正确地输入位置，你可以玩得很好，例如，如果你想要标记位置 O5，你必须输入“flag o5”。如果你输入“flag 05 ”,程序就会崩溃，并出现一个`NullPointerException`……我必须解决这个问题。

但是在我修复它之前，我必须找出是什么导致了这个异常。我很快就发现`Board`类中的一个哈希映射为越界位置返回空值，而不是我错误地认为的任何类型的异常。

我甚至开始怀疑我是否只是想象了`NoSuchElementException`，也许在 Java 开发工具包(JDK)中没有这样的例外。或者这是 Scala 的事情。

正如你可能知道的，Scala 是一种用于 Java 虚拟机的编程语言。它提供了对整个 JDK 的访问，但它也提供了一些 JDK 类的更好版本，尤其是集合。

Scala 有自己的可变`HashMap[K, V]`，这很像 Java 的`HashMap<K, V>`，也是不可变的`HashMap[K, V]`。

回到灵感引语日历，为了举例，假设我们的不可变散列图有今年除了今天之外的每一天的引语。获取两天前和昨天的励志名言非常简单:

```
scala> scalaImmutableHashMap(twoDaysAgo).formatAsHTML
res8: String = <blockquote>&ldquo;I have not failed. I've just found 10,000 ways that won't work.&rdquo; &mdash; Thomas Edison</blockquote>scala> scalaImmutableHashMap(yesterday)
res9: Quotation = "Unthinking respect for authority is the greatest enemy of truth." -- Albert Einstein
```

但是当我们试图得到今天的报价时，这种情况发生了:

```
scala> scalaImmutableHashMap(LocalDate.now)
java.util.NoSuchElementException
  at scala.collection.immutable.BitmapIndexedMapNode.apply(HashMap.scala:608)
  at scala.collection.immutable.HashMap.apply(HashMap.scala:131)
  ... 28 elided
```

的确`NoSuchElementException`来自 JDK。

如果您愿意，也可以使用 Java 中熟悉的“`.get()`”语法。但是那个总是返回一个`Option[T]`(很像 Java 的`Optional<T>`)，其中类型`T`是相关的类型`V`。

```
scala> scalaImmutableMap(threeDaysAgo)
res14: Option[Quotation] = Some("Optimism is the faith that leads to achievement." -- Helen Keller)
```

所以在今天的日期使用`get()`会返回一个`None`。可变和不可变散列映射都是如此，更广泛使用的`Map[K, V]`也是如此，它既不需要导入也不需要完全限定名。

```
scala> scalaMutableHashMap.get(LocalDate.now)
res24: Option[Quotation] = None
```

但是当`NoSuchElementException`出现在 Scala 的可变哈希映射中时，异常消息会更有帮助:

```
scala> scalaMutableHashMap(LocalDate.now)
java.util.NoSuchElementException: **key not found: 2021-01-13**
  at scala.collection.MapOps.default(Map.scala:246)
  at scala.collection.MapOps.default$(Map.scala:245)
  at scala.collection.AbstractMap.default(Map.scala:376)
  at scala.collection.mutable.HashMap.apply(HashMap.scala:405)
  ... 28 elided
```

注意上面写着“找不到钥匙:2021–01–13”为了便于比较，我们重新构建一个与 Java `HashMap<K, V>`相同的 map。

```
scala> val javaHashMap: java.util.HashMap[LocalDate, Quotation] = new java.util.HashMap
javaHashMap: java.util.HashMap[java.time.LocalDate,Quotation] = {}scala> javaHashMap.put(threeDaysAgo, kellerQuote) 
                                                  // etc., etc.
```

为了从 Java 散列映射中检索值，我们必须使用“`.get()`”语法。

```
scala> javaHashMap.get(fourDaysAgo)
res39: Quotation = "Knowing what must be done does away with fear." -- Rosa Parks
```

将上一条异常消息与下一条异常消息进行比较:

```
scala> javaHashMap.get(LocalDate.now).formatAsHTML
java.lang.NullPointerException
  ... 28 elided
```

因为这是在本地 Scala REPL 上，所以很容易立即知道是什么导致了问题，即使堆栈跟踪几乎是零(28 个省略的堆栈帧是特定于 REPL 的)。

这个异常不会把我们踢出 REPL，但是我的扫雷程序中的一个异常会把我们踢出游戏。这就好像，即使你玩得很好，你仍然发现并引爆了一个地雷位置，而不是标记它。

堆栈跟踪确实给出了一些问题的指示，但是问题出在一个过程中，我已经允许它增长到大约四十行。这是另一个喜欢短单元的原因。我需要把`processCommand()`分成更小的单元。

但是为了解决游戏因玩家的小错误而崩溃的直接问题，我重写了`Board`类，这样当它的实例被要求在界外的位置操作时就会抛出`NoSuchElementException`。

然后我重写了`ui.text.Game`,这样它除了通知玩家之外，不会对界外位置做任何事情。我仍然有工作要做的程序，显示相邻的空位置，但除此之外，我很满意我目前所得到的。

当然，这对于有图形用户界面的游戏来说不是问题。玩家可以随心所欲地点击出界，但除了消耗他们的游戏时间之外，不会有任何其他影响。

下次当你在 Java 程序中遇到`NullPointerException`时，不要急于责怪自己。考虑它可能来自 JDK 或第三方库的可能性。