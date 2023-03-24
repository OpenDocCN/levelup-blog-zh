# 代码气味 186 —硬编码的业务条件

> 原文：<https://levelup.gitconnected.com/code-smell-186-hardcoded-business-conditions-e0a22e6e8e68>

## *你是 FTX，你的代码允许特殊情况*

![](img/3bb59ca1ef79ed4a4e4b06a92a760248.png)

> *TL；DR:不要在代码中添加硬性的业务规则。*

# 问题

*   违反开放/封闭原则
*   硬编码
*   易测性

# 解决方法

1.  具体化条件。
2.  创建配置选项并设置配置行为的异常。
3.  不要使用[设置/配置](https://blog.devgenius.io/code-smell-29-settings-configs-39188e5b8316)。

# 语境

据路透社报道，在最近的 FTX 丑闻中，有一个硬编码条件，跳过对自己投资组合的风险控制。

代码是明确的，开发人员知道这条规则。

# 示例代码

## 错误的

```
if (currentExposure > 0.15 && customer != "Alameda") {
  // Be extra careful not to liquidate
  liquidatePosition();
}
```

## 对吧

```
customer.liquidatePositionIfNecessary(0.15);

// This follows Tell, Don't ask principle
```

# 侦查

[X]半自动

我们可以搜索主要的硬编码条件(与原始类型相关)。

我们可能会有比实际问题更多的假阳性。

# 标签

*   硬编码

# 结论

如果你做代码评审，要特别注意这种硬编码。

# 关系

[](/code-smell-133-hardcoded-if-conditions-8cc7398fbe90) [## 代码气味 133 —硬编码 IF 条件

### 硬编码没问题。在短时间内

levelup.gitconnected.com](/code-smell-133-hardcoded-if-conditions-8cc7398fbe90) [](https://blog.devgenius.io/code-smell-29-settings-configs-39188e5b8316) [## 代码气味 29 —设置/配置

### 在控制板中改变系统行为是客户的梦想。软件工程师的噩梦。

blog.devgenius.io](https://blog.devgenius.io/code-smell-29-settings-configs-39188e5b8316) 

# 更多信息

*   [路透社对 FTX 坠机事件的报道](https://www.reuters.com/technology/how-secret-software-change-allowed-ftx-use-client-money-2022-12-13/)

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

由 [Alexander Mils](https://unsplash.com/@alexandermils) 在 [Unsplash](https://unsplash.com/s/photos/steal-money) 上拍摄的照片

> *计算机科学颠倒了常态。在正常的科学中，你被给定了一个世界，你的工作就是找出其中的规律。在计算机科学中，你给计算机规则，它创造世界。*

*艾伦·凯*

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)