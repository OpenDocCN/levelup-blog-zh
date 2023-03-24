# 为什么一个接口不能实现另一个接口

> 原文：<https://levelup.gitconnected.com/why-an-interface-can-not-implement-another-interface-updated-f3f7ec936e95>

## 面向对象编程

## 类和接口示例和概念

![](img/2db89d2af0ac2ad7180ae151a42b5e83.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com/s/photos/java?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

因为你已经读过文章的标题，这是你下次面试时可能会被问到的有趣的面试问题之一。我发现这个问题非常有趣，我们将在本文中讨论它。

# 问题是:

## 让我通过写一些示例代码来问这个问题，这样你就能清楚地知道问这个问题的意义。

```
interface A {
    void display();
}
```

## 抽象类 B 实现接口 a。

```
abstract class B implements A {
} 
// we may or may not implement display() method but this is allowed
```

## C 类将这个抽象扩展到了 b 类。

```
class C extends B {
   void display(){
   }
}
```

## 接口 D 实现了接口 a。为什么这是不允许的，尽管它是 100%抽象的，并且类似于抽象类 B？

```
interface D implements A {
} // this is not allowed.
```

在跳到答案之前，我们先来了解一下抽象类和接口。这将是一个快速掌握答案的复习。

# Java 中的抽象

***抽象*** *是隐藏实现细节，只向用户显示功能的过程。*

另一种方式，它只向用户显示基本的东西，隐藏内部细节，例如，在你输入文本和发送消息的地方发送短信。你不知道消息传递的内部处理。

抽象让你专注于对象做什么，而不是它如何做。

## 实现抽象的方法:

在 java 中有两种实现抽象的方法

1.  *抽象类(0 到 100%)*
2.  *界面(100%)*

# 1.抽象类:

以下是关于抽象类的一些重要观察。

1.  ***无法创建抽象类的实例，因为它可能包含在抽象类中没有方法定义的抽象方法。***
2.  ***允许构造函数，这样就可以初始化抽象类的数据字段。***
3.  ***我们可以有一个抽象类，不需要任何抽象方法。***
4.  我们可以在抽象类中定义静态方法。

## 示例:

抽象示例。

## 输出:

```
in display of CentralBank class.
in writeMessage of CentralBank class.
in sayHello of Bank class.
in display of Bank class.
```

# 2.界面:

*一个* ***接口*** *是一个类的蓝图。它有静态常数和抽象方法。*

*它是一种实现抽象的机制。Java 接口中只能有抽象方法，不能有方法体。它用于实现 Java 中的抽象和多重继承。*

*   通过接口，我们可以 ***支持多继承的功能。***
*   它 ***可以用来实现松耦合。***
*   它 ***不能像抽象类一样被实例化。***

## 注意:

1.  *从 Java 8 开始，我们可以在一个接口中拥有* ***默认和*** *静态方法。*
2.  *从 Java 9 开始，我们可以在一个接口中拥有* ***私有方法*** *。*

## 示例:

界面示例

## 输出:

```
overridden default method
default method
drawing rectangle
```

# 要回答上述问题，下面是几个要点:

1.  一个类可以扩展另一个类来继承父类的特性和/或实现一个接口来为接口的抽象方法提供方法定义。
2.  一个接口扩展了另一个接口，因为扩展另一个接口的接口只是添加了自己的抽象方法，并没有为另一个接口的抽象方法提供方法定义。
3.  接口永远不会扩展类，因为接口不提供方法定义。

# TLDR:

*一个接口只能有* `***extend***` *其他接口不能有* `***implement***` *其他接口作为接口只能有方法声明而不能有方法体本身。*

*当我们说实现时，那基本上意味着为抽象方法提供方法定义。*

***于是一个接口扩展了另一个接口。***

另外，注意一个 *100%* `abstract class`在功能上等同于一个`interface`，但是如果你愿意，它也可以有实现(在这种情况下，它不会保持 100% `abstract`)，所以从 JVM 的角度来看，它们是不同的东西。

同样，100%*`**abstract class**`中的成员变量可以有任何访问说明符，而在`**interface**`中，它们是隐式的`**public static final**`。*

# *一个接口可以覆盖另一个接口的默认方法吗？*

*可以在一个接口中编写*默认*和*静态* *方法*来支持向后兼容。当一个接口扩展另一个接口时，该接口可以覆盖默认方法，然而，将实现的类也可以覆盖默认方法。*

*查看以下示例:*

*重写接口内的默认方法。*

**本文到此为止。希望你喜欢这篇文章。**

# *您可以关注[维克拉姆·古普塔](https://medium.com/u/2c3b611409dc?source=post_page-----f3f7ec936e95--------------------------------)了解类似内容。*

# *分级编码*

*感谢您成为我们社区的一员！在你离开之前:*

*   *👏为故事鼓掌，跟着作者走👉*
*   *📰查看更多内容请参见[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)*
*   *🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)*

*🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)*