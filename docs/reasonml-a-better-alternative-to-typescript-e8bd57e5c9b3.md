# reason ml:TypeScript 的更好替代方案

> 原文：<https://levelup.gitconnected.com/reasonml-a-better-alternative-to-typescript-e8bd57e5c9b3>

![](img/48dd4391caaa9cca23170a53262f2f3d.png)

我前面已经谈到了不使用 TypeScript 的原因，但是没有提供替代方案。在本文中，我将向您介绍 [ReasonML](https://reasonml.github.io/) ，它是 TypeScript 的一个更好的替代方案。

[](https://medium.com/javascript-in-plain-english/7-really-good-reasons-not-to-use-typescript-166af597c466) [## 不使用 TypeScript 的 7 个非常好的理由

### 有很多理由使用 TypeScript，但我会给你 7 个不使用的理由。

medium.com](https://medium.com/javascript-in-plain-english/7-really-good-reasons-not-to-use-typescript-166af597c466) 

ReasonML 并不完全是一种新语言，或者根本不是一种语言。它基于 [OCaml](https://ocaml.org/) ，而 OCaml 又基于 ml 语言。这些语言和其他一些语言(比如 Rust、Scala 或 Elm)被认为是 ML 家族的成员。他们的主要特点是专注于函数式编程、强类型和不变性。

ResonML 基本上是 OCaml 的工具链和语法改进，使其非常适合 web 开发。正如您可能猜到的那样，它编译成 JavaScript，让您可以轻松地与现有的 JS 包进行交互。但是，它在一些关键概念上不同于 TypeScript。

首先，ReasonML 保证了编译时**和运行时**的类型安全。这是从 OCaml 继承的一点东西，我不能强调这有多重要，因为 TypeScript 不能保证编译后的类型安全。此外，虽然 TypeScript 会警告您类型不匹配但仍然可以编译，但 ReasonML 根本不会编译。这可能有点苛刻，但我认为由此带来的内心平静是值得的。

其次，ReasonML 是基于 OCaml 的，OCaml 流传很广，存在了几十年。它按照最初设计的方式实现函数式编程，并且本身是不可变的。另一方面，TypeScript 试图成为一种通用的语言，实现函数式和面向对象的范例。因此，这两者的实现都很差。

第三，ReasonML 只是比 TypeScript 做得更好。它提供了人们在 TypeScript 中喜欢的所有特性，比如类型推断、接口(在 ReasonML 中称为记录)和林挺。但由于其功能性遗产，它在所有这些方面做得更好。例如，考虑以下 TypeScript 中的示例:

从这段代码中可以看出，TypeScript 假设`x`和`y`的类型应该是`any`，以及返回值。ReasonML 中的相同代码:

ReasonML 推断出`a`、`b`的类型和返回值必须是整数，如果你试图传入一个字符串，它会警告你。

ReasonML 也有一个庞大的社区，并得到脸书的支持，所以没有理由担心支持和缺乏材料。而且，由于您可以开箱即用地使用任何 JS 包，所以没有什么是您不能用 ReasonML 做的。

另外，ReasonML 还可以直接编译成 OCaml，而 OCaml 又可以编译成[本机代码](https://reasonml.github.io/docs/en/native)。是的，您可以使用相同的代码库开发真正的本地应用程序以及 web 应用程序！

# 例子

希望我已经说服你至少给 ReasonML 一个尝试。以下是一些常见原因语法的示例:

感谢您的阅读！让我知道你对理性的看法，以及你希望我写什么样的理性教程。另外，请随意查看我的其他文章:

[](https://medium.com/javascript-in-plain-english/10-javascript-interview-questions-for-2020-697b40de9480) [## 2020 年 10 个 JavaScript 面试问题

### 随着对 JS 开发人员需求的增长，你必须做好准备。这里有 10 个问题可以帮助你…

medium.com](https://medium.com/javascript-in-plain-english/10-javascript-interview-questions-for-2020-697b40de9480) [](https://medium.com/javascript-in-plain-english/to-infinity-and-beyond-with-javascript-proxy-api-8d4f7a26c8dc) [## 使用 JavaScript 代理 API 无限超越

### 代理 API 是一个高级的概念，但是如果你想掌握 JavaScript，代理 API 是你绝对需要的…

medium.com](https://medium.com/javascript-in-plain-english/to-infinity-and-beyond-with-javascript-proxy-api-8d4f7a26c8dc) [](https://medium.com/javascript-in-plain-english/no-you-do-not-need-windows-or-mac-to-develop-with-javascript-460e1ac9e494) [## 不，你不需要 Windows 或 Mac 来开发 JavaScript

### 在这篇文章中，我会给你很好的理由来说服你尝试用 JavaScript 开发 Linux

medium.com](https://medium.com/javascript-in-plain-english/no-you-do-not-need-windows-or-mac-to-develop-with-javascript-460e1ac9e494)