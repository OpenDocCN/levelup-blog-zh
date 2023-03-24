# 函数式编程的简明介绍

> 原文：<https://levelup.gitconnected.com/a-gentle-introduction-to-functional-programming-f20df9ff2e2d>

函数式编程越来越受欢迎，这是你可以开始的地方！

![](img/017e07006de3c37b11d95354ca078460.png)

克里斯托夫·高尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在开始函数式编程之前，让我们先了解一下常见的编程范例。

# 编程范例

两种常见的编程范例是:

*   命令式编程
*   声明式编程

## 命令式编程

*   这里我们写了一个关于如何解决问题的**分步解决方案**。

源自命令式编程的编程范例有:

## 过程程序设计

*   编程指令被分成称为**程序的单元。**
*   过程可以在其作用域内存储和使用本地数据，也可以修改全局变量。
*   过程编程语言的例子有 [Fortran](https://en.wikipedia.org/wiki/Fortran) 和 [ALGOL](https://en.wikipedia.org/wiki/ALGOL) 。

## 面向对象编程

*   编程指令被分成**对象**。
*   对象有**属性/特性**和**方法**。
*   面向对象编程语言的例子有 [Java](https://en.wikipedia.org/wiki/Java_(programming_language)) 、 [C++](https://en.wikipedia.org/wiki/C%2B%2B) 、 [C#](https://en.wikipedia.org/wiki/C_Sharp_(programming_language)) 和 [Python](https://en.wikipedia.org/wiki/Python_(programming_language)) 。

## 声明式编程

*   这里我们定义问题，并依靠底层编程语言来解决问题

源自命令式编程的一种编程范式是**函数式编程**。

![](img/d19fafaf6ed22a0b4dc8c8f8804b2e44.png)

[Jexo](https://unsplash.com/@jexo?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 什么是函数式编程？

这是一种编程范例，程序以称为**函数**的单元编写。

# 函数式编程最佳实践

让我们讨论一下在遵循这种编程范式时应该记住什么。

## 1.不可变数据结构的使用

当进行函数式编程时，通常使用不可变的数据结构，如映射、链表和树。

这些数据结构具有[持久性](https://en.wikipedia.org/wiki/Persistent_data_structure)的特性。

## 2.函数必须是确定性的

如果给定相同的输入作为参数，函数必须始终返回相同的输出。

![](img/15ae333f6d9f2478ee93bfda95f52189.png)

照片由 [Vipul Jha](https://unsplash.com/@lordarcadius?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 3.函数必须是纯的

这意味着函数应该**最小化副作用**，&应该**确定性**。

最小化副作用意味着函数不应该修改其本地环境之外的变量。

上述函数修改了一个全局变量`answer`。因此，它不是一个纯粹的函数。

另一方面，下面提到的函数是纯函数，因为它不修改全局变量:

## 4.高阶函数

函数可以接受一个函数作为参数或者/并且可以返回一个函数作为结果。

这种函数被称为高阶函数，它们是函数式编程的重要组成部分。

![](img/a55b66631ca86c836268df0a8c7b0a64.png)

Emile Perron 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 5.“for”循环上的递归

`for`循环并非没有副作用，因为它们使用了可变的计数器变量。

因此，我们使用递归来避免副作用。

## 6.可重用功能

以下函数不可重复使用，因为它依赖于`num`变量。

它可以重复使用，如下所示:

这是对函数式编程的简单介绍！

# 资源

 [## 函数式编程终于成为主流

### Paul Louth 在他于 2005 年创建的医疗保健软件公司 Meddbase 有一个很棒的开发团队。但是作为…

github.com](https://github.com/readme/featured/functional-programming)  [## 函数式编程 101

### 函数式编程经常被误认为是你应该在以后的职业生涯中保留的一个概念，但实际上它可能是一个…

github.com](https://github.com/readme/guides/functional-programming-basics) [](https://en.wikipedia.org/wiki/Functional_programming) [## 函数式编程-维基百科

### 在计算机科学中，函数式编程是一种编程范式，在这种范式中，程序是通过应用…

en.wikipedia.org](https://en.wikipedia.org/wiki/Functional_programming) 

*感谢阅读！*

[](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium-Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership) 

*感谢阅读本文！*

*如果你是 Python 或编程的新手，可以看看我的新书，书名是'* [**《没有公牛**t 学习 Python 指南》**](https://bamaniaashish.gumroad.com/l/python-book) **'** *下面:*

[](https://bamaniaashish.gumroad.com/l/python-book) [## 学习 Python 的无牛指南

### 你是一个正在考虑学习编程却不知道从哪里开始的人吗？我有适合你的解决方案…

bamaniaashish.gumroad.com](https://bamaniaashish.gumroad.com/l/python-book) [](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium——Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入人才集体，找到一份令人惊喜的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)