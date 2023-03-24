# 气味代码 141 —发动机、车辆、汽车

> 原文：<https://levelup.gitconnected.com/code-smell-141-iengine-avehicle-implcar-a23ff338299f>

## 你在野外见过工程师吗？

![](img/653e231d2c751eeac4337c8840799714.png)

> *TL；DR:不要给你的类加前缀或后缀*

# 问题

*   可读性
*   [双射](https://codeburst.io/the-one-and-only-software-design-principle-5328420712af)故障
*   实现名称

# 解决方法

1.  删除前缀和后缀
2.  根据对象的用途给它们命名

# 语境

一些语言有与数据类型、抽象类或接口相关的文化约定。

这些名字给我们的模型带来了难以理解的认知翻译。

我们必须[亲吻](https://en.wikipedia.org/wiki/KISS_principle)。

# 示例代码

## 错误的

```
public interface IEngine
{
    void Start();
}public class ACar 
{}public class ImplCar 
{}public class CarImpl
{}
```

## 对吧

```
public interface Engine
{
    void Start();
}public class Vehicle 
{}public class Car 
{}
```

# 侦查

[X]自动

如果我们有一本辞典，我们就能指出难用的名字。

# 例外

在 C#中，将“I”放在接口名中是一种常见的做法，因为如果没有它，你就无法判断它是一个接口还是一个类。

这是一种语言气味。

# 标签

*   命名

# 结论

对你的模特使用真名。

# 关系

[](https://mcsee.medium.com/code-smell-130-addressimpl-89f5209870a7) [## 代码气味 130 —地址

### 很高兴看到一个实现接口的类。更好的是理解它做什么

mcsee.medium.com](https://mcsee.medium.com/code-smell-130-addressimpl-89f5209870a7) 

# 更多信息

*   [名字里有什么:第二部康复](https://blog.devgenius.io/what-exactly-is-a-name-part-ii-rehab-fc5058b3ff1)

# 信用

照片由 Tim Mossholder 在 Unsplash 上拍摄

> 有些人，当遇到问题时，会想“我知道，我会用正则表达式。”现在他们有两个问题。

杰米·扎温斯基

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)