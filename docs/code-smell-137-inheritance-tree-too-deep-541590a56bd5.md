# 代码味道 137 —继承树太深

> 原文：<https://levelup.gitconnected.com/code-smell-137-inheritance-tree-too-deep-541590a56bd5>

## *又一个糟糕的代码重用症状*

![](img/69a0950aa0505a73b68be8491eeaab05.png)

> *TL；博士:偏爱合成而非遗传*

# 问题

*   [联轴器](https://codeburst.io/coupling-the-one-and-only-software-design-problem-869e293a9f04)
*   子类化重用
*   凝聚力差
*   脆弱基类
*   方法覆盖
*   利斯科夫替代

# 解决方法

1.  打破阶级和组成他们。

# 语境

以前的文章推荐使用类作为代码重用的专门化。

我们了解到组合是一种更有效和可扩展的分享行为的方式。

# 示例代码

## 错误的

```
classdef Animaliaendclassdef Chordata < Animalia endclassdef Mammalia < Chordata endclassdef Perissodactyla < Mammalia endclassdef Equidae < Perissodactyla endclassdef Equus < Equidae 
//Equus behaviour
endclassdef EFerus < Equus
//EFerus behaviour
endclassdef EFCaballus < EFerus
//EFCaballus behaviour    
end
```

## 对吧

```
classdef Horse       
    methods        
      // Horse behavior       
    end    
end
```

# 侦查

[X]自动

许多 linters 报告*继承树的深度(DIT)* 。

# 标签

*   等级制度

# 结论

注意你的等级制度，经常打破它们。

# 关系

[](https://blog.devgenius.io/code-smell-11-subclassification-for-code-reuse-2e2f5b564204) [## 代码味道 11 —代码重用的子类化

### 代码重用是好的。但是子类化会产生静态耦合。

blog.devgenius.io](https://blog.devgenius.io/code-smell-11-subclassification-for-code-reuse-2e2f5b564204) [](https://blog.devgenius.io/code-smell-43-concrete-classes-subclassified-7cccf1545ab5) [## 代码气味 43——具体分类

### 遗产。具体的类。重用。奇妙的混合。

blog.devgenius.io](https://blog.devgenius.io/code-smell-43-concrete-classes-subclassified-7cccf1545ab5) [](https://blog.devgenius.io/code-smell-58-yo-yo-problem-1e092d3c69ff) [## 代码气味 58 —溜溜球问题

### 寻找一个具体的方法实现？来来回回，上上下下。

blog.devgenius.io](https://blog.devgenius.io/code-smell-58-yo-yo-problem-1e092d3c69ff) [](https://blog.devgenius.io/code-smell-37-protected-attributes-4607260edb2d) [## 代码气味 37 —受保护的属性

### 受保护的属性对于封装和控制对我们属性的访问非常有用。他们可能在警告我们…

blog.devgenius.io](https://blog.devgenius.io/code-smell-37-protected-attributes-4607260edb2d) [](https://mcsee.medium.com/code-smell-125-is-a-relationship-3de263ab437d) [## 代码气味 125 —“是-是”关系

### 我们在学校学过，遗传代表一种“是-是”的关系。它不是。

mcsee.medium.com](https://mcsee.medium.com/code-smell-125-is-a-relationship-3de263ab437d) 

# 更多信息

*   [耦合:唯一的问题](https://codeburst.io/coupling-the-one-and-only-software-design-problem-869e293a9f04)
*   [维基百科](https://en.wikipedia.org/wiki/Cyclomatic_complexity)

> *软件实体(类、模块、功能等。)应该对扩展开放，但对修改关闭。*

伯特兰·迈耶

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)