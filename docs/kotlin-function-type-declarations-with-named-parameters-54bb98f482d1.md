# 带命名参数的 Kotlin 函数类型声明

> 原文：<https://levelup.gitconnected.com/kotlin-function-type-declarations-with-named-parameters-54bb98f482d1>

## 任何 lambda: (foo: Bar) ->单元的有用文档

![](img/e799c0c19a5206d013d9bb8d5068a92c.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Obi Onyeador](https://unsplash.com/@thenewmalcolm?utm_source=medium&utm_medium=referral) 拍摄

函数声明包含其参数的名称。这在编程中再普通不过了。

匿名函数和 lambda 的定义包含命名参数，尽管`it`可能被隐式命名并与 lambda 一起使用。根据函数或 lambda 声明的上下文，甚至可能不需要声明参数类型。

当声明函数本身的类型时，重点是类型。因此，一个普通的声明就是`(Int) -> Unit`，一个需要单个`Int`类型参数的函数。知道了这一点的来龙去脉，以后搞清楚`it`的意思应该是相当容易的。但是，即使只增加一个`Int`参数，每个参数的目的也会立刻变得任意。但是…

> [函数类型](https://kotlinlang.org/docs/reference/lambdas.html#function-types)符号可以选择性地包含函数参数的名称:`(x: Int, y: Int) -> Point`。这些名称可用于记录参数的含义。

很好地隐藏在*函数类型*的文档中，据说**命名参数**在函数类型声明中可用。这是一些有用的信息和文档的巨大增强。 *IntelliJ IDEA* 和 *Android Studio* 甚至支持这些名字的代码生成。所以尽可能地利用它，帮助其他人更好地理解代码的意图。

[](https://kotlinlang.org/docs/reference/lambdas.html) [## 高阶函数和λ

### Kotlin 函数是一流的，这意味着它们可以存储在变量和数据结构中，作为…

kotlinlang.org](https://kotlinlang.org/docs/reference/lambdas.html)