# JavaScript 中使用蹦床的安全递归

> 原文：<https://levelup.gitconnected.com/safe-recursion-with-trampoline-in-javascript-dbec2b903022>

在 JavaScript 中使用递归是不安全的——考虑一下蹦床。这将递归转换为一个`while`循环，以避开 JavaScript 的限制并防止溢出。

![](img/970653f063e4bacbd9a2e2e6da5f5aaf.png)

查尔斯·程在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

递归函数的一个惯用示例是阶乘计算。我们调用`factorial`函数`n`次来得到一个结果。每次调用都会在调用栈中增加一个`factorial`函数。

JavaScript 中的递归大多是代码味道的标志，通常应该避免。原因是 JavaScript 是基于栈的语言，还没有尾部调用优化。如果您执行下面的代码片段，您将获得一个`RangeError: Maximum call stack size exceeded`。

尽管如此，还是有需要使用递归函数的情况。一个现实的方法是遍历一棵`DOM`树或任何其他类似树的结构。

## trampoline——在 JavaScript 中使用递归的安全方式。

使用`trampoline`，您将递归函数转换成一个`while`循环:

让我们重写`factorial`函数来使用`trampoline`。我们需要:

1.在基本情况下返回值。

2.返回一个在其他情况下调用的函数。

我们还为内部实现`_factorial`添加了累加器参数。

我们的`recursiveFn`可以像`factorial`一样重写，但是我们不需要累加器来跟踪状态。这种实现甚至可以安全地运行数百万次迭代。

下次你想使用递归的时候，试试`trampoline`！

可以试试下面 CodePen 里的`trampoline`。

蹦床不是一种受欢迎的技术。即使在拉姆达和洛达什，也没有蹦床帮手。

感谢凯尔·辛普森关于蹦床的灵感。