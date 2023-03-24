# 代码气味 143 —数据块

> 原文：<https://levelup.gitconnected.com/code-smell-143-data-clumps-86cfe2f36a19>

## *有些物体总是在一起。我们为什么不把他们分开？*

![](img/273cf6009c1cef335a44c8756ae85eb2.png)

> *TL；DR:让内聚的原始对象一起旅行*

# 问题

*   凝聚力差
*   重复代码
*   验证复杂性
*   可读性
*   可维护性

# 解决方法

1.  提取类
2.  寻找小物件

# 语境

这种气味是原始痴迷的朋友。

如果两个或更多的原始对象粘在一起，业务逻辑重复，它们之间有规则，我们需要在[双射](https://codeburst.io/the-one-and-only-software-design-principle-5328420712af)上找到现有的概念。

# 示例代码

# 错误的

```
public class DinnerTable
{
    public DinnerTable(Person guest, DateTime from, DateTime to)
    {
        Guest = guest; 
        From = from;
        To = to;
    }
    private Person Guest;
    private DateTime From; 
    private DateTime To;
}
```

# 对吧

```
public class TimeInterval
{
    public TimeInterval(DateTime from, DateTime tol)
    {
        // We shoud validate From < To
        From = from;
        To = to;
    }
}public DinnerTable(Person guest, DateTime from, DateTime to)
{    
    Guest = guest;
    Interval = new TimeInterval(from, to);
}
```

# 侦查

[X]半自动

基于内聚模式的检测可用于一些棉绒。

# 标签

*   内聚力

# 结论

群体行为放在正确的位置并隐藏了原始数据。

# 关系

[](https://mcsee.medium.com/code-smell-122-primitive-obsession-ad1f821f7158) [## 代码气味 122——原始痴迷

### 物品就在那里等待挑选。即使是最小的。

mcsee.medium.com](https://mcsee.medium.com/code-smell-122-primitive-obsession-ad1f821f7158) [](https://blog.devgenius.io/code-smell-01-anemic-models-f9fb5a1323b3) [## 代码气味 01——贫血模型

### 你的对象是一堆没有行为的公共属性。

blog.devgenius.io](https://blog.devgenius.io/code-smell-01-anemic-models-f9fb5a1323b3) [](https://blog.devgenius.io/code-smell-27-associative-arrays-77c36284eb5e) [## 代码味道 27 —关联数组

### [键，值]，魔术，快速，可塑性和错误修剪。

blog.devgenius.io](https://blog.devgenius.io/code-smell-27-associative-arrays-77c36284eb5e) 

# 更多信息

*   [重构大师](https://refactoring.guru/es/smells/data-clumps)
*   [维基百科](https://en.wikipedia.org/wiki/Data_clump)

# 信用

动态王在 Unsplash 上的照片

> *软件的核心是为用户解决领域相关问题的能力。所有其他功能，尽管可能很重要，也支持这一基本目的。*

埃里克·埃文斯

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)