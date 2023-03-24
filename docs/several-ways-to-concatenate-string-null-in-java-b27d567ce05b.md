# Java 中连接字符串 Null 的几种方法

> 原文：<https://levelup.gitconnected.com/several-ways-to-concatenate-string-null-in-java-b27d567ce05b>

如何在 Java 中处理字符串

![](img/a7d7ee2e00feb3fabec3857a81fa96af.png)

照片由 [Kari Shea](https://unsplash.com/@karishea?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/desk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

Java 提供了多种串接 String 字符串的方式，但是有时候如果我们不注意`null`字符串，可能会将 null 串接成结果，这显然不是我们想要的。

在本文中，我将介绍一些在连接`String`时避免`null`值的方法。

**案例分析。**

如果我们想要连接字符串数组，我们可以简单地使用`+`操作符来连接它们，但是我们可能会遇到`null`值。

这将把所有元素连接成结果字符串，如下所示:

```
https://gist.github.comnull
```

但是，我们已经发现了问题，最终的`null`值也被连接成一个字符串，这显然不是我们想要的。

此外，即使我们运行在 Java 8 或更高版本上，然后使用`String.join()`静态方法连接字符串，我们也会得到带有`null`值的输出。

结果也如下。

```
https://gist.github.comnull
```

那么如何解决这个问题呢？下面我们分享一些解决方法。

为了方便后面的代码演示，我们提取了一个方法，可以传入一个字符串，返回一个非空字符串。

**# 1。使用** `**String.concat()**` **。**

`String.concat()`是 String 类附带的一个方法。使用这种方法连接字符串非常方便。

因为调用了`nullToString()`方法，所以结果中没有`null`值。

**# 2。使用 StringBuilder。**

StringBuilder 类提供了许多有用且方便的字符串构造方法。

最常用的方法是 append()方法，用`append()`拼接字符串，结合`nullToString()`方法避免`null`值。

可以获得以下结果:

```
https://gist.github.com
```

**# 3。使用 StringJoiner 类(Java 8+)。**

`StringJoiner`类提供了更强大的字符串拼接功能。它不仅可以指定拼接时的分隔符，还可以指定拼接时的前缀和后缀。这里我们可以使用它的`add()`方法来拼接字符串。

同样会使用`nullToString()`方法来避免空值。

**# 4。使用 Streams.filter (Java 8+)。**

Stream API 是 Java 8 中引入的一个强大的流操作类，可以执行常见的过滤、映射、遍历、分组、统计等操作。

过滤操作`filter`可以接收一个`Predicate`功能。

`Predicate`功能界面与之前介绍的`Function`界面相同。

这是一个功能界面。

它可以接受一个通用的`<T>`参数，返回值是一个布尔类型。

`Predicate`常用于数据`filter`。

因此，我们可以定义一个`Predicate`来检查一个`null`字符串，并将其传递给流 API 的`filter()`方法。

最后，使用`Collectors.joining()`方法连接剩余的非`null`字符串。

**# 5。使用** `**+**` **运算符。**

使用`+`操作符可以解决问题，但不推荐使用。

我们知道字符串是不可变的对象。使用`+`符号会频繁创建一个字符串对象，每次都会在内存中创建一个新的字符串，所以使用`+`符号拼接字符串的性能消耗非常高。

**总结一下。**

本文介绍了几种连接字符串和处理 NULL 的方法，不同的方法可能适用于不同的场景。

请注意，连接字符串是一项开销很大的操作，下面是使用 JMH 对几个连接进行基准测试的结果。

可见`StringBuilder`的性能是最好的。在实际使用中，需要结合具体场景，然后选择性能开销最低的方法。

感谢阅读。

如果你喜欢这样的故事，想支持我，请给我鼓掌。

你的支持对我来说非常重要，谢谢你。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**将像你这样的开发人员安置在顶级创业公司和科技公司**](https://jobs.levelup.dev/talent/welcome?referral=true)