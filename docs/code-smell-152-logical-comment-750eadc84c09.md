# 代码气味 152 —逻辑注释

> 原文：<https://levelup.gitconnected.com/code-smell-152-logical-comment-750eadc84c09>

## *暂时的攻击可能是永久的*

![](img/2204b548f4d4512b2ff02b7330de805e.png)

> *TL；DR:不要改变代码语义来跳过代码。*

# 问题

*   可读性
*   无意揭露

# 解决方法

1.  如果你需要一个临时的黑客，让它明确
2.  依靠你的源代码控制系统

# 语境

临时修改代码是非常糟糕的开发实践。

我们可能*忘记*一些暂时的解决方案，并永远离开它们。

# 示例代码

## 错误的

```
if (cart.items() > 11 && user.isRetail())  { 
  doStuff();
}
doMore();
// Production code// the false acts to temporary skip the if condition
if (false && cart.items() > 11 && user.isRetail())  { 
  doStuff();
}
doMore();if (true || cart.items() > 11 && user.isRetail())  {
// Same hack to force the condition
```

## 对吧

```
if (cart.items() > 11 && user.isRetail())  { 
  doStuff();
}
doMore();
// Production code// Either if we need to force or skip the condition
// we can do it with a covering test forcing
// real world scenario and not the codetestLargeCartItems() {}testUserIsRetail() {}
```

# 侦查

[X]半自动

一些棉绒可能会警告我们奇怪的行为。

# 标签

*   评论

# 结论

关注点的分离在我们的职业中极其重要。

商业逻辑和黑客应该永远分开。

# 关系

[](/code-smell-135-interfaces-with-just-one-realization-9609f377e3e2) [## 代码气味 135 —只与一个实现接口

### 通用和预见未来是好的。

levelup.gitconnected.com](/code-smell-135-interfaces-with-just-one-realization-9609f377e3e2) 

# 信用

照片由 [Belinda Fewings](https://unsplash.com/@bel2000a) 在 [Unsplash](https://unsplash.com/s/photos/road-closed) 上拍摄

感谢@ [Ramiro Rela](@racter) 的提示

> 你可能不认为程序员是艺术家，但编程是一个极具创造性的职业。是基于逻辑的创意。

*约翰·罗梅洛*

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)