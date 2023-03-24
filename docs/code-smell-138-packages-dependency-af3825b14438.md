# 代码味道 138 —包依赖性

> 原文：<https://levelup.gitconnected.com/code-smell-138-packages-dependency-af3825b14438>

## 行业趋势是尽可能避免编写代码。但这不是免费的

![](img/501a92830d9bef83a4486e5c32bed735.png)

> *TL；DR:写你的代码，除非你需要一个现有的复杂解决方案*

# 问题

*   [联轴器](https://codeburst.io/coupling-the-one-and-only-software-design-problem-869e293a9f04)
*   [安全问题](https://nakedsecurity.sophos.com/2022/05/25/poisoned-python-and-php-packages-purloin-passwords-for-aws-access/)
*   建筑复杂性
*   [包装腐败](https://www.bleepingcomputer.com/news/security/dev-corrupts-npm-libs-colors-and-faker-breaking-thousands-of-apps/)

# 解决方法

1.  导入并实现简单的解决方案
2.  依赖外部和成熟的依赖

# 语境

最近，有一种依赖难以追踪的依赖关系的趋势。

这将耦合引入到我们的设计和架构解决方案中。

# 示例代码

## 错误的

```
$ npm install --save is-odd// https://www.npmjs.com/package/is-odd
// This package has about 500k weekly downloads
// https://github.com/i-voted-for-trump/is-odd/blob/master/index.jsmodule.exports = function isOdd(value) {
  const n = Math.abs(value); 
  return (n % 2) === 1;
};
```

## 对吧

```
function isOdd(value) {
  const n = Math.abs(value); 
  return (n % 2) === 1;
};// Just solve it inline
```

# 侦查

[X]自动

我们可以检查我们的外部依赖，并坚持最小化。

我们也可以依靠某个具体的版本来避免劫持。

# 标签

*   安全性

# 结论

懒惰的程序员将重用推到了荒谬的极限。

我们需要在代码复制和疯狂重用之间取得良好的平衡。

一如既往，有经验法则但没有硬性规定。

# 更多信息

*   [中毒包裹](https://nakedsecurity.sophos.com/2022/05/25/poisoned-python-and-php-packages-purloin-passwords-for-aws-access/)
*   [包损坏](https://www.bleepingcomputer.com/news/security/dev-corrupts-npm-libs-colors-and-faker-breaking-thousands-of-apps/)
*   [版权威胁](https://qz.com/646467/how-one-programmer-broke-the-internet-by-deleting-a-tiny-piece-of-code/)
*   [软件包中的恶意软件](https://therecord.media/malware-found-in-npm-package-with-millions-of-weekly-downloads/)

# 信用

[olieman.eth](https://unsplash.com/@moneyphotos?) 在 [Unsplash](https://unsplash.com/s/photos/security-box) 拍摄的照片

感谢 Ramiro Rela 的这种味道

> 复杂会杀死人。它耗尽了开发人员的精力，使产品难以规划、构建和测试，引入了安全挑战，并导致最终用户和管理员的沮丧。

*雷·奥茨*

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)