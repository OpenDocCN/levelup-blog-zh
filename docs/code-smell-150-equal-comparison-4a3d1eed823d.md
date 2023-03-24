# 代码气味 150 —同等比较

> 原文：<https://levelup.gitconnected.com/code-smell-150-equal-comparison-4a3d1eed823d>

## 每个开发者都平等地比较属性。他们错了。

![](img/66b0c189811046dfe241dc51959c80cf.png)

> *TL；DR:不要导出比较，只做比较。*

# 问题

*   封装中断
*   代码复制
*   信息隐藏违规
*   拟人违反

# 解决方法

1.  在单个方法中隐藏比较

# 语境

我们的代码中大量使用了属性比较。

我们需要关注行为和责任。

与其他对象进行比较是一个对象的责任。不是我们自己的。

不成熟的优化器会告诉我们这是低性能的。

我们应该向他们询问真实的证据，对比更容易维护的解决方案。

# 示例代码

## 错误的

```
if (address.street == 'Broad Street') { if (location.street == 'Bourbon St') {// 15000 usages in a big system  
// Comparisons are case sensitive
```

## 对吧

```
if (address.isAtStreet('Broad Street') {
    }// ...if (location.isAtStreet('Bourbon St') {
    }  
// 15000 usages in a big system function isAtStreet(street) {
  // We can change Comparisons to case sensitive in just one place. 
}
```

# 侦查

[X]半自动

我们可以使用语法树来检测属性比较。

和许多其他气味一样，原始类型也有很好的用途。

# 标签

*   包装

# 结论

我们需要把责任放在一个地方。

攀比就是其中之一。

如果我们的一些业务规则发生变化，我们需要改变一个*单点*。

# 关系

[](https://blog.devgenius.io/code-smell-63-feature-envy-ac1f93cf8dce) [## 代码气味 63 —特征羡慕

### 如果你的方法是嫉妒和不信任授权，你应该开始这样做。

blog.devgenius.io](https://blog.devgenius.io/code-smell-63-feature-envy-ac1f93cf8dce) [](https://blog.devgenius.io/code-smell-101-comparison-against-booleans-958b112a560) [## 代码气味 101 —与布尔比较

### 当与布尔比较时，我们执行魔法转换并得到意想不到的结果

blog.devgenius.io](https://blog.devgenius.io/code-smell-101-comparison-against-booleans-958b112a560) 

# 信用

照片由 [Unsplash](https://unsplash.com/s/photos/scale?) 上的 [Piret Ilver](https://unsplash.com/@saltsup) 拍摄

> *行为是软件最重要的。是用户所依赖的。用户喜欢我们添加行为(假设这是他们真正想要的)，但是如果我们改变或删除他们依赖的行为(引入错误)，他们就会不再信任我们。*

*迈克尔·费瑟斯*

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级别提升编码](https://levelup.gitconnected.com/)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)
*   🚀👉 [**软件工程师的顶级工作**](https://jobs.levelup.dev/)