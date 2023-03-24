# 代码气味 168 —未记录的决策

> 原文：<https://levelup.gitconnected.com/code-smell-168-undocumented-decisions-c63205d35dd0>

## 我们需要做一些改变。我们需要清楚为什么

![](img/84c3ea27a9c8a71989dc0c0c14bc1c42.png)

> *TL；DR:对您的设计或实现决策进行声明。*

# 问题

*   代码注释
*   缺乏可测试性

# 解决方法

1.  明确说明原因。
2.  将注释转换为方法。

# 语境

有时我们发现任意的规则不那么容易测试。

如果我们不能编写一个失败的测试，我们需要一个函数，用一个优秀的声明性的名字来代替注释。

# 示例代码

## 错误的

```
// We need to run this process with more memory
set_memory("512k)run_process();
```

## 对吧

```
increase_memory_to_avoid_false_positives();
run_process();
```

# 侦查

[X]半自动

这是一种语义气味。

我们可以检测评论并警告我们。

# 标签

*   评论

# 结论

代码是散文。设计决策应该是叙述性的。

# 关系

[](https://blog.devgenius.io/code-smell-05-comment-abusers-feec3aeb926) [## 代码气味 05 —评论滥用者

### 代码有很多注释。注释与实现联系在一起，很难维护。

blog.devgenius.io](https://blog.devgenius.io/code-smell-05-comment-abusers-feec3aeb926) [](https://blog.devgenius.io/code-smell-75-comments-inside-a-method-c911f8729ee1) [## 代码味道 75 —方法内部的注释

### 注释通常是一种代码味道。将它们插入到方法中需要紧急重构。

blog.devgenius.io](https://blog.devgenius.io/code-smell-75-comments-inside-a-method-c911f8729ee1) 

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

照片由[吴礼仁](https://unsplash.com/@gohrhyyan)在 [Unsplash](https://unsplash.com/s/photos/warning) 上拍摄

> *程序和人一样，都会变老。我们无法阻止衰老，但我们可以了解其原因，限制其影响并逆转一些损害。*

马里奥·富斯科

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)