# 代码气味 153 —太长的名字

> 原文：<https://levelup.gitconnected.com/code-smell-153-too-long-names-64d586d6cd31>

## 名字应该长且具有描述性。多久了？

![](img/0d8623d56483fd9a1fdcf7a46353e060.png)

> *TL；DR:名字应该足够长。不再是了。*

# 问题

*   可读性
*   认知负荷

# 解决方法

1.  使用与[映射器](https://medium.com/@mcsee/what-is-software-9a78c1172cf9)相关的名称

# 语境

由于空间和时间的限制，我们在 50 年代和 60 年代使用了非常短的名字。

现代语言不再是这种情况。

有时候我们太兴奋了。

命名是一门艺术，我们应该谨慎对待。

# 示例代码

## 错误的

```
PlanetarySystem.PlanetarySystemCentralStarCatalogEntry// Redundant
```

## 对吧

```
PlanetarySystem.CentralStarCatalogEntry
```

# 侦查

[X]半自动

我们的棉短绒可以用太长的名字来警告我们。

# 标签

*   膨胀者
*   命名

# 结论

名字长度没有[硬性规定](https://medium.com/dev-genius/what-exactly-is-a-name-part-i-the-quest-b812a4b1e0bf)。

只是启发。

# 关系

[](https://blog.devgenius.io/code-smell-33-abbreviations-ba5149c93a68) [## 代码气味 33 —缩写

### 缩写是非常重要的，这样我们看起来聪明，节省记忆和思维空间

blog.devgenius.io](https://blog.devgenius.io/code-smell-33-abbreviations-ba5149c93a68) 

# 更多信息

[名字到底是什么？—第一部分:任务](https://medium.com/dev-genius/what-exactly-is-a-name-part-i-the-quest-b812a4b1e0bf)

[名字到底是什么？—第二部分:康复](https://blog.devgenius.io/what-exactly-is-a-name-part-ii-rehab-fc5058b3ff1)

[命名的长与短](https://agileotter.blogspot.com/2009/08/long-and-short-of-naming.html)

# 信用

照片由[埃姆雷·卡拉塔什](https://unsplash.com/@emrekaratas)在 [Unsplash](https://unsplash.com/s/photos/long) 拍摄

> 许多人倾向于像宗教一样看待编程风格和语言:如果你属于一个宗教，你就不能属于其他宗教。但这种类比是另一种谬误。

*尼古拉斯·沃斯*

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)