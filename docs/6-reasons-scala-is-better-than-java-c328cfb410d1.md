# Scala 优于 Java 的 6 个理由

> 原文：<https://levelup.gitconnected.com/6-reasons-scala-is-better-than-java-c328cfb410d1>

## 最后一个可能是锦上添花

![](img/5c63acf00277c662fb96b2ff7fafea0e.png)

克里斯里德在 [Unsplash](https://unsplash.com/s/photos/scala-code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# 代码的简单性和大小

支持 java 的人提出的最大论点是，Java 非常简单，易于理解。但是 java 的冗长本质使得代码变得更大，并且常常使理解起来更复杂。然而，Scala 编译器更加智能，不需要明确指定编译器可以推断的内容。让我们举一个简单的“*你好世界*”的例子

```
// Java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello World");
    }
}
//scala
object HelloWorld {
    def main(args: Array[String]): Unit = {
        println("Hello World")
    }
}
```

即使在这个基本的例子中，我们也看到了 scala 中不需要的冗余代码。复杂的例子也是如此

```
public class Car {
    private String type;

    public String getType() {
        return type;
    }

    public void setType(String name) {
        this.type = type;
    }
}
```

在 scala 中，这要简单得多

```
class Car {
    var type: String = _
}
```

对于 Scala 来说，面向对象模式对于实现代码的限制并不适用，这导致了简洁的代码。更少的代码行最终会提高测试和开发的速度。

# 表演

![](img/4be991392c6c56186edcba2e1551d6f5.png)

照片由[转到](https://unsplash.com/@laurentmedia?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上的 [Unsplash](https://unsplash.com/s/photos/speed?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

谷歌几年前做过一项研究，比较了 C++、Java、Scala 和 go。该研究得出结论，当普通开发人员编写代码时，如果没有过多考虑优化，Scala 比 Java 和 Go 更快。这项研究使用了每种语言中默认的惯用数据结构。这样做的原因是，他们假设开发人员在时间压力下工作，并且基本上使用对开发人员来说简单快捷的方法为该语言编写惯用代码。

根据其他一些网站的消息，Scala 比 Java 快。一些程序员甚至声称 Scala 比 Java 快 20%。【Scala 和 Java 都运行在 JVM 上。所以他们的代码在 JVM 上运行之前必须被编译成字节码。但是 Scala 编译器支持一种叫做尾部调用递归的优化技术。优化使得 Scala 代码比 Java 代码编译得更快。

# 静态类型化

像 Java 这样的静态类型语言会在编译时检测错误，并在使用它们之前强制声明变量的类型，而像 Python 这样的动态语言只能在运行时发现错误。

Scala 集两者之长。它具有很强的统计类型，但给人一种动态的感觉。例如，编译器确保在整个程序中正确使用 Int 类型的某个值，并且在运行时，该值的内存位置只能保存 Int 类型的值。

Scala 编译器最大限度地使用了类型推理。

```
var a = 10
```

Scala 为变量和函数提供了类型推断，比 [Java](https://click.linksynergy.com/fs-bin/click?id=JVFxdTr9V80&subid=0&offerid=323058.1&type=10&tmpid=14538&RD_PARM1=https%3A%2F%2Fwww.udemy.com%2Fjava-the-complete-java-developer-course%2F) 中有限的类型推断要好得多。

# 高级结构

Scala 的很多语法和 Java 的很相似。但是 scala 比 Java 有很多高级的结构。例如，Scala 支持描述不可变值对象的 case 类，以及强大的自动类型推断。DSL 在 Scala 中非常流行，因为 Scala 具有高度结构化的特性。因此，程序员可以根据需要创建自己的小型子语言来定制 Scala 的外观。在这个[堆栈溢出](https://stackoverflow.com/questions/41724/what-is-a-dsl-and-where-should-i-use-it)中，一个工程师解释了他如何有效地为不太懂技术的人使用它。

# 不断增长的社区和框架

Scala 生态系统正在不断发展。许多[顶级公司](https://alvinalexander.com/scala/whos-using-scala-akka-play-framework/)已经开始使用 Scala。随着高采用率，出现了大量的酷框架，如 Lift、Finch 和 Play。

Akka 是另一个基于 Scala 的并发框架，是一个成熟的工具包和运行时，用于在 JVM 上构建高度并发、分布式和容错的事件驱动应用程序。它有一个**伟大的** **并发模型**，因此开发者认为 Akka 优于竞争对手。

# 对工程师的需求

这并不是真正的 Scala 专家，但是精通 Scala 的工程师很少。这使得 scala 开发者非常受欢迎。

Scala 现在是一种小众语言。我强烈建议花几个月时间学习这门语言。这不仅会让你成为一名更好的程序员，还会让你在竞争激烈的市场中领先一步。

如果你读到这里，很好——也许你同意我的观点。这里有一些 Scala 的在线课程:

1.  [开始 Scala 编程](https://click.linksynergy.com/fs-bin/click?id=JVFxdTr9V80&subid=0&offerid=323058.1&type=10&tmpid=14538&RD_PARM1=https%3A%2F%2Fwww.udemy.com%2Fbeginning-scala-programming%2F)
2.  [Scala 中的函数式编程](https://opencourser.com/collection/2ou9b0/functional-programming-in-scala?from=au84a3)
3.  [托比·韦斯顿的面向 Java 开发者的 Scala】](https://pluralsight.pxf.io/c/1193463/424552/7490?u=https%3A%2F%2Fwww.pluralsight.com%2Fcourses%2Fscala-for-java-developers)
4.  [不耐烦的 Scala】](http://www.amazon.com/dp/0321774094/?tag=javamysqlanta-20)
5.  [高级 Scala 编程](https://www.udemy.com/course/advanced-scala/)