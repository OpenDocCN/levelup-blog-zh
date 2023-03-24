# Java 中的 10 个最佳编码实践

> 原文：<https://levelup.gitconnected.com/10-best-coding-practices-in-java-9302b3cf7ead>

## 让你和你的开发伙伴生活更轻松的编码实践

![](img/59c545f24d3f19976396b2c5dfd3d536.png)

照片由[克里斯蒂娜@ wocintechchat.com](https://unsplash.com/@wocintechchat?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

用 Java 编写代码的最佳实践经常被开发人员忽略，因为他们缺乏实现它们的知识或时间。然而，您花费在学习和实现最佳实践上的时间比您可能意识到的要多。

本文讨论的最佳实践面向初级和高级 Java 开发人员。这些最佳实践将帮助您提高性能、降低安全风险、节省堆内存并简化调试，从而节省大量时间。让我们开始吧。

# 使用正确的命名约定

> 如果你知道某个东西是做什么的，你就有很大的机会猜测它的 Spring 类或接口的名字。

Spring 框架的创建者 Rod Johnson 发表了上述声明，以强调遵循命名约定的重要性。在您的 Java 代码中也应该遵循这样的命名约定。

一个格式良好的名称不仅有助于阅读代码，还传达了大量关于代码意图的信息。让我们看一个真实世界的例子。

```
public class User{     
    private String userName;     
    public void setUserName(String userName){  
        this.userName = userName;     
    } 
}
```

从前面的例子中可以看出，

*   类名或接口名应该是名词，因为它们代表真实世界的对象。
*   方法名应该是动词，因为它们总是类的一部分，通常代表一个动作。
*   变量名应该简短但有意义，除了循环迭代器之类的临时变量，应该避免使用单字符变量名。

因此，不要用无意义的名字填充代码，而要使用有意义的名字，让你的开发伙伴、质量保证工程师，甚至你自己在将来更容易理解代码。

# 使用“+”运算符最小化字符串连接

在 Java 中使用“+”操作符连接字符串是许多开发人员的常见做法，包括我自己。但是，当连接多个字符串时，运算符“+”是低效的，因为 Java 编译器在创建最终连接的字符串之前会创建多个中间字符串对象。

为每个字符串分配内存会降低程序速度，最终可能会耗尽所有堆内存。

Java 中的解决方案和最佳实践是使用`StringBuilder`或`StringBuffer`类。这些类中的内置函数，比如`append()`，可以在不创建中间字符串对象的情况下连接两个字符串，节省处理时间和内存使用。

```
StringBuilder sbd = new StringBuilder("0");
for (int i = 1; i < 100; i++) {
    sbd.append(", "+i);        
}
```

你可以从我下面的文章中学习如何使用`StringBuilder`和`StringBuffer`。

[](https://medium.com/javarevisited/string-stringbuilder-and-stringbuffer-do-you-know-the-difference-6a53429dcf) [## String、StringBuilder 和 StringBuffer 你知道区别吗？

### Java 字符串完全指南

medium.com](https://medium.com/javarevisited/string-stringbuilder-and-stringbuffer-do-you-know-the-difference-6a53429dcf) 

# 避免对象可变性

可变对象的对象状态和变量值可以在其生命周期内更改。因为许多变量可以指向同一个实例，所以跟踪对象状态何时何地发生了变化可能会变得很困难。这是尽可能避免可变对象的一个令人信服的理由。

另一方面，不可变对象不会随时间改变它们的内部状态，是线程安全的，并且没有副作用。由于这些特征，不可变对象在多线程环境中特别有用。

实现不变性最基本的方法是使所有可变字段`private`和`final`，这样它们一旦初始化就不能被迭代。Spring 框架允许你通过使用`constructor`依赖注入模型来保持对象不变。

# 记录

在开发中，尤其是在维护中，日志的重要性不能被夸大。任何进行过生产代码调试的 Java 开发人员都希望在某个时候有更多的日志。

在`java.util.logging package`中找到的 Java 日志 API 允许您跟踪应用程序中的错误。当应用程序生成日志调用时，`Logger`将事件记录在`LogRecord`中。随后，它发送给适当的处理程序或附加程序。

Java 中有很多日志库和框架，包括 [SLF4J](http://www.slf4j.org/) 和[日志回溯](http://logback.qos.ch/)。虽然它们使代码库中的日志记录相对简单，但是您必须确保遵循日志记录最佳实践。否则，草率的日志记录会成为维护的噩梦，而不是福音。让我们来看看其中的一些最佳实践:

*   避免过多的日志记录，只考虑可能对故障排除有用的信息。
*   小心选择日志级别；您可能需要在生产中有选择地启用日志级别。
*   为了更快地进行分析，请使用外部工具来跟踪、聚合和过滤日志消息。

让我们看一个适当的描述性日志记录的例子。

```
logger.info(String.format(“A new user has been created with userId: %s”, userId));
```

# 始终检查参数前提条件

在执行任何逻辑之前，您必须始终验证公共方法的输入参数，以确保容错。Java 断言是验证方法参数的最佳方式。在下面的例子中，如果年龄小于零，就会抛出一个`AssertionError`。

```
public void setAge(int age) {
    assert age >= 0 : "Age should be positive";
}
```

> 注意:为了避免对生产系统产生任何性能影响，您可以在运行时禁用断言。

另一种选择是使用 Google Guava，其中的`Preconditions`类可用于验证参数。Guava 比 Java 的基本断言提供了更大的灵活性。

[](https://github.com/google/guava) [## GitHub-Google/guava:Google Java 核心库

### Guava 是来自 Google 的一组核心 Java 库，包括新的集合类型(如 multimap 和 multiset)…

github.com](https://github.com/google/guava) 

# 避免空的捕捉块

在处理异常的同时，在 catch 块中编写一条正确且有意义的消息是精英开发人员首选的 Java 最佳实践。新手开发人员通常一开始会将 catch 块留空，因为只有他们在处理代码。然而，当一个异常被一个空的 catch 块捕获时，程序不会显示任何东西，这使得调试成为一场噩梦。

> 此外，未处理或打印出的异常堆栈跟踪会使您的应用程序容易受到注入攻击。

因此，在 catch 块中正确处理异常非常重要。下面的示例演示了如何正确使用 catch 块。

```
try {                
    doSomeWork();        
} catch (NullPointerException e) {  
    log("Exception occurred", e);
    response.sendError(
        HttpServletResponse.SC_INTERNAL_SERVER_ERROR,
        "Exception occurred");
    return;
}
```

在本例中，我们记录了堆栈跟踪并返回了一个非公开的响应，以防止攻击者看到堆栈跟踪。

# 避免多余的初始化

> 对于任何字段或数组组件，将值显式设置为 0、false 或 null 是不必要的，也是多余的。

默认情况下，Java 将字段或数组组件的值设置为上面列出的值。这个特性是专门包含在 Java 中的，以减少重复编码，所以我们应该利用它。

# 浮动还是双精度？

没有经验的开发人员经常在不适当的上下文中使用 float 和 double。他们通常只选择一种类型，而不考虑需求。使用浮子还是双浮子应由要求决定。

如果需要精确计算，应该用 double 代替 float。因为大多数处理器处理浮点或双精度运算的时间几乎相同，但双精度运算比浮点运算精度高得多。

当不考虑精度时，可以使用 float 代替 double，因为它占用的内存空间是 double 的一半。

请记住，使用适当的数据类型将提高代码的性能。

# 检查古怪的最好方法

我见过很多开发者马上用`i % 2 == 1`来看一个数字是不是奇数，这是不正确的。这是因为它只对正数有效。所以下面的方法才是 Java 中检查一个正负数的奇性的正确方法。

```
public boolean isOdd(int num) {
   return (num % 2 != 0)
}
```

尽管这是正确的，但它不是最佳的 Java 实践。因为按位 AND 运算符比算术模运算符快得多，所以使用下面的方法来检查异常是 Java 中的最佳实践。

```
public boolean isOdd(int num) {
   return (num & 1) != 0;
}
```

# 避免内存泄漏

尽管 Java 自动管理内存并避免了大多数内存泄漏，但是如果不遵循正确的实践，内存泄漏会发生。在编码时避免内存泄漏的一些最佳实践包括:总是在查询完成后释放数据库连接，尽可能频繁地使用 finally 块，避免内部类，以及释放存储在静态表中的实例。

还有许多工具可以帮助您检测代码中的内存泄漏。

*   IntelliJ IDEA 中的 [Memory 选项卡允许您查看堆中所有对象的详细信息，这将有助于您检测内存泄漏及其原因。在 IntelliJ 中，您还可以使用 Profiler 工具窗口来捕获正在运行的进程的](https://www.jetbrains.com/help/idea/analyze-objects-in-the-jvm-heap.html)[内存快照](https://www.jetbrains.com/help/idea/analyze-hprof-memory-snapshots.html)，并对它们进行泄漏分析。
*   Eclipse 中的[内存分析器(MAT)](https://www.eclipse.org/mat/) 允许您检查 Java 堆的内存泄漏并减少内存消耗。您可以轻松地分析堆转储，即使它们包含数百万个对象，并查看每个对象的大小。
*   [NetBeans Profiler](https://netbeans.apache.org/kb/docs/java/profiler-intro.html) 可用于分析 Java SE、Java FX、EJB、移动应用程序和 Web 应用程序中的内存使用情况。
*   除此之外，你可以使用 [Java 飞行记录器](https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/memleaks001.html)工具或者开源的 [VisualVM](https://visualvm.github.io/) 工具来调试你的应用中的内存泄漏。

应用程序中的内存泄漏会显著降低性能，因此请确保遵循上面列出的最佳实践来避免它们。

本文到此为止。我希望你能遵循这些最佳实践来确保你的代码是干净的，你的应用是高效的。

感谢您的阅读，祝您编码愉快！