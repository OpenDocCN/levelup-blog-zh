# 不变性和安全性——从 Java 到 Kotlin 到 Haskell

> 原文：<https://levelup.gitconnected.com/immutability-from-java-to-kotlin-to-haskell-eea641ca1db1>

![](img/2fb84fa73410f36d22cc24c24cea8b7e.png)

图片来自[来自 Pixabay 的 GLady】](https://pixabay.com/photos/cocoon-butterfly-larva-larvae-209096/)

不可变对象的状态在创建后不能修改。如果对象在创建后可以被修改，我们称之为对象的变异。简而言之，对象是可变的。

让我们用一个 [Java](https://en.wikipedia.org/wiki/Java_(programming_language)) 中的例子。

我们有一个`allowedUsers`列表，一个`getAllowedUsers`方法和一个检查`username`是否为`canUserAccessResource`的方法。我们可以把它看作是一种简单的访问控制方法，比如`canUserAccessResource("Mallory")`，它将并且应该返回`false`。马洛里不在`allowedUsers`的名单中。

更详细的例子可以在 Coursera 的[编程语言中找到](https://www.coursera.org/learn/programming-languages/lecture/aOQ26/optional-java-mutation)

上面的代码有一个与突变相关的问题。具体在哪里？我们想一会儿。

我们的列表`allowedUsers`可以被修改以允许最初不在列表中的用户访问。

我们创建了一个测试来使问题可见并可修复。

两项测试都通过了。马洛里，开始时不在`allowedUsers`中，可访问列表突变后的资源。**那就糟了**。

与其学习如何减轻上述问题，我们为什么不使用更多的现代语言呢？这些语言可能会给我们带来开箱即用的不变性。

比如[科特林](https://kotlinlang.org/)？：

我们不再需要吸气剂了。另外，[文件](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/list-of.html)在`listOf`状态:

> 返回给定元素的新只读列表。

这是否解决了安全问题？

这次我们用科特林克 REPL 来代替测试

尝试，例如

```
authorizedUsers[2] **=** "Mallory"
```

或者

```
authorizedUsers **=** listOf**(**"Mallory"**)**
```

会失败。

既没有一个`set`方法来提供对`list`的访问，也没有一个方法来重新分配`authorizedUsers`，因为我们使用的是`val`而不是`var`。看起来不错。

我们能做的就是把它转换成`MutableList`，然后修改它

实际对象`authorizedUsers`可由另一个参考改变。尽管措辞`MutableList`可能暗示使用`List`将是不可变的，但事实并非如此。

为科特林辩护:人们经常强调如何使用措辞`read-only`而不是`immutability`。`listOf`的文档再次声明

> 返回一个新的给定元素的只读列表。

只读的，不是不可变的。让我们离开 JVM，继续我们在 Haskell 中的不变性之旅。Haskell 是一种纯函数式语言。

使用[堆栈](https://docs.haskellstack.org/en/stable/README/)哈斯克尔 REPL。

在 Haskell 中，数据是不可变的。我们不会改变现有的列表，而是通过应用函数来构建一个新的列表。Haskell 帮助我们避免了默认的安全问题。

为什么以上这些都很重要？这是关于可预测性。高级程序员会认识到 Java 例子中的问题吗？很有可能。睡眠不足的大三或大四学生会在最后期限前完成任务吗？也许吧。我们会想记住代码库的其他部分是如何修改允许的用户的吗？让我们猜测答案是否定的。

没错。我们可以通过简单地复制 getter 中的列表来解决 Java 的问题。现在，是否有人试图改变返回的副本已经无关紧要了。初始的`allowedUsers`不会改变。

尽管创建副本是我们必须时刻记住的事情。精神开销。

从 Kotlin 开始的人会在文档中偶然发现它之前或遇到 bug 时意识到`read-only`和`immutability`之间的区别吗？

解决上述问题的最简单方法似乎是，我们的编程语言是否默认使用了不变性？