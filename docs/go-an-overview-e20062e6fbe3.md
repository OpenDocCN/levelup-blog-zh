# Go:概述

> 原文：<https://levelup.gitconnected.com/go-an-overview-e20062e6fbe3>

![](img/976b797fe0f37df3d4fb6165754ac8bb.png)

阿什利·麦克纳马拉提供

G oogle 工程师——*Rob Pike*、 *Robert Griesemer* 和*Ken Thompson*——在 2007 年开始设计一种[静态类型](https://en.wikipedia.org/wiki/Static_typing)编程语言的过程，目标是创建一种简单的语法来安全地利用[多核](https://en.wikipedia.org/wiki/Multi-core_processor)处理器。它旨在为其他语言(C++和 Java)中不必要的复杂性提供解决方案，结合了 C、Pascal、Modula 和 Oberon 的有利功能，包括东尼·霍尔的 [CSP](http://usingcsp.com/) 的一些方面，旨在以一种语法实现高效编译、高效执行和编程简便。2008 年中期*，伊恩·泰勒*开始着手 [GNU 编译器集合](https://talks.golang.org/2010/gofrontend-gcc-summit-2010.pdf)前端的工作，主要贡献由 *Russ Cox 提供。*

2009 年 11 月 10 日——谷歌公开宣布发布开源项目——确切地说是——第一个版本将于 2012 年推出。别名 *Golang* 来源于域名[**golang.org**](https://golang.org/)——这是由于*go.org*不可用而选择的。2016 推出了 *Gopher* 吉祥物——由 *Renée French —* 创造，以及一张[模型单](https://golang.org/doc/gopher/modelsheet.jpg)展示了独特特征的准确表现。如今，Go 有一个数百万程序员的社区，这些程序员来自世界各地，他们为这个项目做出了贡献，帮助塑造了该语言目前的面貌。

# 运行时间

*Go* 受到 C 编程语言的启发，增加了安全性和简单性[动态类型的](https://en.wikipedia.org/wiki/Dynamic_programming_language)语言，如 JavaScript 或 Python。它带有一个内置的运行时库，采用的功能包括但不限于 [**【垃圾收集】**](https://en.wikipedia.org/wiki/Garbage_collection_%28computer_science%29)[**并发**](https://en.wikipedia.org/wiki/Concurrency_%28computer_science%29) 和 [**堆栈管理**](https://en.wikipedia.org/wiki/Stack-based_memory_allocation) 。

与 [*线程*](https://en.wikipedia.org/wiki/Thread_%28computing%29) 不同的是， *Go* 利用了一个被称为[**goroutine**](https://gobyexample.com/goroutines)**的函数或方法，它独立地、同步地与代码中的其他 gorroutine 相关联地执行，从而更容易使用并发性。Goroutines 通过**渠道**沟通。**

**本质上，函数或方法在一组线程上执行，当一个 goroutine 被阻塞时——被阻止执行——运行时将它的协同例程从同一个操作系统线程移动到一个分布式线程，这加速了阻塞。**

**为了防止堆栈变得过大，运行时库使用了一个封闭的堆栈，其大小可以调整到几千字节。然后，它会增加或减少用于存储堆栈的内存，这允许在适量的内存中溢出 go routine，从而可以在相同的地址空间中构建大量的 go routine。**

# **类型**

**在*走，*一切都是一个**式的**。原语类型包括:字符串、布尔、整数、字节、符文、浮点数和复数。虽然可以使用支持面向对象编程风格的类型和方法，但是没有固定的顺序，所以对象比其他语言更轻量级。**

***Go* 中的面向对象编程专注于类型之间的关系。不是预先声明哪些类型是相关的，类型将根据其方法的子集来协调一个接口，从而可以协调多个接口。**

**[**接口**](https://golangdocs.com/interfaces-in-golang) 在 *Go* 中可谓是刻画了另一种类型必须具备的类型的定义。它们允许[多态](https://en.wikipedia.org/wiki/Polymorphism_%28computer_science%29)——接受两种不同类型实体的单一接口。一个**空接口**没有方法，允许每个类型满足。**

***Go* 中的所有内容也是通过值传递的，这意味着函数将总是接收作为参数传递的任何内容的副本。**

# **错误处理**

***Go* 没有设计内置异常。相反，错误是使之成为可能的价值。就标准的错误处理而言，函数和方法可以返回多值，从而方便地输出错误，而不会淹没返回的值。它还附带了一些功能，如**延迟**、**紧急**和**恢复**，以发出信号并从独特的条件中恢复，这些条件作为分解错误的功能状态的一部分执行，从而实现干净的错误处理。**

# **入门指南**

**[围棋之旅](https://tour.golang.org/welcome/1)**

**[有效去](https://golang.org/doc/effective_go.html)**

**[Go 语言规范](https://golang.org/ref/spec)**

**[Go 文档](https://godoc.org/)**

**[去游乐场](https://play.golang.org/)**