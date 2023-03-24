# 10 分钟学会 Scala 基础知识

> 原文：<https://levelup.gitconnected.com/learn-scala-basics-in-10-minutes-7a16d6597a51>

## 你还不会成为 Scala 大师，但是你会一路走好的。

![](img/1e1cbfe939c0a97f5e2367e4173bad2d.png)

马修·布朗在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

S 卡拉可以是一种美丽而又富有挑战性的语言，当我发现自己不得不再次学会它的时候——这一次可能几乎每天都在使用它——我想我应该与世界上的其他人分享一些知识。现在，不要假设在本文结束时你已经了解了 Scala。任何承诺在几分钟内学会任何东西的人——尤其是在编程方面——都是在撒谎。但是，您将学习一些基础知识，并可能自己决定语法是否符合您的喜好，以及您希望在后端使用的语言。

Scala 当然不是唯一一种在后端使用的语言。外面有很多。我个人最喜欢的是 JavaScript (Node.js)，但这只是因为我用得最多。我在后端使用的其他语言是 Python 和 PHP 之类的。大约 10 年的编程生涯之后，我对任何语言都没有宗教信仰，只是尝试使用正确的工具来完成正确的工作，包括编程语言。

你会发现这种语言相当新，是马丁·奥德斯基在 2004 年想出来的。这使得它在 2022 年只有 18 岁。总的来说，这使得 Scala 成为一门年轻的语言——尽管与 Rust、Kotlin 或 Dart 等比它年轻一半的语言相比已经足够成熟。相比之下，C 已经 50 岁了，JavaScript 和 PHP 都是 26 岁，就这一点而言，让我们不要再浪费时间去研究它了。

## 变量和基本类型

Scala 是一种类型化语言，就像 Dart 和 TypeScript 一样，尽管我认为后者更像是一种编译器，而不是真正的语言。使用类型化语言有很多好处，尤其是在 Scala 中。

*   **隐式文档:**将鼠标悬停在 IDE 中几乎任何一行代码、变量、方法、表达式上，都会告诉您几乎所有需要了解的信息，输入、输出等等。省去了你写大量的评论。也就是说，这不是不为业务逻辑或软件的棘手部分编写文档的借口。
*   **错误预防:**确保你的软件中使用了正确的类型，这对于预防与类型相关的错误非常有帮助。它是一种编译语言，所以看到它编译已经是一个很好的迹象，表明你在正确的道路上。这也意味着，随着时间的推移，重构和维护变得更加容易。
*   **更少的测试:**我觉得这么说有点奇怪——也就是说更少的测试是一种好处——但是对于 Scala 来说确实是这样，如果你精通这门语言及其库/框架，你可以通过编写更少的测试来节省一些开发时间。

有了上面的背景，与 JavaScript 相比，每个变量类型都是类型良好的就不足为奇了。

```
val x: Int = 22
val greeting: String = "hello world"
val isFalse: Boolean = false
val singleCharacter: Char = 'q'
val year: Short = 2022
val infinity: Long = 34566666677777L
val float: Float = 3.0f
val pi: Double = 3.14
```

在上面的代码片段中需要注意的一点是，**`**val**`**符号意味着它们实际上是常数**，它们的值不能改变，它们是不可变的。如果您希望覆盖或更改变量的值，您必须使用`var`符号，如下所示:**

```
var greeting: String = "see you later"
```

**如果您想知道变量/常量的名称和类型之间是否需要空格，那么答案是否定的。`**greeting: String**` **和** `**greeting:String**` **实际上是一回事。****

**关于这些基本类型的最后一个趣闻是，它们大多数都有位长，记住这一点很重要，以确保使用正确的类型，并且我们的软件不会在以后失败:**

*   ****布尔:**二进制，所以非真即假**
*   ****字节:** 8 位有符号二进制补码整数(-2⁷到 2⁷-1，含)-128 到 127**
*   ****短:** 16 位有符号二进制补码整数(-2 ⁵到 2 ⁵-1，含)-32，768 到 32，767**
*   ****Int:** 32 位二进制补码整数(-2 到 2 -1，含)-2，147，483，648 到 2，147，483，647**
*   ****Long:** 64 位二进制补码整数(-2⁶到 2⁶ -1，含)-9，223，372，036，854，775，808 到+9，223，372，036，854，775，807**
*   ****浮点:** 32 位 IEEE 754 单精度浮点 1.40129846432481707e-45 到 3.40282346638528860e+38(正或负)**
*   ****Double:** 64 位 IEEE 754 双精度浮点 4.94065645841246544e-324 到 1.79769313486231570e+308(正或负)**
*   ****Char:** 16 位无符号 Unicode 字符(0 到 2 ⁶-1，含 0 到 65，535)**
*   ****字符串:**字符序列**

## **字符串操作**

**信不信由你，字符串操作是我在编程中最喜欢的事情之一。不管是哪种语言。不要问我为什么。我只是觉得它们非常直观和放松。功能将是我下一个最喜欢的东西，但那是另一篇未来的文章，所以如果你也想在你的收件箱里看到那个故事，那么[订阅](https://attilavago.medium.com/subscribe)和[成为会员](https://attilavago.medium.com/membership)！**

**[](https://attilavago.medium.com/membership) [## 通过我的推荐链接加入 Medium-Attila vágó

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

attilavago.medium.com](https://attilavago.medium.com/membership) 

假设 Scala 运行在 JVM 上，那么对于初学者来说，默认情况下，您会得到很好的 ol' Java 字符串操作符:

```
val str: String = "Hello, you are learning Scala"

println(str.charAt(2)) // > l
println(str.substring(7, 11)) // > you
println(str.split(" ").toList) // > List(Hello,, you, are, learning, Scala)
println(str.startsWith("Hello")) // > true
println(str.replace(" ", "-")) // > Hello,-you-are-learning-Scala
println(str.toLowerCase) // > hello, you are learning scala
println(str.toUpperCase) // > HELLO, YOU ARE LEARNING SCALA
println(str.length) // > 29

val numberString = "9"
val number = aNumberString.toInt 
println('a' +: aNumberString :+ 'z') // > a9z
println(str.reverse) // > alacS gninrael era uoy ,olleH
println(str.take(4)) // > Hell
```

现在，如果你以前用其他语言编写过代码，你会说，*好了，好了，这里没什么新的了，继续，*所以这些不会让你太无聊(还有一些)，但是会跳到 Scala 特有的，这将很快被证明是有用的，尽管，再次强调，不是唯一有这些的语言。例如，JavaScript 最近刚刚在其功能数组中添加了模板文字。在 Scala 中它被称为**“字符串插值”**，它有不是一个，不是两个，而是三个变体！

**“S”插值器**——您在整个 Scala 编码生涯中使用最多的一个。

```
val name = "Attila"
val followerCount = 2300
val bragging = s"Hello, my name is $name and I have $followerCount Medium followers today."
println(bragging) // > Hello, my name is Attila and I have 2300 Medium followers today.
```

但是你可以超越仅仅替换字符串中的变量，你可以运行任何你喜欢的表达式。

```
val bragging = s"Hello, my name is $name and I will have {$followerCount + 10} Medium followers by tomorrow!"
println(bragging) // > Hello, my name is Attila and I will have 2310 Medium followers by tomorrow!
```

我想我不必解释“S”插值器有多强大！你的想象力是极限！** 

****“F”插值器**不太令人兴奋，也不太常用，但它有时会派上用场。**

```
val name = "Attila"
val miles = 1.5f
val lie = f"$name can run $speed%2.3f miles per minute."
println(lie) // > Attila can run 1.500 miles per minute.
```

****最后是“RAW”**内插器，它非常像“S”内插器，除了它不像`\n`那样对文字进行转义。**

```
println(raw"This is a \n newline") // > This is a \n newline
```

**但是值得注意的是**如果一个变量中的字符串已经被转义，我们对它使用** `**raw**` **操作符，不会有任何影响**，会导致一个转义的字符串。**

```
val escapedString = "This is a \n newline"
println(raw"$escapedString") // > This is a 
 		             newline
```

## **很简单，斯卡拉·斯奎兹！**

**我估计，通读这篇文章大概需要 10 分钟，了解 Scala 中的类型和字符串操作。话虽如此，请记住，仅仅因为它看起来很熟悉，并不一定意味着你们都理解它，你可以假设它与其他语言完全一样。是的，说到底这只是另一种语言，但是和每一种语言一样，你对它了解得越深，差异就越大。**

**这种特殊语言的一个有趣的特性是，它是一种混合语言，在面向对象和函数性方面都非常好。为了实现这一点，必须在这种语言的三个版本中添加大量有趣和令人费解的概念，所以现在还不要把 Scala 添加到你的 LinkedIn 个人资料中！你还有很多要学的，我会分享更多，敬请期待！**

## **你喜欢编码？**

**那好吧。这里还有几篇文章，我打赌你会喜欢的！😁**

**[](/coding-is-not-supposed-to-be-hard-dd5ed4cfb7e0) [## 编码不应该很难

### 然而，解决问题……那是一个完全不同的故事。

levelup.gitconnected.com](/coding-is-not-supposed-to-be-hard-dd5ed4cfb7e0) [](/stop-building-super-components-that-do-everything-5b32f3ad3c9c) [## 停止构建无所不能的超级组件！

### 它们是开发商和企业的噩梦。令人沮丧的、无底的时间和资源坑。我们可以做得更好…

levelup.gitconnected.com](/stop-building-super-components-that-do-everything-5b32f3ad3c9c) [](/how-to-transfer-data-between-two-flutter-apps-without-any-type-of-connection-ae808e78a00a) [## 如何在没有任何连接的情况下在两个 Flutter 应用程序之间传输数据

### 没有网络。没有任何形式的连接，但仍然有一种方法来传输数据…

levelup.gitconnected.com](/how-to-transfer-data-between-two-flutter-apps-without-any-type-of-connection-ae808e78a00a) 

阿提拉·瓦戈——*软件工程师，一次改进一行代码。永远的酷呆子，代码和博客的作者。网络无障碍倡导者，乐高迷，黑胶唱片收藏家。喜欢精酿啤酒！***