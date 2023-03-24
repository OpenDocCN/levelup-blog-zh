# Java 中的构造函数、构造函数重载和构造函数链接——完全指南

> 原文：<https://levelup.gitconnected.com/constructors-constructor-overloading-and-constructor-chaining-in-java-complete-guide-latest-c10cb8a244bd>

## 面向对象编程

## 都是关于 java 构造函数和面试问题的。

![](img/54c7d21612031a782a72110845faa9c3.png)

萨法尔·萨法罗夫在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在这篇文章中，我将解释 Java 中的构造函数。构造函数重载和构造函数链接。所以让我们开始吧…

# Java 中的构造函数是什么？

1.  构造函数是用来初始化类的对象的一段代码。
2.  构造函数和方法有相似的语法，但是构造函数没有返回类型。
3.  构造函数与类名同名。

# 构造函数的语法:

```
access_modifier class_name(argument_list or no_arguments) {
    //object fields initialization or no initialization
}
```

我们举个例子来理解这一点。阅读代码时，请查看与代码相关的注释，以便更好地理解。

**建造师实例**

## 输出:

```
no-argument constructor called !
constructor with one argument called !
constructor with two arguments called !
```

# 何时调用构造函数？

我举个例子解释一下:

```
Student s1 = new Student();
```

这里用一个新的关键字创建 object，并调用无参数构造函数 Student()。

# 什么是无参数构造函数？

程序员显式地编写一个无参数的构造函数。无参数构造函数可能有也可能没有语句/字段初始化。

**无参数构造函数:**

```
//example of no-argument constructor
public Student() {
    System.out.println("no-argument constructor called !");
}
```

# 什么是参数化构造函数？

程序员添加一个参数化的构造函数，并在创建对象时将参数传递给构造函数。参见下面的例子。

## 参数化构造函数:

```
//example parameterized constructor (1 param)
public Student(String name) {
    this.name = name;
    System.out.println("constructor with one argument called !");
}
```

## 对象创建和参数化构造函数调用。

```
/*
 new creates object s2 in heap and invokes parameterized constructor - one param
 */
Student s2 = new Student("Vikram");
```

# 什么是默认的构造函数，编译器什么时候把它添加到程序中？

下面是一个默认构造函数的例子。

```
//example of default constructor
public Student() {

}
```

当程序员不向类中添加任何构造函数时，编译器会在编译时向类中添加一个默认构造函数，您可以在。**类的**文件各自的**。java** 文件。

当一个类扩展另一个类时，默认构造函数也使用 **super()调用**调用父类构造函数。这是一个隐式调用，你不会看到它。

关于 **super 关键字和 super()调用**的更多细节，你可以参考这篇文章。

# 什么是复制构造函数？

复制构造函数用于将一个对象复制到另一个对象。请参见下面的示例。

## 输出:

```
constructor with two arguments(String , int) called !
name : Vikas id : 29
copy constructor called !
name : Vikas id : 29
```

在上面的程序中，使用 **new** 操作符创建了两个对象，正如您在输出中看到的，这两个对象具有相同的对象字段值。

# 什么是构造函数重载？

名称相同但签名不同的构造函数称为重载构造函数。它们可能有不同数量的参数、不同的参数序列或不同类型的参数。让我们看看下面的节目。

构造函数重载

## 输出:

```
no-argument constructor called !
constructor with one argument(name) called !
constructor with one argument(id) called !
constructor with two arguments(String , int) called !
constructor with two arguments(int , String) called !
```

你已经看到我用不同数量的参数、不同的参数序列或者不同类型的参数重载了 **Student()** 构造函数。

关于方法重载和重写，可以参考这篇文章。

# 什么是构造函数链接？

**使用这个()调用从一个构造函数调用另一个构造函数，称为构造函数链接。**我们可以有多个构造函数，但是在创建一个对象时，任何一个构造函数都可以被调用。让我们看一个例子来理解这个概念。

> **注意:this()调用应该总是第一个语句是构造函数。**

构造函数链接

## 输出:

```
constructor with two arguments(String , int) called !
name : Vikram id : 10
one param constructor called !
no-arg constructor called !
```

在上面的例子中，无参数构造函数调用→一个参数构造函数，一个参数构造函数调用→两个参数构造函数。这被称为构造函数链接。

我已经介绍了与构造函数、构造函数类型、构造函数重载和构造函数链接相关的所有概念。我希望你从这篇文章中学到了一些东西。

*本文到此为止。希望你喜欢这篇文章。*

# 类似内容可以关注[维克拉姆古普塔](https://medium.com/u/2c3b611409dc?source=post_page-----c10cb8a244bd--------------------------------)。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入人才集体，找到一份令人惊喜的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)