# 代码气味 174 —属性中的类名

> 原文：<https://levelup.gitconnected.com/code-smell-174-class-name-in-attributes-f45bbccde106>

## 名字中的冗余是一种不好的气味。名称应该是上下文相关的

![](img/4e90fae8486a889b97e7e3f367fe1eb0.png)

> *TL；DR:不要在你的属性前加上你的类名*

# 问题

*   不是上下文名称

# 解决方法

1.  从属性中删除类前缀

# 语境

这是一种命名的味道，我们不应该孤立地阅读属性，名字是与上下文相关的。

# 示例代码

## 错误的

```
public class Employee {
   String empName = "John";
   int empId = 5;
   int empAge = 32;
}
```

## 对吧

```
public class Employee {
   String name;
   int id; // Ids are another smell
   int age; // Storing the age is yet another smell
}
```

# 侦查

[X]半自动

当前缀中包含全名时，我们的 linters 可以警告我们。

# 标签

*   命名

# 结论

谨慎命名是一项非常重要的任务。

我们需要以行为命名，而不是类型或数据

# 关系

[](/code-smell-141-iengine-avehicle-implcar-a23ff338299f) [## 气味代码 141 —发动机、车辆、汽车

### 你在野外见过工程师吗？

levelup.gitconnected.com](/code-smell-141-iengine-avehicle-implcar-a23ff338299f) [](https://blog.devgenius.io/code-smell-96-my-objects-69fe32c670ad) [## 代码气味 96 —我的物品

### 你没有自己的物品。

blog.devgenius.io](https://blog.devgenius.io/code-smell-96-my-objects-69fe32c670ad) 

# 更多信息

[](https://blog.devgenius.io/what-exactly-is-a-name-part-ii-rehab-fc5058b3ff1) [## 名字到底是什么？第二部分:康复

### 我们都同意:好名声永远是最重要的。让我们找到他们。

blog.devgenius.io](https://blog.devgenius.io/what-exactly-is-a-name-part-ii-rehab-fc5058b3ff1) 

*   [Linux 提示](https://linuxhint.com/java-class-attributes/)

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

[凤凰韩](https://unsplash.com/@phienix_han)在 [Unsplash](https://unsplash.com/s/photos/mushroom) 上的照片

> *抄袭跳过理解。理解是你成长的方式。你必须理解为什么有些东西会起作用，或者为什么有些东西是这样的。当你复制它的时候，你会想念它。你只是改变了最后一层的用途，而不是理解下面的所有层。*

*杰森·弗里德*

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)