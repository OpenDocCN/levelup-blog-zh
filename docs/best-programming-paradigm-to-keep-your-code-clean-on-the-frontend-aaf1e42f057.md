# 保持代码前端整洁的最佳编程范例

> 原文：<https://levelup.gitconnected.com/best-programming-paradigm-to-keep-your-code-clean-on-the-frontend-aaf1e42f057>

## Java Script 语言

## 为什么我们应该在前端使用函数式编程来保持代码库的可读性、可维护性、可重用性和可测试性。

![](img/c2190866539d65731fd7f2488e4c2ce2.png)

在 [Unsplash](https://unsplash.com/s/photos/coffee?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由 [Toa Heftiba](https://unsplash.com/@heftiba?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

在我应用**函数式编程范例**将一个可怕而庞大的源代码重构为一个更简单的代码之后，我就写了这篇文章。

我想与世界各地的前端开发者分享这个想法，为什么 FP 非常适合 JavaScript，以及一些想法来编写更好的可读、可维护、可重用和可测试的代码。

内容:

1.  问题
2.  函数式编程有什么帮助
3.  什么是函数式编程

> OO 通过封装移动部分使代码变得可以理解。FP 通过最大限度地减少活动部件来使代码变得易于理解- [迈克尔·费哲](http://twitter.com/mfeathers/status/29581296216)

# 问题

JavaScript 是客户端唯一的编程语言，这意味着它带有大量的共享状态，以便制作动画、处理 UI 事件、产生异步流程、客户端复杂的业务逻辑，以及服务器端渲染的一些额外内容。

所以，如果你不好好管理你的代码库，你迟早会失去控制。代码不可读、不可维护、不可理解，并且让其他开发人员在工作时感到疲劳。

有一些便于编程任务的编程范例，例如:

*   [命令式](https://en.wikipedia.org/wiki/Imperative_programming)
    - [程序式](https://en.wikipedia.org/wiki/Procedural_programming)将指令分组为程序。
    - [面向对象](https://en.wikipedia.org/wiki/Object-oriented_programming)将指令和它们操作的状态部分组合在一起，
*   [声明式](https://en.wikipedia.org/wiki/Declarative_programming)
    - [函数式](https://en.wikipedia.org/wiki/Functional_programming)其中期望的结果被声明为一系列函数应用的值。
    - [逻辑](https://en.wikipedia.org/wiki/Logic_programming)其中期望的结果被宣布为关于事实和规则系统的问题的答案。
    - [数学](https://en.wikipedia.org/wiki/Mathematical_programming)其中期望的结果被声明为优化问题的解。

我在这里建议的解决方案是函数式编程，因为 JavaScript 大量使用异步和基于事件的代码，而 FP 是允许我们专注于数据流和函数的最佳方法。

这种范式将帮助我们降低复杂性，即在我们的源代码中任何使软件难以理解或修改的东西。

# 函数式编程有什么帮助

在了解函数式编程如何帮助我们处理大型代码库之前，我们将通过下面的一些问题来了解编程中的五个原则:

1.  **可扩展性** *我是否经常重构我的代码以支持额外的功能？*
2.  **易于模块化**
    *如果我更改一个文件，另一个文件会受到影响吗？*
3.  **复用性**
    *是否存在大量重复？*
4.  **可测试性**
    *我要努力对我的功能进行单元测试吗？*
5.  **容易推理关于** *我的代码是不是没有结构，难以遵循？*

如果你的答案是“不”,你就没有必要再看这篇文章了。

如果“是”，函数式编程是项目中代码库的一个好方法。

> 代码可能无法运行，但在我们看来一定很美——来自我的一个同事。

*如何更好的保存我们的代码？我将在下一节解释。*

# 什么是函数式编程

> 在[计算机科学](https://en.wikipedia.org/wiki/Computer_science)，**函数式编程**是一种[编程范式](https://en.wikipedia.org/wiki/Programming_paradigm)，其中程序由[应用](https://en.wikipedia.org/wiki/Function_application)和[组成](https://en.wikipedia.org/wiki/Function_composition) [函数](https://en.wikipedia.org/wiki/Function_(computer_science))来构造。这是一个[声明式编程](https://en.wikipedia.org/wiki/Declarative_programming)范例，其中函数定义是[表达式](https://en.wikipedia.org/wiki/Expression_(computer_science))的[树](https://en.wikipedia.org/wiki/Tree_(data_structure))，每个返回一个[值](https://en.wikipedia.org/wiki/Value_(computer_science))，而不是一系列[命令式](https://en.wikipedia.org/wiki/Imperative_programming) [语句](https://en.wikipedia.org/wiki/Statement_(computer_science))，它们改变程序的[状态](https://en.wikipedia.org/wiki/State_(computer_science))。

你可以简单地认为函数式编程只是一套帮助开发人员专注于如何在编程中组织和使用函数的实践，它帮助开发人员避免:

1.  副作用
2.  减少状态突变

**与非功能代码有什么区别？**

函数式编程和非函数式编程的输出没有区别。

唯一的区别是我们编写代码的方式。不同的方法和问题解决方案。

正如我上面提到的，函数式编程是一套帮助开发人员专注于如何编写和使用好函数的实践。这些实践是一些基本概念:

*   纯函数
*   不变
*   对透明性有关的
*   作为一级实体发挥作用
*   高阶函数
*   声明式编程
*   功能组成
*   关闭
*   模式匹配
*   单子
*   库里/部分功能应用
*   懒惰评估

可以开始 google 每个关键词，自学函数式编程中的这些概念和模式。

我将在下一篇文章的后面写更多关于每一项的内容。

我希望这篇文章对你有用！你可以在[媒体](https://medium.com/@transonhoang?source=post_page---------------------------)上关注我。我也在[推特](https://twitter.com/transonhoang)上。欢迎在下面的评论中留下任何问题。我很乐意帮忙！

# 参考

1.  JavaScript 中的函数式编程:如何使用函数式技术改进您的 JavaScript 程序。
2.  函数式编程—【https://en.wikipedia.org/wiki/Functional_programming 