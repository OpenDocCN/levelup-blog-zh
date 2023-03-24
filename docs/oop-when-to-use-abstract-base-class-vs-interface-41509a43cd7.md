# OOP:何时使用“抽象”基类与“接口”

> 原文：<https://levelup.gitconnected.com/oop-when-to-use-abstract-base-class-vs-interface-41509a43cd7>

## 一个定义一个对象的特征，另一个建立这个对象能做什么的契约

![](img/5c6a75d03e561f0d96e104e08304d54a.png)

阿列克斯·马林科维奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在设计类的架构时，会出现一些问题:

*   它的**实例**将如何被**使用**
*   发现**公共属性**和**模式**
*   预见这个类在未来将如何扩展

您可以通过使用**抽象基类**和**接口**来利用您的实现。通常这两者会引起很多混淆，因为它们是相似的，然而，它们实际上有不同的实现。

让我们找出何时使用每一个。为此，我们将看到使用不同编程语言(Kotlin、TypeScript、Java)的例子，但是理论适用于所有这些语言。

## 什么是抽象类？

当你想让其他类(或其他抽象类)扩展**公共方法或属性时，可以将一个类声明为**抽象**。**

当一个抽象类定义一个**抽象方法**时，这个方法**必须被扩展子类**覆盖——除非这个抽象类被另一个抽象类扩展，在这种情况下，你将得到一个级联继承。

这些在抽象类中定义的抽象方法，被创建时只有它们的签名。此时它们不应该有主体，主体将在被覆盖的方法中定义。

同样地，**你可以定义具体的(非抽象的)方法，这些方法将被扩展类继承。**

考虑一下这个:

*   X 教授是一个**教授**，他也是一个*延伸*的**人**
*   所有的**教授**都会**用特定的方式跟**打招呼:“*嗨，我是……*教授。”
*   所有的**人**都会**以同样的方式**说再见:“*再见！*”。
*   每个**人**都有一个**效忠**，他们可以用同样的方式表示效忠:“*万岁…！*”。

用面向对象编程你会怎么做？首先，让我们试试**科特林**:

Kotlin 中抽象基类的示例

前面脚本的输出将是:

```
Starting up...
Hello, you can call me Doctor Doom.
Long live Sorcerers Supreme!
Hello, you can call me Professor X.
Long live X-Men!
The end.
```

在这种情况下，我们所做的是创建一个抽象类 **Person** ，这个类有两个具体的方法( **showLoyalty** 和 **sayGoodbye** )。这两个将被继承到任何扩展这个抽象。

我们还有一个抽象变量(**忠诚度**)。这个变量必须被两个扩展 **Person** 的类覆盖。这些类( **Professor** ， **Doctor** )在它们的构造函数中这样做。**人**的**名**也在构造函数中定义。

现在，我们看到每个类都覆盖了抽象类中定义的抽象方法 **intro** 。如果没有，它会抛出一个类似于下面的错误:

```
Class ‘Professor’ is not abstract and does not implement abstract base class member public abstract fun intro(): Unit defined in Person
```

## 什么是接口？

另一方面，一个**接口**传统上被称为一个**契约**，这个契约将被强制**到任何实现它们的类。**

本质上，一个接口将向实现类的用户展示这个类能够做什么。相反，一个抽象的定义**是一种平等关系**:一个**医生** *是一个* **人**。

还要注意，**接口没有定义要实现的方法**的主体。

您可能注意到的另一件事是，传统上，接口是以“*-able”*后缀命名的。这不是强制性的，但有助于确定他们的目的。其他程序员倾向于添加“*I-”*作为前缀，例如一个接口**克隆**将最终被称为 IClonable——个人认为这有点矫枉过正，但是，各有各的！

现在，观察下面的 Java 代码:

Java 中的接口示例。

前面的程序将在终端中打印出如下内容:

```
Abilities check...
Nightcrawler teletransported to X-Mansion.
Storm started to fly.
Suddenly we see 4 clones of Multiple Man.
End of exercise.
```

这里实例化的类是**学生**，实现了接口**突变体**。变种人定义了 3 种方法:**瞬移**，**飞翔，**和**自复制**。这意味着这 3 个方法必须在**学生**类中实现。否则，您会看到如下错误:

```
Main.java:7: error: Student is not abstract and does not override abstract method selfDuplicate(int) in Mutant
```

## 把所有的放在一起

面向对象编程的一个很好的特性是能够将接口与抽象类混合起来，使你的类完全符合你的需要。

在下面的 TypeScript 代码中，注意类的定义为`class Male extends Person implements Mutantable`。现在，这是非常强大的，因为你可以定义这个**男**到*是一个* **人**并且*拥有一个**突变**能人的能力*。

TypeScript 上的抽象和接口示例。

前面的代码运行时将产生以下输出:

```
An X-Men Short Story
A new X-Men is about to be born...
Wolverine is born.
Checking Wolverine's abilities:
Regeneration: true
Fly: false
Wolverine met Kayla.
Kayla is now Wolverine's girlfriend.
The end.
```

同样，本文的主要观点是:

> 一个接口将定义**这个类能够做什么**:一个**医生**可以**操作()**
> 
> 一个抽象定义**一个平等关系**:一个**医生** *是一个* **人**。

# 感谢阅读！

在 [LinkedIn](https://www.linkedin.com/in/pablo-del-valle) 、 [Medium](https://medium.com/@pablo.delvalle.cr) 、 [GitHub](https://github.com/delvalle) 上找我。