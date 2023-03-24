# 固体:利斯科夫替代原理在。NET C#

> 原文：<https://levelup.gitconnected.com/the-liskov-substitution-principle-explained-in-net-c-2a838b8a5155>

## 回归基础

## 理解中 SO(L)ID 原则的 Liskov 替换原则。NET C#

![](img/ce5ceda2ac9c0688a84fc9a19539509b.png)

布雷特·乔丹在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

几乎所有使用**面向对象编程(OOP)** 语言的开发人员都知道**坚实的**原则。

# 坚实的原则

*   单一责任原则
*   **O** 关笔原理
*   **L** 伊斯科夫替代原理
*   **I** 界面偏析原理
*   **D** 依赖反转原理

对于这篇文章，我们将要解释 SO **L** ID 的 **L** ，**利斯科夫替代原理**。

![](img/dae42316c04548aad197e34b378e3bf1.png)[](https://medium.com/subscribe/@eng_ahmed.tarek) [## 🔥订阅艾哈迈德的时事通讯🔥

### 订阅艾哈迈德的时事通讯📰直接获得最佳实践、教程、提示、技巧和许多其他很酷的东西…

medium.com](https://medium.com/subscribe/@eng_ahmed.tarek) ![](img/dae42316c04548aad197e34b378e3bf1.png)

# 什么是**利斯科夫替代原理**

里斯科夫替代原则是由芭芭拉·里斯科夫于 1987 年在她的会议主题演讲《数据抽象》中提出的。主要标题是**计算机程序中的数据抽象和层次**。

几年后，在与**Jeanette Wing**的合作下，他们发表了一篇论文，将该原则定义为:

> 设φ(x)是关于 t 类型的对象 x 的一个可证明的性质，那么φ(y)对于 S 类型的对象 y 应该是真的，其中 S 是 t 的子类型。

我知道这个定义似乎太过学术化而非实用化。这就是我们写这篇文章的原因。

![](img/dae42316c04548aad197e34b378e3bf1.png)

# 利斯科夫替代原理到底是什么

它是关于如何使用抽象来构建解决方案层次结构，同时保持逻辑完整和健壮。

如果我试图翻译学术定义，我会说如果一个对象 **B** 继承/扩展对象 **A** ，假设任何期望 **A** 的逻辑应该正确执行，如果 **B** 被传递给它。

然而，这只是直接翻译，但是，如果你问我，我会告诉你，我喜欢给**里斯科夫替代原理**另一个名字；**隐藏意图原理**。

如果你已经在使用一种**面向对象编程(OOP)** 语言，你会知道大多数编译器——如果不是全部的话——会进行静态检查，以确保如果类 **B** 从类 **A** 继承，那么 **A** 中的所有成员都会在 **B** 中找到。所以，这其实不是重点。

关键是你需要确保 **B** 的代码没有隐藏任何可能破坏逻辑的意图。这是编译器检测不到的，你需要自己去做。

![](img/dae42316c04548aad197e34b378e3bf1.png)![](img/649fbfe3547923c32bd646b496934207.png)

照片由[吕山德元](https://unsplash.com/@lysanderyuen?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 这个例子

如果你在网上搜索一个例子来理解**利斯科夫替代原理**，我相信你会遇到著名的**长方形**和**正方形**的例子。事实上，我觉得这是一个很好的例子。

在数学世界里，正方形是矩形，但在某些条件下。所以，如果你要用面向对象的语言来建模**正方形**和**矩形**，你可能会说**正方形**应该继承/扩展**矩形**。

然而，在现实世界中，你确定这是正确的决定吗？

![](img/dae42316c04548aad197e34b378e3bf1.png)![](img/0fdde0c5543c9946ee7ddef5cbbc1389.png)

照片由[阿诺德·弗朗西斯卡](https://unsplash.com/@clark_fransa?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

# 编码时间到了

让我们开始实现矩形和正方形，看看效果如何。

## 矩形

## 平方

这里，我们有一些外部用户不知道的**隐藏意图**。对于一个`Square`，当你将`Width`设置为某个值时，你也将`Height`设置为相同的值。同样，当您将`Height`设置为某个值时，您也将`Width`设置为相同的值。

这会引起任何问题吗？让我想想…

## 程序

这里就可以看出问题了。我们来分析一下会发生什么。

当执行第`StretchRectangle(new Rectangle(2, 5));`行时，会发生以下情况:

1.  `new Rectangle(2, 5)`将创建一个`Width` = 2、`Height` = 5 的`Rectangle`。
2.  `rectangle.Width *= 2`会将`Width`设置为 4。
3.  `rectangle.Height *= 2`会将`Height`设置为 10。
4.  那么最终面积将是 40。

这是意料之中的。

然而，当执行行`StretchRectangle(new Square(2, 5));`时，会发生这种情况:

1.  `new Square(2, 5)`会创建一个`Square`。
2.  在构造函数中，当将它的`Width`设置为 2 时，它的`Height`也会被设置为 2。
3.  同样，在构造函数中，当将它的`Height`设置为 5 时，它的`Width`也会被设置为 5。
4.  所以，到现在为止`Width` = 5、`Height` = 5。
5.  `rectangle.Width *= 2`会将`Width`设置为 10，并将`Height`也设置为 10。
6.  `rectangle.Height *= 2`会将`Height`设置为 20，也将`Width`设置为 20。
7.  那么最终面积将是 400。

这完全不在意料之中。

![](img/dae42316c04548aad197e34b378e3bf1.png)![](img/206bb262ca0648bdf87535a1c9c0408b.png)

照片由 [Aaron Burden](https://unsplash.com/@aaronburden?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 最后的想法

正如你所看到的，我们在这里面临的问题不是由错误的语法引起的，而是由一些隐藏的意图引起的。

因此，抽象和继承是好的，但是你需要考虑你的抽象设计，避免隐藏任何可能破坏你的逻辑的意图。

![](img/dae42316c04548aad197e34b378e3bf1.png)

## 希望这些内容对你有用。如果您想支持:

如果您还不是**中型**会员，您可以使用 [**我的推荐链接**](https://medium.com/@eng_ahmed.tarek/membership) ，这样我就可以从**中型**中获得您的一部分费用，您无需支付任何额外费用。订阅
[**我的简讯**](https://medium.com/subscribe/@eng_ahmed.tarek) 将最佳实践、教程、提示、技巧和许多其他很酷的东西直接发送到您的收件箱。

![](img/dae42316c04548aad197e34b378e3bf1.png)

# 其他资源

这些是你可能会发现有用的其他资源。

[](/compiler-friendly-code-sealed-keyword-in-net-c-b363fbcd1e35) [## 编译器友好代码:在。NET C#

### Why & When Sealed 关键字可以提高。NET C#

levelup.gitconnected.com](/compiler-friendly-code-sealed-keyword-in-net-c-b363fbcd1e35) [](/defensive-copy-in-net-c-38ae28b828) [## 防御副本在。NET C#

### 所有关于防御的内容。NET C#

levelup.gitconnected.com](/defensive-copy-in-net-c-38ae28b828) [](/why-split-large-methods-into-smaller-ones-7b71f26f8745) [## 为什么要把大方法分成小方法呢？！

### 学习何时将大方法分解成小方法，让不可能变成可能。

levelup.gitconnected.com](/why-split-large-methods-into-smaller-ones-7b71f26f8745) [](/protecting-public-methods-from-illogical-calls-in-net-c-91fcbb8bee33) [## 保护公共方法免受不合逻辑的调用。NET C#

### 包含代码示例和解释的完整指南。

levelup.gitconnected.com](/protecting-public-methods-from-illogical-calls-in-net-c-91fcbb8bee33) ![](img/dae42316c04548aad197e34b378e3bf1.png)