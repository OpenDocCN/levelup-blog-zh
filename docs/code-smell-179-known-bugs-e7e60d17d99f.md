# 代码气味 179 —已知错误

> 原文：<https://levelup.gitconnected.com/code-smell-179-known-bugs-e7e60d17d99f>

## 每个软件都有一个已知缺陷列表。为什么？

![](img/69efe65ff051126109d5af30a861fd73.png)

> *TL；DR:不要跟踪 bug。修好它们。*

# 问题

*   难以跟踪的列表
*   技术债务
*   功能债务

# 解决方法

1.  不要再称它为 [Bug](https://mcsee.medium.com/stop-calling-them-bugs-370db4691360)
2.  重现[缺陷](https://mcsee.medium.com/stop-calling-them-bugs-370db4691360)。
3.  用自动化覆盖场景
4.  做出最直接的修复(甚至是硬编码解决方案)
5.  重构

欢迎来到 TDD！

# 语境

我们不喜欢被打断。

然后，我们创建列表，延迟修复和解决方案。

我们应该能够很容易地改变软件。

如果我们不能快速修复和修正，我们需要改进我们的软件。

而不是通过创建待办事项列表。

# 示例代码

## 错误的

```
function divide($numerator, $denominator) {
  return $numerator / $denominator;  
  // FIXME denominator value might be 0
  // TODO Rename function
}
```

## 对吧

```
function integerDivide($numerator, $denominator) {
  if (denominator == 0) {
    throw new DivideByZero();
  }
  return $numerator / $denominator;  
}// we pay our debts
```

# 侦查

[X]自动

我们需要避免产生错误和问题。

# 标签

*   技术债务

# 结论

我们需要在工程方面阻止 bug 并发布追踪器。

当然，客户需要跟踪他们的发现，我们需要尽快解决这些问题。

# 关系

[](/code-smell-148-todos-5a23d024ad70) [## 气味代码 148 — ToDos

### 我们为未来的自己购买债务。是回报的时候了

levelup.gitconnected.com](/code-smell-148-todos-5a23d024ad70) 

# 更多信息

[著名的 bug](https://en.wikipedia.org/wiki/List_of_software_bugs)

[](https://codeburst.io/stop-calling-them-bugs-370db4691360) [## 别再叫他们“虫子”了

### 让我们正确地命名事物

codeburst.io](https://codeburst.io/stop-calling-them-bugs-370db4691360) 

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

Justin Lauria 在 [Unsplash](https://unsplash.com/s/photos/bug) 上拍摄的照片

> 一般来说，在修复一个 bug 之前等待的时间越长，修复的成本(时间和金钱)就越高。

乔尔·斯波尔斯基

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)