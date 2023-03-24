# 关于 JavaScript 中的 var、let 和 const，您需要了解的唯一一篇文章

> 原文：<https://levelup.gitconnected.com/the-one-and-only-article-you-need-to-know-about-var-let-and-const-in-javascript-d51562a17f47>

![A](img/e335c4dacc10fa5bf9454d1eacca89b2.png)  A 你是否对 JavaScript 中声明变量的不同方式感到有些困惑？别担心，你不是一个人！但是不要害怕，因为 var、let 和 const 实际上是超级简单的概念，可以让你像专业人士一样立刻声明变量。所以拿起你最喜欢的编码工具包，准备好永远告别变量困惑吧！

![](img/a72c8118758c515f7ea7d1df2f370f48.png)

照片由[特雷西·亚当斯](https://unsplash.com/@tracycodes?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 JavaScript 中，var、let 和 const 都是用于声明变量的关键字。他们每个人都有自己特定的特征和行为。以下是何时使用它们的总结:

*   var:var 关键字用于在 JavaScript 中声明变量。对于声明变量来说，它是最古老、支持最广泛的关键字，但是与 let 和 const 相比，它有一些限制。
*   let:let 关键字用于声明稍后可以在代码中重新分配的变量。它类似于 var，但更现代，有更严格的作用域规则。当您需要在代码的后面重新分配变量时，let 通常比 var 更受欢迎。
*   const:const 关键字用于声明不能在代码中重新分配的变量。它类似于 let，但更严格，只能用于在声明时用值初始化的变量。当你不需要重新分配一个变量时，const 通常比 let 更好。

这里有一个例子来说明 var、let 和 const 之间的区别:

```
var x = 5;
x = 10; // valid, because x was declared with var and can be reassigned
let y = 5;
y = 10; // valid, because y was declared with let and can be reassigned
const z = 5;
z = 10; // invalid, because z was declared with const and cannot be reassigned 
```

虽然在现代 JavaScript 中，let 和 const 通常比 var 更适合用来声明变量，但是在某些情况下仍然有一些使用 var 的正当理由:

*   **兼容性** : var 是 JavaScript 中最古老、支持最广泛的声明变量的关键字。它在所有现代浏览器中都得到支持，并且在 JavaScript 的早期就已经存在。如果您需要支持可能不支持 let 和 constl 的旧浏览器或环境，使用 var 可能是必要的。
*   **函数级范围**:在 JavaScript 中，var 变量是函数范围的，这意味着它们只能在声明它们的函数中被访问。在您希望将变量的可见性限制到特定函数的特定情况下，这可能会很有用。let 和 const 变量是块范围的，这意味着它们只能在声明它们的块(例如{})中访问。
*   **性能**:在某些情况下，与 let 和 const 相比，使用 var 可能会产生稍好的性能，因为 var 变量是在代码执行的创建阶段创建的，而 let 和 const 变量是在执行阶段创建的。这在性能至关重要的情况下非常重要，例如在游戏或其他高性能应用程序中。

总之，虽然在现代 JavaScript 中，let 和 const 通常比 var 更适合声明变量，但仍然有充分的理由使用它们

![](img/5c7fdb823e2c7f4190f716ff6bed224c.png)

感谢您和我们一起了解 JavaScript 中的 var、let 和 const！我们希望您对自己的可变申报技能更有信心，并准备好应对任何挑战。如果你对这些变量声明有任何问题或想法，我们希望在下面的评论中听到。如果您喜欢这些内容，并且想了解 JavaScript 的最新动态，请务必关注并订阅更多有价值的技巧和诀窍。编码快乐！