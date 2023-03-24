# 代码气味 155 —多个承诺

> 原文：<https://levelup.gitconnected.com/code-smell-155-multiple-promises-67cccd8795c>

## 你有承诺。你需要等待。等他们都来了

![](img/192ce5415d7f0e2e65b56df3db1200dd.png)

> *TL；博士:不要以一种分类的方式来阻碍自己。*

# 问题

*   非决定论
*   性能瓶颈

# 解决方法

1.  一次等所有承诺。

# 语境

我们在学习操作系统的时候听说过信号量。

无论顺序如何，我们都应该等到所有条件都满足。

# 示例代码

## 错误的

```
async fetchOne() { /* long task */ }
async fetchTwo() { /* another long task */ }async fetchAll() {
  let res1 = await this.fetchOne(); 
  let res2 = await this.fetchTwo();
  // they can run in parallel !!  
}
```

## 对吧

```
async fetchOne() { /* long task */ }
async fetchTwo() { /* another long task */ }async fetchAll() {
  let [res3, res4] = await Promise.all([this.fetchOne(), this.fetchTwo()]);
  //We wait until ALL are done
}
```

# 侦查

[X]半自动

这是一种语义气味。

我们可以告诉我们的棉绒找到一些与承诺等待相关的模式。

# 标签

*   表演

# 结论

我们需要尽可能接近[真实世界的](https://medium.com/@mcsee/what-is-software-9a78c1172cf9)商业规则。

如果规则规定我们需要等待所有的操作，我们就不应该强制某个特定的顺序。

# 信用

谢谢你的主意

[](https://twitter.com/1542249552480174081) [## JavaScript 不可用。

### 编辑描述

twitter.com](https://twitter.com/1542249552480174081) 

阿尔文·马赫穆多夫在 [Unsplash](https://unsplash.com/s/photos/flowers-boyfriend) 上的照片

> 据我所知，JavaScript 是唯一一种人们觉得在开始使用之前不需要学习的语言。

*道格拉斯·克洛克福特*

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)