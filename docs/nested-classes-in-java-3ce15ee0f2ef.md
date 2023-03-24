# Java 中的嵌套类

> 原文：<https://levelup.gitconnected.com/nested-classes-in-java-3ce15ee0f2ef>

## 4 种不同类型的快速介绍

![](img/39e48a4cd6f92f26e6d118a62ad89047.png)

Alberto Triano 在 [Unsplash](/s/photos/nesting?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在面向对象的语言中，嵌套类或内部类是完全在另一个类中声明的类。

这允许我们将逻辑上绑定在一起的类组合在一起，以增加封装，从而获得更简洁和可维护的代码。

下面是对 4 种嵌套类的快速、非深入的概述。

```
**TABLE OF CONTENTS**-  [Static Nested Classes](#b6c5)
-  [Non-Static Classes / Inner Classes](#7bc6)
-  [Local Classes](#10d1)
-  [Anonymous Classes](#85cf)
-  [Shadowing](#7c10)
-  [Conclusion](http://63e0)
```

# 静态嵌套类

嵌套类的定义与任何其他类一样:

像`static`成员一样，`static`嵌套类被绑定到类本身，而不是它的实例。这意味着我们可以实例化它，而不用先创建一个`Outer`的中间实例:

它的行为就像任何其他类一样，并遵循相同的规则:

*   支持所有的[访问修饰符](https://docs.oracle.com/javase/tutorial/java/javaOO/accesscontrol.html)
*   可以定义`static`和非`static`成员
*   无法访问其封闭类的非`static`成员

为了更简单的用法，我们甚至可以`import`嵌套类去掉外部类前缀:

## 何时使用

嵌套类的行为就像任何其他顶级类一样，也应该如此对待。主要优点是包装方便。

# 非静态嵌套类/内部类

非`static`嵌套类也称为*内部类:*

内部的`Nested`类被绑定到其封闭类的实例，而不是与`Outer`类类型相关联。

由于这种关系，它可以访问封闭类的所有成员，而不仅仅是`static`成员。但是内部类本身不能定义任何`static`成员。

为了实例化内部类，我们现在需要其封闭类的一个实例:

不再只有一种类型，即“仅仅”嵌套在另一个类下。内部类与其封闭类的实际实例紧密绑定，不能再独立存在。

## 序列化

仅仅因为封闭类可能是可序列化的，并不意味着嵌套类也是自动可序列化的。

与任何其他成员一样，我们必须确保他们也执行`[java.io.Serializable](https://docs.oracle.com/javase/8/docs/api/java/io/Serializable.html)`。或者我们可能以一个`[java.io.NotSerializableException](https://docs.oracle.com/javase/8/docs/api/java/io/NotSerializableException.html)`结束。

## 何时使用

内部类的优势在于与它们的封闭类有更深的联系，包括对其所有成员的完全访问。但是这种联系会导致不明显的记忆保持。在嵌套实例垃圾收集之前，封闭类也不能垃圾收集。

## 资源

[内部类和封闭实例](https://docs.oracle.com/javase/specs/jls/se11/html/jls-8.html#jls-8.1.3) (JLS)

# 本地课程

局部类是内部类的一种特殊形式。

我们可以在任何类型的块(例如方法)中定义一个*局部类*:

就像内部类一样，我们可以访问封闭类的所有成员。但是我们不能给类提供访问修饰符，因为它只能“本地”使用。

## 何时使用

我们可以用内部类实现同样的行为。但是它不会像局部类那样将逻辑强有力地绑定到特定的块。

这是一个很好的工具，可以更好地将逻辑分组在一起，并且对于本地类，我们可以使用尽可能小的内存占用。

## 资源

[局部类声明](https://docs.oracle.com/javase/specs/jls/se11/html/jls-14.html#jls-14.3) (JLS)

# 匿名类

*匿名类*不是定义一个嵌套类，而是通过实例化一个预先存在的类型来创建一个新类:

我们刚刚基于接口`[Runnable](https://docs.oracle.com/javase/8/docs/api/java/lang/Runnable.html)`创建了一个新类，因为它没有名字，所以它是*匿名的*。

不仅接口可以用于创建匿名类。我们还可以扩展其他非`final`类:

一个专门的`List<String>`实现，完全不需要创建一个单独的类，非常简洁！

创建语法总是遵循相同的结构:

```
**new** <<Type>>(<<constructor arguments>>) {
    // declarations / overrides
};
```

匿名类声明是表达式，并且 ***必须*** 是语句的一部分，要么在块中，要么作为成员声明本身。

## 匿名类对 Lambdas

随着λ表达式的出现，我们终于有了一种更简单的方法来当场实现类型:

两个谓词的功能是相同的，所以我们可能认为 lambdas 只是匿名类的语法糖。生成的字节码有所不同，表明它可能以相同的方式运行，但不是以完全相同的方式完成的:

lambda 使用操作码`[invokedynamic](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/multiple-language-support.html#invokedynamic)`，这允许 JVM 进行更动态的方法调用。

## 何时使用

匿名类非常适合小型的、特定的现场实现。即使λ可能就足够了。

由于它们的简单性，与局部类或内部类相比也有许多缺点:

*   没有名字会让 stacktraces 更难追踪
*   只能使用单一类型，没有额外的接口等。
*   更复杂的语法

## 资源

*   [匿名阶级宣言](https://docs.oracle.com/javase/specs/jls/se11/html/jls-15.html#jls-15.9.5) (JLS)
*   虚拟机中的匿名类(Oracle)
*   Java 函数式编程:语法 suga(中等)

# **遮蔽**

在软件开发中，*隐藏*是在更深的范围内对成员的重新声明。这意味着我们可以在嵌套类中重用变量名，还可以通过在调用前添加前缀来访问隐藏成员:

# 结论

我们可以逻辑地将类分组在一起，或者当场创建匿名实例，这很好。但是我们需要仔细决定我们真正想要和需要的嵌套类的类型。特别是对于[功能接口](https://medium.com/@benweidig/best-of-java-8-e5aa8cbed673#f751)来说，lambda 可能是一个更简洁的解决方案。

## 额外资源

*   Java 教程:嵌套类(甲骨文)
*   [内部类](https://en.wikipedia.org/wiki/Inner_class)(维基百科)
*   [Java 中的嵌套类](https://www.baeldung.com/java-nested-classes) (Baeldung)
*   [Java 内部类](https://www.tutorialspoint.com/java/java_innerclasses.htm) (Tutorialspoint)