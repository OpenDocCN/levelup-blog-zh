# 请重构这些代码块

> 原文：<https://levelup.gitconnected.com/please-refactor-these-blocks-of-code-c7d2b6f2e4ce>

## 我知道它们有用，但是请让我们去掉这些代码的味道

![](img/fb855009c7258d8608b682a6b93e80f9.png)

照片由[路易斯·戈麦斯](https://www.pexels.com/@luis-gomes-166706?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)从 [Pexels](https://www.pexels.com/photo/black-and-gray-laptop-computer-546819/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄

我和我的团队目前正在将我们的 Odoo ERP 从 12.0 版本升级到 14.0 版本。该练习还包括升级我们的定制模块，确保与 14.0 版兼容。

正在升级的一些模块是六年前开发的。因此，偶然发现让我汗毛直竖的代码块并不奇怪。

这样的代码块通常暗示着代码的味道。这么多年过去了，我忍不住把它们清理干净，让它们看起来焕然一新。

通过这篇文章，我相信你也可以用你自己的代码库做同样的事情。
如果你遇到了代码的味道，偿还他们创造的一些技术债务就成了你的责任。

我的口头禅是，如果你遇到代码味道，那么你有责任确保它被清除。消除代码味道可能是一个简单而独立的活动，或者如果您对代码块的功能及其影响范围没有足够的了解，您可能需要与其他开发人员和利益相关者协作。

我分享了一些你必须立刻重构的代码块。这种重构不需要任何深刻的见解或背景，因此您引入潜在错误或副作用的可能性很小甚至没有。

重构这些代码块给你和其他开发人员带来了回报

1.  提高了代码库的可读性
2.  增强了对代码库的理解
3.  降低代码库的认知复杂性
4.  减少代码行数
5.  提高代码库的可维护性

# #1

## 严重的

**因为:**

1.  使用`+`来实现名称的连接，代码冗长而混乱
2.  使用可避免的`Str.rstrip()`方法，在将名称与`,`分隔符拼接在一起后，清除尾部空格
3.  我们用太多的代码行实现了一个简单的解决方案

## 较好的

**因为:**

1.  代码简单、干净、全面
2.  代码不需要像`Str.rstrip()`那样移除尾部空格
3.  使用 Python 的`Str.join()`连接起来很好也很简单，不需要`+`操作符
4.  我们用 3 行代码实现了一个更简单的解决方案。

# #2

## 严重的

**因为:**

1.  我们通过过于明确地使用我们的`if invoice.is_due is True`检查来执行多余的检查
2.  `is True`没有必要考虑简单使用`if invoice.is_due`就可以完成隐含真值评估

## 较好的

**因为:**

1.  代码简单明了
2.  通过添加`is True`充分利用了`invoice.is_due`的真实性，而不是显而易见和冗长

# #3

## 严重的

**因为:**

1.  `else`考虑到所有`Exception`的提出都将终止程序，所以没有必要阻塞
2.  代码比需要的多了一行
3.  遵循和理解程序的控制流增加了代码的复杂性和认知负荷

## 较好的

**因为:**

1.  我们在程序的预期流程之前检查并提高所有的`Exception`
2.  通过移除`else`条件块，降低了代码复杂性和认知过载
3.  更少的代码行和更简单的控制流

# #4

## 严重的

**因为:**

1.  代码冗长，不简单，也不直接全面
2.  代码有太多代码行
3.  使用可避免的`total_allocation`变量作为聚合器
4.  代码行太多，无法实现简单的求和

## 较好的

**因为:**

1.  代码简单、直接且全面
2.  不使用迭代来实现求和
3.  利用 Python 内置的`sum()`函数实现求和
4.  相比之下只有几行代码

# #5

## 严重的

**因为:**

1.  代码冗长，不简单，而且简单易懂
2.  代码有太多代码行
3.  使用可避免的`total_allocation`变量作为聚合器
4.  代码行太多，无法实现简单的求和

## 较好的

**因为:**

1.  代码简单、直接且全面
2.  不使用迭代来实现求和
3.  利用 Python 内置的`sum()`函数实现求和
4.  利用 Python 的[列表理解](https://docs.python.org/3/tutorial/datastructures.html)，而不是冗长的`for`循环
5.  相比之下只有几行代码

# #6

## 严重的

**因为:**

1.  太啰嗦了
2.  由于在`if`块中嵌套了`for`循环，增加了代码的复杂性和认知负荷
3.  因为`for`循环间接兼作`if`程序块，所以`if`程序块是不必要且可避免的。如果列表中至少有一个元素，它将遍历列表

## 较好的

**因为:**

1.  一行更简单，可读和全面
2.  删除多余的`if`条款；利用`for`循环的真实评估机制

## 你可能也会喜欢这些

[](/the-5-habits-every-junior-developer-must-have-to-become-a-better-developer-639374a12e77) [## 每个初级开发人员成为更好的开发人员必须具备的 5 个习惯

### 是时候重拾曾经富有感染力的激情和热情，成为一名更好的开发人员了

levelup.gitconnected.com](/the-5-habits-every-junior-developer-must-have-to-become-a-better-developer-639374a12e77) [](/im-finally-giving-functional-css-a-chance-a9ab284dde12) [## 我终于给了函数式 CSS 一个机会

### 我正在用顺风 CSS 做这件事

levelup.gitconnected.com](/im-finally-giving-functional-css-a-chance-a9ab284dde12) [](https://medium.com/analytics-vidhya/13-git-commands-i-use-daily-14e3ad562068) [## 我每天使用的 13 个 Git 命令

### 您需要知道这些命令

medium.com](https://medium.com/analytics-vidhya/13-git-commands-i-use-daily-14e3ad562068)