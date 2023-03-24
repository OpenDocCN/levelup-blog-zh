# 碳:中级王位的继承人

> 原文：<https://levelup.gitconnected.com/carbon-the-heir-to-the-mid-level-throne-297ac58e92d9>

![](img/f25ba8823d9e54adf012fab7d5a31ac5.png)

用现代选项取代黄金编程语言的趋势正变得非常流行。但是他们能经受住时间的考验吗？

微软在他们发布 TypeScript 的时候就是这么做的，Jet Brains 发布 Kotlin 来代替 Java，现在谷歌在实验性发布 Carbon 的时候也是这么做的。

# 碳有存在的必要吗？

除非你是编程新手，否则你会知道 C++长期以来一直是最有效的编程语言之一，并且通常在计算机科学的基础水平上教授。这是因为它是一种高级语言，有很多低级功能。然而，内存损坏、没有垃圾收集以及语言及其语法的一般复杂性使得它早就应该被替换了。

[点击此链接了解如何在你的机器上测试碳含量](https://polite-bay-0071b4b10.1.azurestaticapps.net/blog/Testing%20Carbon%20Programming%20Language)

# **碳到底是什么？**

> 谷歌的首席软件工程师 Chandler Carruth 在多伦多的“CPP North”C++会议上宣布了 Carbon，这是一种实验性语言，旨在取代 c++。

使这成为一个可行选择的主要特性是 Carbon 的双向互操作性，这意味着现有 C++栈中任何地方的库都可以采用 Carbon，而不需要移植其余部分和更容易理解的语法。这是苹果从 Objective C 转换到 Swift 时采用的类似方法。

## **比喻**

为了澄清它是 C++的替代品，我认为它更像是 C++的继承者。如果你看过黑豹电影，就像它一样。黑豹(在这种情况下是铁锈)将被加冕为国王，直到一个更可行的选择杀戮者(在这种情况下是碳)出现。

这样做并不是因为上一个国王不好(这里是 C++)，而仅仅是因为时候到了。这个故事的展开需要一些时间。

谁知道它可能最终成为合法的继承者，或者另一个玩家可能会加入游戏，或者 Rust 可能会证明其批评者是错误的，并成为合法的继承者？

![](img/325e2bd196cf2a0ec54b0742fbd0f3fd.png)

# **碳的特性📋**

-引入关键字并删除旧关键字(不再有“cout 和 cin”相关错误)和更简单的语法。

-函数的输入参数是只读值，它们的指针提供了间接访问和变异

-可以通过包名导入像 Boost 和 QT 这样的 API

-泛型是 C++模板的一种更加类型安全的实现

-显式对象参数声明一个方法，并有一个接口

-开源

# **测试⚙️**

因为在这个阶段它只是实验性的，所以它需要一个运行在 WSL 上的 [LLVM(低级虚拟机)](https://llvm.org)和 [Bazel](https://bazel.build/) 来使用它。

在这个阶段没有有效的编译器或工具链。但在我的 Ubuntu WSL 上测试演示时，速度结果可以与 C++相比。但是我应该声明这只是一个简单的 Hello world 程序。

[点击此链接了解如何在你的机器上测试碳含量](https://polite-bay-0071b4b10.1.azurestaticapps.net/blog/Testing%20Carbon%20Programming%20Language)

# **开源贡献**

也许 Rust 在这个故事中可能不是你最喜欢的，你可以通过参与 GitHub 上的项目来加速继承。这需要 C++、Python 和一些 JavaScript 技能。

[GitHub 上的碳](https://github.com/carbon-language/carbon-lang)

感谢您的阅读。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入人才集体，找到一份令人惊喜的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)