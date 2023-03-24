# 什么是 ECMAScript？

> 原文：<https://levelup.gitconnected.com/what-is-ecmascript-9b9e546fd1ca>

JavaScript 有许多不同的实现方式。ECMAScript 如何融入其中？

![](img/e1d4b9cb925d5201931eb23679eebceb.png)

由[詹姆斯·杰弗瑞](https://www.instagram.com/jjeffphoto)在 [Instagram](https://www.instagram.com/p/B_YJCiBjT2c) 上拍摄的照片

ECMAScript 是 JavaScript，但是 ECMAScript 这个名字表明了一个事实，即欧洲计算机制造商协会(ECMA)是控制该规范的管理机构。它比这更复杂，而且最终[规范会受到整个社区的影响](https://www.ecma-international.org/ecma-262)，但这就是它的名字的来源。

然而，关于使用什么名称的困惑是品牌和商标(非)问题[的结果](https://github.com/getify/You-Dont-Know-JS/blob/2nd-ed/get-started/ch1.md#whats-with-that-name)。建议用 JS，不要用 JavaScript，或者 ES20##。我们使用 JS 是因为它回避了商标(非)问题，使用 ES20##是因为从 ES2016 开始，它是引用规范的正确方式。

话虽如此，我们知道我们在谈论以下内容。

> …一种面向对象的编程语言，用于在主机环境中执行计算和操作计算对象。

即使我们都知道如何编写 JS，我还是想提供一个规范的概要；语言是如何发展的以及规范的范围。为此，我将提供一些历史背景，并引用规范本身的一些介绍性段落。

# ECMAScript 的起源故事

布伦丹·艾希是 JavaScript 的创始人。他在网景公司为他们的 Navigator 2.0 浏览器开发了这种语言。最终，Ecma 大会于 1997 年 6 月通过了第一版，随后在 1998 年的第二版中对其进行了完善。

2002 年，第三版出版。这是该语言开始被广泛采用的版本。

今天，这种语言基本上被所有的浏览器所采用。

# ECMAScript 引擎

JS 没有[的单数实现。每个浏览器都有自己的 ECMAScript 引擎实现。](https://en.wikipedia.org/wiki/List_of_ECMAScript_engines)

那么，ECMAScript 引擎之间的差异有多大呢？

这个答案因我们谈论的规范部分而异。在某些情况下，规范非常具体，而在其他情况下，它允许一些回旋余地。

例如，`Array.prototype.sort`必须是稳定的排序算法但不一定是特定的排序算法。这意味着每个浏览器在排序算法消耗的时间和内存上可能有所不同。

在其他情况下，标准期望非常具体的功能。例如，每个引擎都需要正确地解析下面的表达式，从`typeof null`到`"object"`，尽管这原本是一个错误。

除了特定的用例，该标准允许引擎扩展该语言。这只是文档中的一小段。

> ECMAScript 的一致性实现可能提供本规范中描述的类型、值、对象、属性和功能之外的其他类型、值、对象、属性和功能…

我不清楚这是鼓励实验的结果还是为主机环境的差异提供灵活性的结果。无论如何，灵活性允许这两种结果。

虽然，扩展规范是有边界的。

> ECMAScript 的一致性实现不得实现子条款 [16.2](https://www.ecma-international.org/ecma-262/#sec-forbidden-extensions) 中被列为禁止扩展的任何扩展。

这很有意义，因为坦率地说，完全可扩展的标准根本不是标准。

# 主机环境

如果你听说过 [Node.js](https://nodejs.org) ，那么你就会知道 js 不仅仅被浏览器使用。这就是为什么 JS 的描述包括这个通用短语。

> …在主机环境中。

这个短语也回避了要求主机环境运行 JS。下面更清楚地说明了如何使用 JS。

> 这里定义的 ECMAScript 并不打算在计算上自给自足…

因此，我们通常认为主机环境是一个浏览器，但它也可能是 Node.js 或其他东西。关键是，必须有宿主。

请记住，主机不是由规范定义的。换句话说，主机环境被抽象掉了。

> [主机环境]…不仅将提供本规范中描述的对象和其他工具，还将提供某些特定于环境的对象，这些对象的描述和行为超出了本规范的范围，除非指明它们可以提供某些可访问的属性和某些可从 ECMAScript 程序调用的功能。

总而言之，我们必须有一个遵循特定接口的主机环境，以便 JS 和主机可以互相“交谈”。想象一下通过 javascript 访问 DOM。这是我们正在讨论的内容的一个具体实现，但却是 JS 如何与主机对话的一个很好的例子。

# 结论

我希望这有助于构建一个关于 JS 如何插入浏览器、Node.js 和任何其他主机环境的更完整的心智模型。它应该有助于解释为什么不同引擎之间会有差异，JS 包之间也会有差异。