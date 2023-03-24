# 构建者解释 Java 中的设计模式

> 原文：<https://levelup.gitconnected.com/builder-explained-design-patterns-in-java-beadf8b32a51>

我将开始一系列文章，在 Java 代码示例上解释[设计模式](https://en.wikipedia.org/wiki/Design_Patterns)。对于任何想深入研究这些常见的 OOP 设计技术的人来说，这可能是有用的。掌握任何东西的最好方法是通过练习。

![](img/8605e440b68c30018f45151e148bb2d1.png)

本杰明·乔彭在 [Unsplash](https://unsplash.com/s/photos/bricks?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# 建设者

Builder 是一种[创造性的设计模式](https://refactoring.guru/design-patterns/creational-patterns)，它提供了一种构建复杂对象的替代方法。当我们想要使用相同的对象构建过程来构建不同的[不可变](https://howtodoinjava.com/java/basics/how-to-make-a-java-class-immutable/)对象时，应该使用这种模式。

> 构建器模式旨在“将一个复杂对象的构建从它的表现中分离出来，以便同一个构建过程可以创建多个不同的表现。”

现在，让我们假设，我们需要创建一个`Employee`对象，它有以下 5 个属性，即`firstName`、`secondName`、`yearOfBirth`、`employeeId`、`registrationAddress`。

现在，如果只有`firstName`、`secondName`、`employeeId`是必需的，而其余的字段是可选的，会怎么样呢？我们需要更多的建设者:

这个问题被称为**伸缩构造函数问题**。想象一下，更多的属性需要多少构造函数。

构建器模式可以在一个类中描述，该类有一个创建方法和几个配置结果对象的方法。构建器方法通常支持链接，例如`ObjectBuilder()->attrubute1(1)->attribute2(2)->build()`。

简称省略字段验证(如果字段值无效`IllegalArgumentException`应该抛出)。请记住，上面创建的对象**没有任何 setter 方法**，因此它的状态一旦构建就不能更改。这提供了期望的不变性。

该模式的用法:

# Lombok @Builder 注释

Project Lombok 的 [*@Builder*](https://projectlombok.org/features/Builder) 是使用该模式的一种简单方法，无需编写样板代码。这里有一个例子:

以及代码中的一个使用示例:

# 结论

## 构建器模式优点

*   设计灵活性和可读性更强的代码。
*   构造函数的参数减少了，并在可读性很高的链式方法调用中提供。这样，在创建类的实例时，就不需要将可选参数的`null`传递给构造函数。
*   实例总是在完整的状态下被实例化。
*   在对象构建过程中，我们可以构建不可变的对象，而不需要太多复杂的逻辑。

## 构建模式缺点

*   该模式需要大量代码

## 在以下情况下使用生成器模式

*   创建复杂对象的算法应该独立于组成对象的部件以及它们是如何组装的
*   构建过程必须允许被构建的对象有不同的表现形式

## Java 核心库中构建器的使用:

*   [StringBuilder](https://docs.oracle.com/javase/8/docs/api/java/lang/StringBuilder.html)
*   [StringBuffer](https://docs.oracle.com/javase/8/docs/api/java/lang/StringBuffer.html#append-boolean-)

要查看关于给定代码示例的更多细节，请访问我的[设计模式 GitHub repo](https://github.com/alexshamrai/design-patterns) 。