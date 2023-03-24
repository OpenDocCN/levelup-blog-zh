# 也许现在还不要放弃 Scala

> 原文：<https://levelup.gitconnected.com/maybe-dont-write-off-scala-just-yet-f0c128a570f0>

![](img/a485ac3a8c62f7229db61edc3b9ffc6a.png)

安妮·尼加德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

作为程序员，我们关心其他程序员用的是什么编程语言。Scala 显示 PYPL 指数下降了 0.2%。谷歌和 YouTube 上的趋势也令人沮丧。所以，应该没人会费心去学 Scala 吧？

从长远来看，Java 目前在 PYPL 排名第二，尽管下降了 3.1%，Kotlin 排名第 12，增长了 0.1%。

一个不可避免的结论似乎是，如果你喜欢 Java 虚拟机(JVM)，那么 Java 是值得学习的语言，Kotlin 和 Scala 都不值得学习。

但是 Java 本身是一种已经被淘汰了很多次的语言，也许从它存在的时候就开始了。Java 并没有像它的名字一样无处不在，表面上相似的 JavaScript。然而现在 JavaScript 在 PYPL 排名第三，仅次于 Java。

如此狭隘地关注一门编程语言的趋势就是忽略了它是什么，它是用来做什么的，它擅长什么，它不擅长什么。

例如，考虑 COBOL。它显示 PYPL 略有上升趋势。也许如果冠状病毒疫情被处理在一个关心和有能力的方式，COBOL 会显示在 PYPL 略有下降。

有些人会惊讶于它竟然在 PYPL 上。但是，不管趋势如何，今天仍然使用 COBOL 程序的理由不会因为当前的事件而神奇地消失。

有一个关于是否是时候将旧的大型机从 COBOL 中移走的争论。在我对 COBOL 了解不多的情况下，我不想参与这场辩论。

那么，Scala 是什么？它是用来做什么的？它擅长什么？它不擅长什么？

Scala 是一种面向对象的编程语言，具有很多函数特性。也许将 Scala 视为某种 JVM Haskell 的观念损害了它的流行。Scala 不是那样的。

Scala 是由 Martin Odersky 发明的，他编写了 Java 1.3 编译器。但是 Java 并没有像他希望的那样快速发展。因此 Scala 是 Odersky 所希望的 Java，甚至更多。

这意味着 Scala 可以用于 Java 可以用于的任何东西，它可以使用任何 Java 库。Java 程序也可以使用 Scala 库，尽管有一些注意事项。

我使用 JUnit 来测试 Scala，我还没有找到使用 ScalaTest 的理由。想必 Kafka 可以配合 Scala 使用，但是 Scala 程序员似乎更喜欢 Akka，Java 程序也可以使用。

尽管 Scala 是一种通用语言，但它似乎因主要用于大数据和机器学习而闻名。

对于一些事情，使用 Scala 是不可取的或者几乎是不可能的。有些人使用 Scala 进行 Android 编程，但是当我尝试的时候，我遇到了太多的问题。Android 编程最好用 Java 或 Kotlin。

我也建议不要尝试在嵌入式系统中使用 Scala。可能对操作系统也没什么好处。但是话说回来，我也不建议将 Java 用于嵌入式系统或操作系统。

如果我们要放弃一种编程语言，我们至少应该看一个用这种语言写的小程序，一个玩具例子。没什么太复杂的，只是比你好世界稍微复杂一点。

假设您想要一个按 ISO-4217 中 3 个字母的代码排序的世界货币列表，并且您还想要一个按数字代码排序的列表。不知道为什么会有人想要这样，但是在 Scala 中这样做很容易。

Java 运行时环境提供了一个货币列表，因此我们可以通过导入 Java 包来访问这些货币。

```
import java.util.Currency
import scala.jdk.CollectionConverters._val currencies = Currency.getAvailableCurrencies.asScala.toList
currencies.sortBy(_.getSymbol)
currencies.sortBy(_.getNumericCode)
```

我在[的一个 Scastie 片段](https://scastie.scala-lang.org/lXKH6wbzS2yl5UGoV4F3MQ)中有这个，你可以从 Firefox 或 Chrome 访问，可能还有 Edge 和 Safari。

第一个排序列表以美元(USD)开始，这是错误的——但这是我的错，我忘记了`getSymbol()`取决于地区，在我的例子中，USD 是“$”，因此排在所有其他的前面，甚至是安道尔比塞塔(ADP)和阿联酋达拉希姆(AED)。

我应该用`toString()`而不是`getSymbol()`。如果您在另一个选项卡中打开了 Scastie 片段，我建议您进行更改，单击“Save ”,然后看到 USD 被移到列表的后面。

第二个排序列表由法国 UIC 法郎(XFU，0)、法国黄金法郎(XFO，也是 0)、旧阿富汗阿富汗尼(AFA，4)、阿尔巴尼亚列克(ALL，8)、阿尔及利亚第纳尔(DZD，12)等组成。

显然，我们也可以用 Java 来实现这一点。但是我们必须创建两个不同的比较器，即使我们将它们实现为匿名类，这也会非常冗长。如果我们想按其他字段排序，还需要更多的比较器。

大多数编程语言彼此非常相似。也许你不需要再为装载机编程了。但是你可能会发现自己正处于一种需要对湿气蒸发器进行编程的情况。

因此，我不会放弃负载升降机的二进制语言。也许有一天银河系会依赖它。