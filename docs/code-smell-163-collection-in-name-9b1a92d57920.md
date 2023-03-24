# 气味代码 163——名义上的收藏

> 原文：<https://levelup.gitconnected.com/code-smell-163-collection-in-name-9b1a92d57920>

## 你见过顾客收藏吗？

![](img/e01ff6c0a091a6e993833ce578555b4b.png)

> *TL；大卫:不要在你的名字中使用“收集”。对于具体的概念来说太抽象了。*

# 问题

*   可读性
*   抽象滥用
*   [不好命名](https://blog.devgenius.io/what-exactly-is-a-name-part-ii-rehab-fc5058b3ff1)

# 解决方法

1.  用特定的名称重命名集合。

# 语境

命名很重要。

我们需要处理很多收藏品。

集合是惊人的，因为它们不需要空值来模拟缺失。

空集合与完整集合是多态的。

我们避免[null](https://codeburst.io/null-the-billion-dollar-mistake-c2918c92f7e0)和[if](https://blog.devgenius.io/how-to-get-rid-of-annoying-ifs-forever-317033474484)。

我们经常使用不好的和模糊的名字，而不是在[映射器](https://medium.com/@mcsee/what-is-software-9a78c1172cf9)中寻找好的名字。

# 示例代码

# 错误的

```
foreach (var customer in customerCollection)
{
    // iterate with current customer
}foreach (var customer in customersCollection)
{
    // iterate with current customer
}
```

# 对吧

```
foreach (var customer in customers)
{
    // iterate with current customer
}
```

# 侦查

[X]半自动

所有的 linters 都能发现这样的错误命名。

这也可能导致假阳性，所以我们必须谨慎。

# 标签

*   命名

# 结论

我们需要关心所有干净的代码、变量、类和函数。

准确的名称对于理解我们的代码至关重要。

# 关系

[](/code-smell-134-specialized-business-collections-ce63d851792b) [## 代码气味 134 —专业商务系列

### 如果它走路像鸭子，叫声像鸭子，那么它一定是一只鸭子

levelup.gitconnected.com](/code-smell-134-specialized-business-collections-ce63d851792b) 

# 更多信息

[](https://blog.devgenius.io/what-exactly-is-a-name-part-ii-rehab-fc5058b3ff1) [## 名字到底是什么？第二部分:康复

### 我们都同意:好名声永远是最重要的。让我们找到他们。

blog.devgenius.io](https://blog.devgenius.io/what-exactly-is-a-name-part-ii-rehab-fc5058b3ff1) 

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

Mick Haupt 在 [Unsplash](https://unsplash.com/s/photos/collector) 上拍摄的照片

> 编程的阿尔茨海默定律:看着你两个多星期前写的代码，就像看着你第一次看到的代码。

丹·赫维茨

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)