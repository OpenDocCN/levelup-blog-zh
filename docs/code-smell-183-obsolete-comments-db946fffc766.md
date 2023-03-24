# 代码气味 183 —过时的注释

> 原文：<https://levelup.gitconnected.com/code-smell-183-obsolete-comments-db946fffc766>

## *评论是一股码味。过时的注释是一种真正的危险，没有人维护那些不能执行的东西。*

![](img/b203041d3572e89045a739c7826ef14d.png)

> *TL；博士:不要相信评论。他们死了。*

# 问题

*   错误的文档
*   可维护性

# 解决方法

1.  用测试替换注释

# 重构

[](/refactoring-005-replace-comment-with-function-name-76dc38114502) [## 重构 005 —用函数名替换注释

### 评论应该增加价值。还有函数名。

levelup.gitconnected.com](/refactoring-005-replace-comment-with-function-name-76dc38114502) 

# 语境

我们知道注释对我们的代码几乎没有任何价值。

我们需要把评论限制在非常重要的决定上。

由于大多数人改变逻辑，忘记更新评论，他们可能很容易过时。

# 示例代码

## 错误的

```
void Widget::displayPlugin(Unit* unit)
{
    // TODO the Plugin will be modified soon, 
    // so I don't implement this right now
    if (!isVisible) {
        // hide all widgets
        return;
    }
}
```

## 对吧

```
void Widget::displayPlugin(Unit* unit)
{ 
    if (!isVisible) {
        return;
    }
}
```

# 侦查

[X]半自动

我们可以警告代码中的注释，并尝试删除它们。

# 例外

*   非常重要的设计决策

# 标签

*   评论

# 结论

在添加评论之前，我们需要思考。一旦它进入了代码库，就超出了我们的控制，随时都可能开始说谎。

# 关系

[](https://blog.devgenius.io/code-smell-05-comment-abusers-feec3aeb926) [## 代码气味 05 —评论滥用者

### 代码有很多注释。注释与实现联系在一起，很难维护。

blog.devgenius.io](https://blog.devgenius.io/code-smell-05-comment-abusers-feec3aeb926) [](/code-smell-152-logical-comment-750eadc84c09) [## 代码气味 152 —逻辑注释

### 暂时的攻击可能是永久的

levelup.gitconnected.com](/code-smell-152-logical-comment-750eadc84c09) [](/code-smell-151-commented-code-3a6feaeedb93) [## 代码气味 151 —注释代码

### 新手不敢拆代码。许多老年人也是如此。

levelup.gitconnected.com](/code-smell-151-commented-code-3a6feaeedb93) 

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

沃洛季米尔·赫里先科在 [Unsplash](https://unsplash.com/s/photos/obsolete) 上的照片

> *过时的注释倾向于从它们曾经描述过的代码中迁移出来。它们成为代码中不相关和误导的浮动岛。*

*鲍伯·马丁*

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)