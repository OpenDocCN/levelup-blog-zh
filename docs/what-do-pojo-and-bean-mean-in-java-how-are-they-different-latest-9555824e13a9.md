# POJO 和 Bean 在 Java 中是什么意思？它们有什么不同？

> 原文：<https://levelup.gitconnected.com/what-do-pojo-and-bean-mean-in-java-how-are-they-different-latest-9555824e13a9>

## 面向对象编程

## java 编程中的 POJO 和 Bean 指南…

![](img/2f2e635c5784e4e3b481ba7a815f0b20.png)

**POJO vs Java Beans**

在本文中，我将解释两个重要的 java 特性，即 POJO 和 Java bean。这些有何不同，何时使用？阅读完本文后，您将对这些 Java 特性有一个很好的了解。

## 先说 POJO。

# POJO 是什么意思？

1.  它代表普通的旧 Java 对象。
2.  它是一个简单的实体[人/雇员等]对象，没有任何限制。
3.  它易于编写并提高了**代码的可读性**和**的可重用性**。
4.  它不应该扩展或实现预定义的 Java 类和接口。
5.  它对对象字段的访问修饰符没有限制。例如，字段可以是**私有**、**默认**、**受保护**和**公共**。
6.  POJO **封装了**一些业务逻辑。

让我们看一个例子来理解这一点。

作为 POJO 类的人:

**担任 POJO 的人**

## 主 as 驱动程序类:

## 输出:

```
name : Vikram Gupta age : 25
```

## 现在我们来讨论一下 Java bean …

# Java bean 是什么意思？

Java bean 是一个 Java 类，

1.  应该有一个**公共无参数构造函数。**
2.  应该只具有**私有属性**(对象字段应该是私有的)。
3.  应该有**公共获取器和设置器**用于访问和设置私有属性。
4.  **可以实现可序列化接口**。

*注:串行化是将 Java 对象转换成字节数组的过程，以便在内存中保存这些对象，因为在程序执行后，Java 会破坏这些对象。*

> **注意:在编写 Java beans 时，应该遵循上面的这些约定。**

# 我们为什么需要 Java beans？

这些是应用程序中最常见的类类型，用于为应用程序中使用的不同对象建模数据。

让我们看一个例子来理解这一点。

学生作为 Java Bean 类:

**学生为 Java bean 类**

**创建一个对象**并在主类中使用 **getter** 和 **setter** 方法来访问字段属性:

## 输出:

```
Student name : Vikram registration number : 100
```

**Java beans 有利有弊:**

## 优势:

1.  Getter 和 Setter 可以向外部应用程序公开。
2.  同一个组件可以使用 **n** 次。

## 缺点:

1.  getter 和 setter 函数的数量随着 bean 类属性数量的增加而增加。例如，如果 bean 类有 30 个属性，那么就有 60 个方法来设置和获取属性值。因此，仅仅是设置和获取这些值就需要大量的代码。

# POJO 和 Java beans 之间的区别:

1.  POJO 可以有非私有字段，而 Java beans 只能有私有字段。
2.  POJO 可能有也可能没有构造函数，而 Java beans 应该有一个无参数构造函数。

# 最后的话:

> POJO 和 Beans 类用于定义 java 对象以提高可读性。
> 
> **所有的爪哇咖啡豆都是 POJOs，但反之亦然。**

*本文到此为止。希望你喜欢这篇文章。*

# 类似内容可以关注[维克拉姆古普塔](https://medium.com/u/2c3b611409dc?source=post_page-----9555824e13a9--------------------------------)。