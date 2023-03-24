# Java 函数接口和 Lamda 实现

> 原文：<https://levelup.gitconnected.com/java-functional-interface-and-lamda-implementation-7121eb7c8564>

## 术语 *Java 函数接口*是在 Java 8 中引入的

![](img/958a7173420ec6af94b69e175a8e1956.png)

[Tracy Adams](https://unsplash.com/@tracycodes?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Java 中的函数接口是只包含一个抽象(未实现)方法的接口。

除了单个未实现的方法之外，函数接口可能已经默认，静态方法可能已经实现。

添加了注释，这样我们可以将一个接口标记为功能接口。即使没有它，只要你的接口只有一个抽象方法，它也会被视为功能性的。**这确保了接口不能有一个以上的抽象方法。**

这里，它只有一个抽象方法，所以我们可以省略注释`@FunctionalInterface.`

## **功能接口要点/观察:**

1.  一个函数接口只有一个抽象方法，但是它可以有多个默认方法。
2.  `@FunctionalInterface`注释用于确保一个接口不能有多个抽象方法。该注释的使用是可选的。
3.  java.util.function 包包含了 Java 8 中很多内置的函数接口。

## 功能界面的使用

*   java 8 中引入了函数接口，以支持 Java 8 中的 lambda 表达式。另一方面，可以说 lambda 表达式是函数接口的实例。
*   Java 8 Collections API 已经被重写，并且引入了一个新的流 API，它使用了很多函数接口。
*   Java 8 在这个包里定义了很多功能接口`java.util.function`。一些有用的 java 8 函数接口有`Consumer`、`Supplier`、`Function`和`Predicate`。

## 如何定义功能接口

首先，我们可以检查一个内置的功能接口。其给出如下:

这里，`Function`接口表示一个函数(方法),它接受一个参数(T)并返回一个值(R)。

除了上面列出的方法之外，`Function`接口实际上包含了一些额外的方法。尽管如此，因为它们都有一个默认的实现，所以您不必实现这些额外的方法。

您必须实现的实现`Function`接口的唯一方法是`apply()`方法。下面是一个`Function`实现的例子:

现在调用方法如下:

这里，

*   第一个例子创建了一个新的`AddSeedValueImplementation`实例，并将其赋给一个`Function`变量。
*   其次，该示例在`AddSeedValueImplementation`实例上调用`apply()`方法。
*   第三，该示例打印出结果(即 9)。

现在我描述它的 lambda 实现:

这里，

*   首先，我们定义计算功能接口应用方法。这里，n 是提供的输入，而(5L+n)是提供的输入和硬编码值 5 之和
*   其次，我们正在使用新配置的接口**功能**。在 apply 方法中，我们传递计算。

## 默认方法的功能接口

我们只是在修改之前的界面。

这里，我们只是添加了一个名为 defaultMethodAdd 的默认方法。下面给出了调用机制。

## 具有两个输入和一个结果的功能接口

检查下面的代码

这里，

*   输入是 X 和 Y，输出是 R

现在调用机制是

这里，

*   我们正在定义函数的行为方式。它将两个参数作为输入，相乘并返回结果值。

## 具有字符串匹配的功能接口

让我们检查下面的功能接口谓词。

这里，

*   输入是 T，会是字符串，输出是 R

现在调用机制如下

这里，

*   创建字符串列表。
*   用从 G 开始的逻辑字符串构建谓词
*   现在从另一个列表中提取从 G 开始的字符串

## 结论

我希望你喜欢这篇文章。快乐阅读:)

**参考文献:
1。**[https://www.geeksforgeeks.org/functional-interfaces-java/](https://www.geeksforgeeks.org/functional-interfaces-java/)
2。[http://tutorials . jen kov . com/Java-functional-programming/functional-interfaces . html](http://tutorials.jenkov.com/java-functional-programming/functional-interfaces.html)
3 .[https://dzone . com/articles/functional-interface-and-lambda-expression-in-Java](https://dzone.com/articles/functional-interface-and-lambda-expression-in-java)