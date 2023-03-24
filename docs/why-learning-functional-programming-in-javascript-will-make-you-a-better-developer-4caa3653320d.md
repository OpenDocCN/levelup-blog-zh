# 为什么学习 JavaScript 函数式编程会让你成为更好的开发人员

> 原文：<https://levelup.gitconnected.com/why-learning-functional-programming-in-javascript-will-make-you-a-better-developer-4caa3653320d>

函数式编程(缩写为 FP)是 JavaScript 中流行的一种编程范式。JavaScript 将函数视为[一等公民](https://developer.mozilla.org/en-US/docs/Glossary/First-class_Function)，这使得用 JavaScript 编写函数式编程变得容易。

![](img/e561dba1a7f5bc65451f1d61fa8cc21d.png)

这篇文章将告诉你函数式编程的基础和好处，以及如何在 JavaScript 中使用它们。

# 什么是函数式编程？

函数式编程是一种用特定原则编写软件的方法。这个想法是这些原则将

*   使编写、测试和调试代码变得更加容易
*   使推理代码变得更容易
*   提高开发人员的工作效率
*   更多

# 函数式编程的核心原则

## 纯函数

函数应该是纯的。纯函数总是产生相同的输出，并且没有影响输出的副作用。副作用是功能控制之外的任何事情。例如，任何输入/输出(I/O ),如从数据库/文件中读取或使用`console.log`。或者甚至是静态变量。

这里有一个简单的例子

## 但是我需要输入输出？

没有 I/O 的应用程序没有那么有用。函数式编程不是要消除 I/O。相反，你应该将业务逻辑从 I/O 中分离出来。任何副作用都应该在流程的边缘处理，而不是在流程的中间。通过这样做，您获得了易于测试的纯业务逻辑。

JavaScript 不是纯粹的函数式编程语言。所以没有什么可以阻止你做你觉得舒服的事情。另一方面，Haskell 是纯函数式编程语言的一个例子。在 Haskell 中，您被迫使用函数式编程原则。

## 不可变状态

不可变状态意味着状态不应该改变。在函数式编程中，我们**复制状态**，而不是改变状态。这可能看起来违背直觉:为什么我们想要复制状态而不是改变它？

在 JavaScript 中，可以通过引用传递值。这可能很危险。让我们看一个例子。

## 递归

使用递归，列表不会使用`for`、`while`或`do...while`进行迭代，因为它们会改变状态(例如，增加计数器)。相反，在函数式编程中，使用了`map()`、`filter()`和`reduce()`等函数。

“递归”这个词吓了我很久，我不得不承认。但是在 JavaScript 中，使用这些函数可以很快看到可读性和生产率的好处。

我们来看一个例子！

# JavaScript 中的函数式编程

我已经简单地向你展示了如何使用`map()`。让我们再深入研究一下。

`map()`遍历你的列表，一次一个元素，让你用新的东西替换它。例如，这对于更新列表中所有元素的值很有用。然后它返回一个新列表，旧列表保持不变。所以它遵循了不修改状态的函数式编程原则。

还有两个你应该知道的功能，`filter()`和`reduce()`。

`filter()`可以让你从列表中过滤掉不想要的元素。这对于从列表中删除满足特定条件的元素非常有用。例如“*过滤掉比 5* 贵的水果。而且就像`map()`一样，它返回一个新的列表。

`reduce()`将让你把一个元素列表“简化”成一个单一的值。这对处理数字很有用。

这个函数对我来说是最难理解的。我推荐你查看一下关于它的 MDN 网络文档。

# 结论

现在你知道什么是函数式编程，它为什么有用，以及如何在 JavaScript 中使用它。这可能需要一些时间来适应，但这是非常值得的努力。我们讨论的函数在所有主流编程语言中都可用。这不是 JavaScript 特有的。

*在*[*Twitter*](https://twitter.com/prplcode)*，*[*LinkedIn*](https://linkedin.com/in/simeg)*，或者* [*GitHub*](https://github.com/simeg)

*最初发布于*[*prplcode . dev*](https://prplcode.dev/)

[](https://medium.com/@prplcode/membership) [## 通过我的推荐链接加入媒体-西蒙·埃格桑德🎈

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

medium.com](https://medium.com/@prplcode/membership)