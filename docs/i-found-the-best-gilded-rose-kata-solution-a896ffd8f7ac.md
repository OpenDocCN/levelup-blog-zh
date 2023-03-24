# 资深开发者如何解决镀金玫瑰形？

> 原文：<https://levelup.gitconnected.com/i-found-the-best-gilded-rose-kata-solution-a896ffd8f7ac>

## 经历[桑迪梅斯](https://sandimetz.com/)镀金玫瑰形的解决方案

![](img/fd9cb2ae1af698ce66186a7f9a3afe10.png)

来自[像素](https://www.pexels.com/photo/female-software-engineer-coding-on-computer-3861972/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)的[摄影](https://www.pexels.com/@thisisengineering?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)

最近发现了桑迪·梅斯。[她在 2014 年举办了一场会议会谈](https://www.youtube.com/watch?v=8bZh5LMaSmE&ab_channel=Confreaks)。它是关于重复和如何避免糟糕的抽象。

我真的很喜欢她镀金玫瑰形的解决方案。并决定用 Java 重新编写，以便更好地理解。

如果你感兴趣，请继续阅读。

# 问题

这是我们面临的实际问题。通读要求，然后我们可以开始。

[](https://github.com/emilybache/GildedRose-Refactoring-Kata/blob/master/GildedRoseRequirements.txt) [## Emily bache/GildedRose-重构-形

### 镀金玫瑰需求规格你好，欢迎加入镀金玫瑰团队。如你所知，我们是一家小旅馆，有最好的…

github.com](https://github.com/emilybache/GildedRose-Refactoring-Kata/blob/master/GildedRoseRequirements.txt) 

我已经把原来的形代码贴在下面了。为了便于参考。代码中充满了神奇的字符串和数字。大量的缩进和代码很难理解。

# 解决办法

基于 Sandi 的 Ruby 解决方案的 Java 实现。

基于 Sandi 在 Ruby-author 中的解决方案，用 Java 实现

# 最后一项要求

为了完成这个形，你需要实现`Conjured`类并将其添加到`SPECIALIZED_CLASSES`。更新测试。镀金玫瑰形完成。

有意省略了实现的最后一步。

## 今天就加入 Medium！

***你为什么要*** [***亡国***](https://zivce.medium.com/membership) ***？*** 率先抛弃微服镀铬模式。其次，你会接触到很多精彩的故事。你可以从[实用程序员书架](https://medium.com/pragmatic-programmers/directory-of-pragmatic-programmer-books-on-medium-6a5cbadbd4b4)上读到 100 本左右的书。你可以看到来自 Pinterest 团队的障碍、非常有用的提示和很好的建议。你可以阅读关于谷歌云的最新进展。

这就是你每月花 5 美元(两杯咖啡)所得到的。你花 5 美元就可以阅读整个实用程序员图书馆。

*免责声明:5 美元中的 2 美元将直接支持我，并为您提供精彩的话题。*

# 结论

我见过很多关于这个问题的实现。这个卡住了。它以一种每个开发人员都理解的方式解决了这个问题。

该解决方案符合开放/封闭原则。您可以在不更改现有代码的情况下添加更多项类型。

配置可以移动。如果需要，您可以将其移动到配置文件中。或者如[桑迪](https://twitter.com/sandimetz)所说，如果它变大了，就把它转移到数据库。

有很多指标[三迪](https://twitter.com/sandimetz)提到。一个是[鞭笞](https://docs.codeclimate.com/docs/flog)。度量显示测试代码有多痛苦。

使用面向对象的方法，Sandi 的性能提高了 75%。超过了大条件法。

# 资源:

# 继续我的相关文章:

[](https://javascript.plainenglish.io/the-rule-of-three-refactoring-rule-every-great-developer-knows-6e910a8b02d8) [## 规则三——每个伟大的开发人员都知道的重构规则

### 学习第三条规则，变得更擅长重构

javascript.plainenglish.io](https://javascript.plainenglish.io/the-rule-of-three-refactoring-rule-every-great-developer-knows-6e910a8b02d8) [](/what-is-a-specification-pattern-460b32bfbf7b) [## 什么是规格模式？

### 用规格模式解开代码

levelup.gitconnected.com](/what-is-a-specification-pattern-460b32bfbf7b) [](/5-critical-things-to-know-from-97-things-every-programmer-should-know-e82bc76c1d1f) [## “每个程序员都应该知道的 97 件事”中的 5 件重要事情

### 凯文·亨尼书中的 5 个要点

levelup.gitconnected.com](/5-critical-things-to-know-from-97-things-every-programmer-should-know-e82bc76c1d1f)