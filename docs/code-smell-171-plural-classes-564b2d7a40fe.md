# 代码气味 171 —复数类

> 原文：<https://levelup.gitconnected.com/code-smell-171-plural-classes-564b2d7a40fe>

## *班级是我的宝贝*

![](img/8fe680a3fa278c1cc7fb0ae07ddc38d6.png)

> *TL；DR:类代表概念。概念是单一的。*

# 问题

*   命名
*   代码标准

# 解决方法

1.  将类重命名为单数

# 语境

给事物命名很难。

我们需要在某些规则上达成一致。

# 示例代码

## 错误的

```
class Users
```

## 对吧

```
class User
```

# 侦查

[X]自动

这是一个句法规则。

# 标签

*   命名

# 结论

用单数来命名概念。

类是概念。

# 更多信息

[](https://blog.devgenius.io/what-exactly-is-a-name-part-ii-rehab-fc5058b3ff1) [## 名字到底是什么？第二部分:康复

### 我们都同意:好名声永远是最重要的。让我们找到他们。

blog.devgenius.io](https://blog.devgenius.io/what-exactly-is-a-name-part-ii-rehab-fc5058b3ff1) 

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

Anton Malanin 在 [Unsplash](https://unsplash.com/s/photos/twins) 上拍摄的照片

> *我们仍然处于给软件开发项目命名的初级阶段。*

阿利斯泰尔·考克伯恩

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)