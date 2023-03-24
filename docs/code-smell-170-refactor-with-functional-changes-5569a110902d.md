# 代码味道 170 —通过功能变化进行重构

> 原文：<https://levelup.gitconnected.com/code-smell-170-refactor-with-functional-changes-5569a110902d>

## 发展是伟大的。重构很神奇。不要同时制作

![](img/42dcc77b254ffac67c18477738a5b7c5.png)

> *TL；DR:不要同时改变功能和重构。*

# 问题

*   很难检查解决方案
*   合并冲突

# 解决方法

1.  重构时永远不要改变功能

# 语境

有时我们发现为了进一步的开发需要重构。

我们是学习专家。

我们应该暂时搁置我们的解决方案。进行重构，继续我们的解决方案。

# 示例代码

## 错误的

```
getFactorial(n) {
  return n * getFactorial(n);
}

// Rename and Change

factorial(n) {
  return n * factorial(n-1);
}

// This is very small example
// Things go works while dealing with huge code
```

## 对吧

```
getFactorial(n) {
  return n * getFactorial(n);
}// ChangegetFactorial(n) {
  return n * getFactorial(n-1);
}// Run the testsfactorial(n) {
  return n * factorial(n-1);
}// Rename
```

# 侦查

这是重构的味道。

[X]手册

# 标签

*   重构

# 结论

我们应该使用物理令牌。

我们要么处于重构阶段，要么处于开发阶段。

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

图片由 [Dannie Jing](https://unsplash.com/@dannie_jing) 在 [Unsplash](https://unsplash.com/s/photos/circus) 上拍摄

> 当我研究代码时，重构让我达到了更高层次的理解，否则我会错过。那些将理解重构视为无用的代码修改的人没有意识到他们从来没有看到隐藏在混乱背后的机会。

马丁·福勒

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)