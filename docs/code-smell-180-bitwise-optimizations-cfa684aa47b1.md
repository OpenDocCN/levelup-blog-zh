# 代码气味 180 —逐位优化

> 原文：<https://levelup.gitconnected.com/code-smell-180-bitwise-optimizations-cfa684aa47b1>

## *位运算速度更快。避免这些微优化*

![](img/cbd74889262e1a3bcbdc69a0e70ae8d4.png)

> *TL；DR:除非你的商业模式是按位逻辑，否则不要使用按位运算符。*

# 问题

*   可读性
*   聪明
*   过早优化
*   可维护性
*   [双射](https://codeburst.io/the-one-and-only-software-design-principle-5328420712af)违例

# 解决方法

1.  提高可读性

# 语境

一些聪明的程序员解决我们没有的问题。

我们应该基于证据优化代码，并使用科学的方法。

我们应该只在必要的时候进行基准测试，只在真正必要的时候改进代码，并承担可更改性和可维护性的代价。

# 示例代码

## 错误的

```
const nowInSeconds = ~~(Date.now() / 1000)
```

## 对吧

```
const nowInSeconds = Math.floor(Date.now() / 1000)
```

# 侦查

[X]半自动

我们可以告诉我们的棉绒警告我们，并手动检查它是否值得改变。

# 例外

*   实时和关键任务软件。

# 标签

*   过早优化

# 结论

如果我们在一个拉请求或代码审查中发现这个代码，我们需要理解原因。如果它们不合理，我们应该进行回滚，并将其更改为正常的逻辑。

# 关系

[](https://blog.devgenius.io/code-smell-20-premature-optimization-60409b7f90ec) [## 代码味道 20 —过早优化

### 提前计划需要一个水晶球，这是任何一个开发者都没有的。

blog.devgenius.io](https://blog.devgenius.io/code-smell-20-premature-optimization-60409b7f90ec) [](/code-smell-165-empty-exception-blocks-ad13df47ffc9) [## 代码气味 165 —空异常块

### 接下来是我在第一份工作中学到的第一件事

levelup.gitconnected.com](/code-smell-165-empty-exception-blocks-ad13df47ffc9) [](https://blog.devgenius.io/code-smell-06-too-clever-programmer-bffec35daf0b) [## 代码气味 06——太聪明的程序员

### 难以阅读的代码。没有语义的名字很棘手。有时使用语言的意外复杂性。

blog.devgenius.io](https://blog.devgenius.io/code-smell-06-too-clever-programmer-bffec35daf0b) 

# 更多信息

[波浪号运算符~~](https://stackoverflow.com/questions/5971645/what-is-the-double-tilde-operator-in-javascript)

[Javascript 按位运算符](http://rocha.la/JavaScript-bitwise-operators-in-practice)

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

[Frédéric Barriol](https://unsplash.com/@webmaster13870) 在 [Unsplash](https://unsplash.com/s/photos/clock) 上拍摄的照片

原文[此处](https://dev.to/dvddpl/clever-coding-tricks-that-we-dont-need--228l)。

> *观察小事；一个小漏洞会使一艘大船沉没。*

本杰明·富兰克林

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)