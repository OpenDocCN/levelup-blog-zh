# 科特林让龙目岛过时了

> 原文：<https://levelup.gitconnected.com/kotlin-makes-lombok-obsolete-9ed3318596cb>

## 如何从龙目岛迁移到科特林

![](img/97b49b45d4bbaf6742f8ae53a2678218.png)

克里斯·布里格斯在 [Unsplash](https://unsplash.com/s/photos/migrating?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

我们的团队喜欢用 Kotlin 编码。事实上，我们非常喜欢它，我们正在将现有的 Java 项目转换成 Kotlin。这些 Java 项目大多使用 Lombok。我们认为这将是一个很好的实验，看看我们是否可以用惯用的 Kotlin 代码替换我们所有的 Lombok 注释 Java 类。

标题泄露了我们的实验结果。Kotlin 的内置特性几乎覆盖了所有的 Lombok 注释。本文将研究 Kotlin 对 Lombok 注释的替换，并讨论这些替换与 Lombok 注释的不同之处。

# @数据和@值

我们经常需要以保存数据为主要目的的类。这些课程包括编写样板代码。编写样板方法，比如`getters`、`setters`、`toString`、`equals`和`hashCode`，可能会很繁琐。Lombok 通过为可变类提供`@Data`和为不可变类提供`@Value`来解决这个问题。这些注释为您生成样板方法。

## 数据类别

Kotlin 承认需要以保存数据为主要目的的类，并提供了数据类来解决这个问题。数据类从主构造函数中的属性派生样板方法。我们可以使用`var`和`val`关键字来创建可变或不可变的类。

## 特定的样板方法

除了`@Data`和`@Value`之外，Lombok 还有注释生成具体的样板方法，如`@Getter`、`@Setter`、`@ToString`、`@EqualsAndHashCode`。科特林不太灵活。没有办法生成特定的样板方法。对我们来说，这不成问题，因为我们从不在代码库中使用这些注释。

# @Builder

构建器是 Java 中最常用的设计模式之一。`@Builder`生成实现构建器所需的所有样板代码。尽管它很受欢迎，但在 Kotlin 中对构建器模式的需求较少。这与 Kotlin 支持默认和命名参数这一事实有关。

## 默认和命名参数

Kotlin 允许函数参数使用默认值。如果指定了默认值，则可以在函数调用中省略该参数。Kotlin 还允许在函数调用中命名参数。命名参数使得以任何顺序提供参数成为可能。

## 构建器模式不是没有用的

默认和命名参数在大多数情况下是一个很好的替代品，但是它们不能取代构建器模式。例如，您可以在不同的方法之间传递一个构建器，在每个方法中设置几个字段。也许这是一个糟糕的做法，但在使用构造函数创建对象时是不可能的。如果你需要更复杂的构建器，我建议你看看关于如何在 Kotlin 中创建类型安全构建器的官方文档。

# @NonNull

Lombok 为每个用`@NonNull`标注的字段和参数生成空检查语句。在 Kotlin 中，默认情况下类型是不可空的，可以使用问号来声明可空类型。

## 对立面

科特林和龙目岛是对立的。使用 Lombok 可以声明不可空的字段，而在 Kotlin 中可以声明可空的字段。对我来说，Kotlin 的方法似乎更符合逻辑，因为字段通常是不可空的，而不是可空的。

# @与

要更新不可变类中的字段，您可以用您想要更改的字段的新值来克隆该类。Lombok 提供了`@With`来生成`withFieldName(newValue)`方法。这些方法为关联字段生成一个具有新值的克隆。

## 复制

数据类通过提供一个`copy()`函数支持现成的克隆。命名参数使得通过这个函数为多个字段设置新值成为可能。

## 复制包括所有字段

`@With`和`copy()`有两个关键区别。通过在类或字段级别设置`@With`,你可以为特定的字段生成`withFieldName(newValue)`方法。`copy()`功能总是包括所有字段。另一个区别是`copy()`允许一次设置多个字段，而`@With`只允许通过链接`withFieldName(newValue)`方法来更新多个字段。

# @清理

Lombok 生成一个 try/finally 构造来清理当前作用域末尾用`@Cleanup`注释的局部变量声明。

## 使用

`use`是 Kotlin 的[标准库](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/use.html)的一部分。它在一个资源上执行一个阻塞函数，并为您关闭它。即使函数抛出异常，资源也会被关闭。

# @ SneakyThrows

Lombok 的`@SneakyThrows`允许你抛出检查过的异常，而不用在你的方法的`throws`子句中声明它们。Kotlin 没有检查异常，所以不需要这个注释。[官方文档](https://kotlinlang.org/docs/exceptions.html#checked-exceptions)解释了为什么 Kotlin 没有检查异常。

# @日志

Lombok 提供了这种注释的许多变体。使用哪一种取决于所选择的日志框架。通过用`@Slf4J`注释您的类，Lombok 在静态 final `log`字段中初始化一个`Slf4J`记录器。

## kot Lin-日志框架

Kotlin 对于`@Log`没有内置的替代品。这意味着我们必须自己进行初始化。与 Kotlin 配合良好的日志框架是 [kotlin-logging](https://github.com/MicroUtils/kotlin-logging) 。它是一个具有更好的本地 Kotlin 支持的`Slf4J`包装器。

## 一致性是关键

我感觉 Lombok 的`@Log`的主要目的不是让你不用写一行初始化记录器的代码，而是统一初始化记录器。大多数记录器在初始化时需要一个类名。由于复制和粘贴错误，很容易用错误的类名初始化记录器。因为 kotlin-logging 在初始化时不需要类名，所以不容易出现复制和粘贴错误。

# 结论

有时科特林的方法不够灵活。一个数据类将总是实现样板函数，而 Lombok 提供了创建一个特定样板方法的选项。在其他领域，科特林更强大。例如，`copy()`允许您在一次调用中设置多个字段。要用 Lombok 的`@With`做同样的修改，你必须多次克隆这个对象。

Lombok 的注释和 Kotlin 的替代方法之间的细微差别可能意味着您不太可能摆脱 Lombok。对我们来说，差异不是问题。最后，我们可以在所有项目中毫无问题地用惯用的 Kotlin 代码替换 Lombok 注释。

感谢阅读。我希望这有所帮助。如果您有问题或反馈，请随时回复。