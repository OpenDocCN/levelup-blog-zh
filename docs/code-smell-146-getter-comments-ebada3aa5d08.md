# 代码气味 146 — Getter 注释

> 原文：<https://levelup.gitconnected.com/code-smell-146-getter-comments-ebada3aa5d08>

## *评论是一股码味。Getters 是另一种代码味道。你猜怎么着？*

![](img/23001470f1cca8b6970e929666a266d0.png)

> *TL；DR:不要用吸气剂。不评论 getters*

# 问题

*   评论滥用者
*   可读性
*   吸气剂

# 解决方法

1.  删除 getter 注释
2.  移除吸气剂

# 语境

几十年前，我们习惯于评论每一种方法。即使是琐碎的事情

注释应该只描述关键的设计决策。

# 示例代码

# 错误的

```
pragma solidity >=0.5.0 <0.9.0;contract Property{
    int public price;       function getPrice() public view returns(int){ /* returns the Price  */ return price;
    }
}
```

# 对吧

```
pragma solidity >=0.5.0 <0.9.0;contract Property{
    int public _price;       function price() public view returns(int){        
        return _price;
    }
}
```

# 侦查

[X]半自动

我们可以检测一个方法是否是 getter 并有注释。

# 例外

这个函数需要一个注释，这个注释偶然是一个 getter，并且这个注释与一个设计决策相关

# 标签

*   评论

# 结论

不要评论 getters。

它们没有增加真正的价值，并且会使你的代码膨胀。

# 关系

[](https://blog.devgenius.io/code-smell-05-comment-abusers-feec3aeb926) [## 代码气味 05 —评论滥用者

### 代码有很多注释。注释与实现联系在一起，很难维护。

blog.devgenius.io](https://blog.devgenius.io/code-smell-05-comment-abusers-feec3aeb926) [](https://blog.devgenius.io/code-smell-68-getters-68571a0f7fa8) [## 代码气味 68 —吸气剂

### 得到的东西是广泛而安全的。但这是一种非常糟糕的做法。

blog.devgenius.io](https://blog.devgenius.io/code-smell-68-getters-68571a0f7fa8) [](https://blog.devgenius.io/code-smell-01-anemic-models-f9fb5a1323b3) [## 代码气味 01——贫血模型

### 你的对象是一堆没有行为的公共属性。

blog.devgenius.io](https://blog.devgenius.io/code-smell-01-anemic-models-f9fb5a1323b3) 

# 信用

照片由 Reimond de Zuñ iga 在 Unsplash 上拍摄

> *代码应该非常有表现力，以避免大部分的注释。会有一些例外，但是我们应该把评论看作是“表达的失败”,直到被证明是错误的。*

罗伯特·马丁

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)