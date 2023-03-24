# 用 5 个词解释的类型脚本泛型

> 原文：<https://levelup.gitconnected.com/typescript-generics-explained-in-5-words-cf223a20901b>

## 打字稿的初学者体验

![](img/61c2f94611c10b16e760d902e5917af5.png)

由 [Raphael Schaller](https://unsplash.com/@raphaelphotoch?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

我是打字新手。我用 JavaScript 编码已经有 10 年了，我开始发现一些问题，我相信这些问题可以用 TypeScript 来解决。我仍然不能 100%确定它在所有项目中都有用，但库作者迁移的兴起并非巧合。

不幸的是，如果你知道 JavaScript，你基本上已经知道 TypeScript 的想法被证明是不正确的。不要误解我，如果你正在写 JavaScript，那么你肯定可以*学习* TypeScript。但这是一个学习的过程。唉，没有免费的东西。

泛型展示了为什么 TypeScript 看起来令人生畏。

这看起来不像 JavaScript，对吗？这对一个 JS 开发者来说完全陌生。它看起来像一种完全不同的语言(即 C#)。

这对我来说是一个完全的阻碍。我不想*转行*成为一名 TypeScript 开发人员，我想把它作为 JS 的扩展来学习，这感觉像是在做一件完全不同的事情。

## 实现的时刻

就像生活中的许多事情一样，我所有的研究都被一个简单的一句话的解释击败了，这个解释稍微转述了一下[打字稿 5 分钟手册](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html)。

> 泛型为类型提供参数。

就是这样。仅此而已。

短短 5 个字的打字稿突然变得更容易理解了。还有很多东西要学，但对我来说，这件事把它从混乱变成了 JS 的可读扩展。

例如，如果您想编写一个可以在多种类型上执行操作的函数，那么泛型就很有用。

一个基于。传入的参数的长度，您不介意它是字符串还是数组(因为两者都有一个. length 属性)。

或者您可能不知道什么类型将被传递给函数，但是您希望类型检查器检查返回的类型是否与被传递的类型相同。

## 为什么我被困住了

我一直喜欢通过检查和调试来学习代码。每个人都有不同的方法，但这是最适合我的方法。这次经历给我上了宝贵的一课。当谈到理解语言的核心特征时，首先找到一个解释更容易。

祝一切顺利，
尼克