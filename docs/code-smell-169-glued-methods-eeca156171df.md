# 代码气味 169 —粘合方法

> 原文：<https://levelup.gitconnected.com/code-smell-169-glued-methods-eeca156171df>

## 不要同时做两件或两件以上的事情。

![](img/8326d41b19ff68643fe23adf59769e9c.png)

> *TL；DR:在你的方法中尽量做到原子化*

# 问题

*   耦合代码
*   更难测试
*   更难阅读

# 解决方法

1.  打破方法

# 重构

[](https://blog.devgenius.io/refactoring-002-extract-method-1478f421b35a) [## 重构 002 —提取方法

### 找到一些可以分组并被原子调用的代码片段。

blog.devgenius.io](https://blog.devgenius.io/refactoring-002-extract-method-1478f421b35a) 

# 语境

如果你用“和”来命名一个方法，你可能会错过一个提取并中断方法的机会。

# 示例代码

# 错误的

```
calculatePrimeFactorsRemoveDuplicatesAndPrintThem()// Three responsibilities
```

# 对吧

```
calculatePrimeFactors();removeDuplicates();printNumbers();// Three diferent methods
// We can test them and reuse them
```

# 侦查

[X]半自动

一些 linters 可以警告我们关于包括术语“和”的方法。

# 标签

*   连接

# 结论

在做方法的时候，玩一些橡皮鸭的故事，告诉自己是不是做对了，是很重要的。

# 关系

[](https://blog.devgenius.io/code-smell-85-and-functions-7f9cb0ad0b2d) [## 代码气味 85 —和功能

### 不要执行超过要求的内容。

blog.devgenius.io](https://blog.devgenius.io/code-smell-85-and-functions-7f9cb0ad0b2d) 

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

斯科特·桑克在 [Unsplash](https://unsplash.com/s/photos/glue) 上拍摄的照片

> 像大多数其他学科一样，学习编程艺术包括首先学习规则，然后学习何时打破规则。

约书亚·布洛赫

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)