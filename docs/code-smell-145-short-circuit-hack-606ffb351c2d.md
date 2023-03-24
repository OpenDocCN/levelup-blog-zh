# 代码气味 145 —短路黑客

> 原文：<https://levelup.gitconnected.com/code-smell-145-short-circuit-hack-606ffb351c2d>

## *不要使用布尔评估作为可读性捷径*

![](img/c494b3c819dca9aa39018541dbeb4f4e.png)

> *TL；DR:副作用函数不要用布尔比较。*

# 问题

*   可读性
*   副作用

# 解决方法

1.  将[短路](https://medium.com/javarevisited/code-smell-140-short-circuit-evaluation-8afa1015ae11)转换为 IFs

# 语境

聪明的程序员喜欢编写粗糙和晦涩的代码，即使没有强有力的证据证明这种改进。

过早的优化总是损害可读性。

# 示例代码

## 错误的

```
userIsValid() && logUserIn();// this expression is short circuit
// Does not value second statament
// Unless the first one is truefunctionDefinedOrNot && functionDefinedOrNot();// in some languages undefined works as a false
// If functionDefinedOrNot is not defined does
// not raise an erron and neither runs
```

## 对吧

```
if (userIsValid()) {
    logUserIn();
}if(typeof functionDefinedOrNot == 'function') {  
    functionDefinedOrNot();
}
// Checking for a type is another code smell
```

# 侦查

[X]半自动

我们可以检查函数是否不纯，将短路改为 if。

一些真实的棉绒警告我们这个问题

# 标签

*   过早优化

# 结论

不要试图显得聪明。

我们不再是 50 年代了。

成为团队开发者。

# 关系

[](https://medium.com/javarevisited/code-smell-140-short-circuit-evaluation-8afa1015ae11) [## 代码气味 140 —短路评估

### 我们在最初的编程课程中学习短路。我们需要记住为什么。

medium.com](https://medium.com/javarevisited/code-smell-140-short-circuit-evaluation-8afa1015ae11) [](https://blog.devgenius.io/code-smell-06-too-clever-programmer-bffec35daf0b) [## 代码气味 06——太聪明的程序员

### 难以阅读的代码。没有语义的名字很棘手。有时使用语言的意外复杂性。

blog.devgenius.io](https://blog.devgenius.io/code-smell-06-too-clever-programmer-bffec35daf0b) [](/code-smell-149-optional-chaining-b8830d7206ae) [## 代码气味 149 —可选链接

### 我们的代码更加健壮和易读。但是我们把空藏在地毯下

levelup.gitconnected.com](/code-smell-149-optional-chaining-b8830d7206ae) 

# 信用

照片由 Michael Dziedzic 在 Unsplash 上拍摄

> 计算机是一台愚蠢的机器，有能力做非常聪明的事情，而计算机程序员是聪明的人，有能力做非常愚蠢的事情。简而言之，他们是天作之合。

*比尔·布莱森*

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) 

# 分级编码

感谢您成为我们社区的一员！更多内容见[升级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在改造理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)