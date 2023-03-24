# 代码气味 156 —隐式 Else

> 原文：<https://levelup.gitconnected.com/code-smell-156-implicit-else-2e5c96e64c0c>

## 我们在编程的第一天学习 if/else。然后我们忘记了其他的

![](img/d2dd2004a119680d31aaa01b1d28c9d7.png)

> *TL；大卫:明确一点。即使有别的。*

# 问题

*   可读性
*   认知负荷
*   不可预见的情况
*   [故障快速原理违反](https://codeburst.io/fail-fast-3f3f036032b0)

# 解决方法

1.  编写显式 else

# 语境

如果我们提前返回 If 句，我们可以省略 else 部分。

之后，我们[移除 IF](https://blog.devgenius.io/how-to-get-rid-of-annoying-ifs-forever-317033474484) 并使用多态。

这就是我们错过真正案例的时候。

# 示例代码

## 错误的

```
function carBrandImplicit(model) {
  if (model === 'A4') {
    return 'audi';
  }
  return 'Mercedes-Benz';
}
```

## 对吧

```
function carBrandExplicit(model) {
  if (model === 'A4') {
    return 'audi';
  }
  if (model === 'AMG') {
    return 'Mercedes-Benz';
  } // Fail Fast
  throw new Exception('Model not found);
}
```

# 侦查

[X]自动

我们可以检查语法树并解析它们，并对丢失的 else 发出警告。

我们也可以重写它们并进行突变测试。

# 标签

*   条件式

# 结论

这种气味带来很多公众的争论，*和讨厌*。

我们必须交换意见，并重视每个利弊。

# 关系

[](https://blog.devgenius.io/code-smell-102-arrow-code-3e9301e83269) [## 代码气味 102 —箭头代码

### 嵌套的 if 和 Elses 很难阅读和测试

blog.devgenius.io](https://blog.devgenius.io/code-smell-102-arrow-code-3e9301e83269) [](https://blog.devgenius.io/code-smell-36-switch-case-elseif-else-if-statements-73f0215abf2b) [## 代码气味 36 — Switch/case/elseif/else/if 语句

### 第一节编程课:控制结构。高级开发人员的教训:避免它们。

blog.devgenius.io](https://blog.devgenius.io/code-smell-36-switch-case-elseif-else-if-statements-73f0215abf2b) 

# 更多信息

[停止使用隐式 Else](https://javascript.plainenglish.io/advice-from-a-senior-dev-stop-using-the-implicit-else-2a2ecf0a3583)

[何时使用隐式 Else](https://medium.com/lost-but-coding/when-to-use-implicit-else-e891cdcfe1bd)

[](https://codeburst.io/fail-fast-3f3f036032b0) [## 快速失败

### 失败是时尚。制造比思考容易得多，失败不是耻辱，让我们把这个想法带到我们的…

codeburst.io](https://codeburst.io/fail-fast-3f3f036032b0) [](https://blog.devgenius.io/how-to-get-rid-of-annoying-ifs-forever-317033474484) [## 如何永远摆脱烦人的 if

### 为什么我们学习编程的第一条指令应该是最后使用的。

blog.devgenius.io](https://blog.devgenius.io/how-to-get-rid-of-annoying-ifs-forever-317033474484) 

## 信用

Elena Mozhvilo 在 [Unsplash](https://unsplash.com/s/photos/invisible) 上拍摄的照片

> 软件团队最大的问题是确保每个人都明白其他人在做什么。

马丁·福勒

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)