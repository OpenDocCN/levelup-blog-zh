# JavaScript 中的 Crockford 对象

> 原文：<https://levelup.gitconnected.com/crockford-objects-in-javascript-9947a65fc5e8>

![](img/3c4f37cc6eb47df6996bc431a668c2eb.png)

照片由 Unsplash 上的 Markus Spiske 提供

来自我在 Crockford Objects 上的博客文章。

这是我在这个主题上发表的三篇不同文章的第一部分。看看这些其他的帖子；

[Crockford 构造函数](/crockford-constructors-bbfd66d58fbc)
[为什么不应该在 JavaScript 中使用 Class 关键字](https://javascript.plainenglish.io/why-you-should-not-use-classes-in-javascript-ca960d13c625)

我在看 JSFest 2018 的一个视频，其中[道格拉斯·克洛克福特](https://www.crockford.com/about.html)做了一个关于范式力量的演示。如果你有一个小时的空闲时间去看 T10，这将是一场精彩的表演。Doug 还写了一本书，每个 JavaScript 开发人员都应该阅读，名为[JavaScript:The Good Parts](https://www.amazon.com/dp/0596517742/wrrrldwideweb)。整个演示很棒，但我真的很喜欢他谈到用 JavaScript 创建对象的那部分。

![](img/88781cf3d222b1f5157eaecbed48d8ad.png)

JavaScript 不像 Java 或 C#那样是传统的面向对象语言，它被认为是具有面向对象特性的基于原型的语言。看完道格的演讲后，我想出了自己的术语，我将称之为“克罗克福德对象”。

当我刚开始学习 JavaScript 时，我学习定义一个新对象的方法是使用一个返回‘this’的函数。您可以使用“new”关键字创建新对象。这里有一个简单的例子:

您可以使用此对象的“prototype”属性向其添加函数。您可以使用 prototype 属性添加属性和函数。

**ES2015 类**

在现代浏览器和 Node.js 的当前版本中，有一个“class”关键字被添加到语言中。这样做主要是为了让使用更通用语言(如 Java 和 C#)的开发人员更熟悉这种语言。

Java 和 C#中的“class”关键字允许开发人员为对象创建模板。它和 JavaScript 中的“类”不是一回事。在下面的例子中，我们可以看到“类”的定义看起来与 C#或 Java 中的“类”相似。

这里的一个关键区别是我们如何引用“this”关键字来访问对象的属性。如果你真的想这样使用语法，我会建议使用 [TypeScript](https://www.typescriptlang.org/) 。TypeScript 提供了一个 JavaScript 的超集，具有定义良好的类型和类。

**克罗克福德物体**

Crockford 在他关于如何创建对象的演示中给出的例子是开发人员应该使用的创建对象的方法。它不需要“class”关键字或“prototype”属性来定义对象。它也不需要使用“new”关键字来实例化新对象。

在上面的例子中，我们有一个带有内部函数的函数。“getName”函数是一个闭包。JavaScript 中的闭包可以访问父函数的所有参数，所以我们可以访问“name”属性。我们从“createThing”函数中返回一个新对象，它只包含我们想要的属性和函数。

我们还使用了“Object.freeze”函数来锁定我们希望包含在对象中的参数和函数。此方法防止在定义函数后将属性和函数添加到此对象的原型中。

**总结**

Crockford 定义和创建对象的方式看起来是 JavaScript 中更好的方式。这些 Crockford 对象是用工厂函数定义的。在传统的面向对象语言中，工厂是实例化新对象的常见模式。

应该注意的是，在 JavaScript 中以这种方式创建对象会有一些额外的开销，但是在大多数情况下，这不应该阻止您使用这种模式。