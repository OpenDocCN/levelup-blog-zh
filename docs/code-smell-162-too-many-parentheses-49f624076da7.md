# 代码气味 162 —括号太多

> 原文：<https://levelup.gitconnected.com/code-smell-162-too-many-parentheses-49f624076da7>

## *括号是免费的。不是吗？*

![](img/80e00f17ce1a0b54c2ad7a5c033627d8.png)

> *TL；DR:尽量少用括号。*

# 问题

*   可读性
*   句法复杂性

# 解决方法

1.  删除所有不必要的括号

# 语境

我们从左到右阅读代码(至少在西方文化中是这样)。

圆括号经常打断这种流动，增加了认知的复杂性

# 示例代码

# 错误的

```
schwarzschild = ((((2 * GRAVITATION_CONSTANT)) * mass) / ((LIGHT_SPEED ** 2)))
```

# 对吧

```
schwarzschild = (2 * GRAVITATION_CONSTANT * mass) / (LIGHT_SPEED ** 2)
```

# 侦查

[X]自动

这是完全自动化的代码味道。

它基于语法树。

很多工具都检测到了。

# 例外

在一些复杂的公式中，我们可以添加额外的括号来增加术语的可读性。

# 标签

*   可读性
*   膨胀者

# 关系

[](https://blog.devgenius.io/code-smell-02-constants-and-magic-numbers-d6c320eef90b) [## 代码气味 02——常数和幻数

### 一种方法用大量的数字进行计算，而不描述它们的语义。

blog.devgenius.io](https://blog.devgenius.io/code-smell-02-constants-and-magic-numbers-d6c320eef90b) 

# 结论

我们写一次代码，读太多次。

可读性才是王道。

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

照片由 [Unsplash](https://unsplash.com/s/photos/signs) 上的[刻痕](https://unsplash.com/@jannerboy62)拍摄

> 如果有人声称拥有完美的编程语言，他要么是个傻瓜，要么是个推销员，或者两者兼而有之。

*比雅尼·斯特劳斯特鲁普*

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)