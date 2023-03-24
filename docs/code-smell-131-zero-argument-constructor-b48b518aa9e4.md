# 代码气味 131 —零参数构造函数

> 原文：<https://levelup.gitconnected.com/code-smell-131-zero-argument-constructor-b48b518aa9e4>

## 没有参数创建的对象通常是可变的和不稳定的

![](img/7a7d7e920f286eb494f4f9af05e040f6.png)

> *TL；DR:在创建对象时传递你所有的基本参数。*

# 问题

*   [易变性](https://codeburst.io/the-evil-powers-of-mutants-f803281ef82e)
*   贫血模型

# 解决方法

1.  使用一个完整的单一构造函数。
2.  避免[设置器](https://mcsee.medium.com/code-smell-28-setters-5b0e764049aa)和[吸气器](https://blog.devgenius.io/code-smell-68-getters-68571a0f7fa8)

# 语境

使用零参数构造函数和一堆 setters 来改变它是很常见的用法。

[豆子](https://en.wikipedia.org/wiki/JavaBeans)就是这种码味的一个众所周知的例子。

# 示例代码

# 错误的

```
public Person();// Anemic and mutable
```

# 对吧

```
public Person(String name, int age){
     this.name = name;
     this.age = age;
     } 
 }// We 'pass' the essence to the object 
// So it does not mutate
```

# 侦查

[X]自动

我们可以检查所有的构造函数，但是有一些误报。

无状态对象是一个有效的例子。

# 标签

*   易变性

# 结论

空构造函数是可变性提示和意外的实现问题。

我们需要研究使用方法来改进我们的解决方案。

# 关系

[](https://blog.devgenius.io/code-smell-68-getters-68571a0f7fa8) [## 代码气味 68 —吸气剂

### 得到的东西是广泛而安全的。但这是一种非常糟糕的做法。

blog.devgenius.io](https://blog.devgenius.io/code-smell-68-getters-68571a0f7fa8) [](https://blog.devgenius.io/code-smell-28-setters-5b0e764049aa) [## 气味代码 28 —设定器

### 初级程序员做的第一个练习。ide、教程和高级开发人员不断教他们这种反模式。

blog.devgenius.io](https://blog.devgenius.io/code-smell-28-setters-5b0e764049aa) [](https://blog.devgenius.io/code-smell-01-anemic-models-f9fb5a1323b3) [## 代码气味 01——贫血模型

### 你的对象是一堆没有行为的公共属性。

blog.devgenius.io](https://blog.devgenius.io/code-smell-01-anemic-models-f9fb5a1323b3) [](https://blog.devgenius.io/code-smell-40-dtos-ca35f5d8f7c9) [## 代码气味 40—dto

### dto 被广泛使用并“解决”了实际问题，是吗？

blog.devgenius.io](https://blog.devgenius.io/code-smell-40-dtos-ca35f5d8f7c9) 

# 更多信息

*   [空构造函数](https://en.wikipedia.org/wiki/Nullary_constructor)
*   [裸体模特——第一部分:设定者](https://medium.com/dev-genius/nude-models-part-i-setters-77ac784a91f3)
*   [裸体模特——第二部分:吸气剂](https://medium.com/dev-genius/nude-models-part-ii-getters-b039e5ad3427)
*   [变种人的邪恶力量](https://codeburst.io/the-evil-powers-of-mutants-f803281ef82e)

# 信用

照片由[阿德·阿德博瓦尔](https://unsplash.com/@adebowax)在 [Unsplash](https://unsplash.com/s/photos/crane) 上拍摄

> 不要担心设计，如果你倾听你的代码，一个好的设计就会出现…倾听技术人员的意见。如果他们在抱怨做出改变的困难，那么认真对待这样的抱怨，给他们时间来解决问题。

马丁·福勒

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)