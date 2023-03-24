# Javascript 闭包简单指南

> 原文：<https://levelup.gitconnected.com/javascript-closures-simple-guide-d83dfc0a02d4>

![](img/0c9af28d18f41dfbcae08c474e336cff.png)

今天我们将讨论 Javascript 中闭包的惊人力量，我们如何利用它们，以及几个例子。

闭包是 JavaScript 面试官最喜欢问的问题之一。大多数中级/有经验的开发人员都知道这个概念，如果不知道，这就给他们敲响了警钟。但是闭包有什么特别的地方让每个人都想测试它呢？

让我们从闭包的官方解释开始:

> 一个**闭包**是一个函数与对其周围状态的引用捆绑在一起(封闭)的组合**词法环境**。换句话说，闭包允许您从内部函数访问外部函数的范围。在 JavaScript 中，闭包是在每次创建函数时创建的。([https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Closures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures))

那么，用更简单的话来说，这意味着什么呢？

> **闭包**是两个函数之间的空间，一个函数定义在另一个函数中，允许我们在两个函数之间存储数据，并从两个函数中访问数据。

函数示例中的函数

让我们深入研究一组例子，从**去抖**开始。我基于一个闭包和一个用来测试知识的问题。使用反跳的最好例子是当用户键入自动完成时。我们不想在他输入的每封信上都发送请求，我们只想在完成输入后发送请求。

那么我们如何通过终结来实现这一点呢？

最简单的去抖实现

让我们继续另一个概念**处理函数**，并通过实现一个简单的 **EventEmmiter** 来演示它。在注册到一个事件时，我们想要接收一个函数来处理我们对事件的注册，如果不再需要的话，我们不想为它定义一个特殊的函数。

简单的 EventEmmiter 实现

最后一个例子是我在日常工作中遇到的。我想打开一个模态，并设置模态的类型，我希望一个函数只做一次。所以，我用了**和**的结尾。

这是一个概念的例子:

每次点击复选框时更新一些状态

所以我希望你喜欢阅读关于**闭包**和一点点**奉承**的内容，并且希望你现在更加熟悉它。对于我们中的 **React** 开发人员，这里有一篇关于`Hooks`以及他们如何实现**闭包功能的文章。**

[](https://www.netlify.com/blog/2019/03/11/deep-dive-how-do-react-hooks-really-work/) [## 深潜:React 钩子到底是怎么工作的？网络生活

### 在本文中，我们通过构建一个 React 钩子的微型克隆来重新引入闭包。这将达到两个目的——为了…

www.netlify.com](https://www.netlify.com/blog/2019/03/11/deep-dive-how-do-react-hooks-really-work/)