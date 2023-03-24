# Javascript 中的 Const vs Let vs Var。你应该用哪一个？

> 原文：<https://levelup.gitconnected.com/const-vs-let-vs-var-in-javascript-which-one-should-you-use-c56cf9b9e2a3>

当我们在 JavaScript 中声明一个新变量时，我们可以使用`const`、`let`或`var`。其中的每一个都有不同的用途，有些可能在特定的情况下需要，这取决于你在做什么。今天，我们将看看`const`、`let`和`var`之间的主要区别，以及何时应该使用哪一个来避免出错

![](img/5842cd36142eaf7245eac81e1032253d.png)

# 常数

我们将从`const`开始，这可能是三个中最容易理解的，也是我通常使用最多的一个。当 ES6 在 2015 年中期发布时，Const 首次被引入 JavaScript，并从此改变了变量的工作方式。

基本上，当你使用`const`给一个变量赋值时，意味着你不能在以后给这个变量另一个值。换句话说，如果你试图改变一个`const`变量，你会得到一个返回的错误。然而，如果您在一个块中设置了一个`const`语句，它将只保留在那个块中。

由于`const`是块范围的，这意味着你试图用`const`变量做的任何事情都只能在你最初编写它的代码块中使用。如果你想深入了解`const`是如何使用的，这个视频很好地解释了 const 如何工作以及什么是块范围。

![](img/84ad6c0926426398a12b0d4eebaddb82.png)

# 让

与`const`类似，`let`在块范围方面也起作用。然而，它也与`var`相似，事实上`let`确实允许您稍后在代码中更改它的值，但稍后会详细介绍。这意味着下面的代码打印 100 而不是 50。如果您在这个例子中用`const`替换`let`，您将得到一个错误。

如您所见，我们可以在稍后的代码中更改该值。对于这个例子，因为 100 是在我们对 50 个苹果的初始声明之后声明的，所以 100 是打印到控制台的内容。这可能会有点混乱，您应该小心，因为下面的代码将返回一个错误。

这将向我们抛出一个错误，因为您不能在同一个块中多次声明同一个变量。如果我们将变量`let numOfApples = 100;` 设置在它自己的块中，与`let numOfApples = 50;`分开，100 将再次打印到控制台。看一下下面的代码，自己体会一下。

![](img/84ad6c0926426398a12b0d4eebaddb82.png)

# 定义变量

在 ES6 时代之前，我们用`var`来声明变量。自从`let`和`const`的引入，Var 不再像以前那样被大量使用，但它仍然没有从 JavaScript 中移除，并且有一些独特的用途。

`var`和`let`非常相似，您可以稍后重新赋值。不过这两者的主要区别在于`let`处理块范围，而`var`根据声明的位置处理全局范围或函数范围。只要您的变量没有在任何函数中声明，`var`就可以在代码中的任何地方再次使用。

与`let`不同的是，`var`允许你任意多次声明这个变量，并且不会有错误出现。这意味着上面带有`let`的例子给了我们一个错误，而下面的例子不会。

如果你不小心的话，这是很容易被忽略的，所以只要根据你是使用`let`还是`var`来确保你使用了正确的语法。为了更好地理解这两者之间的主要区别，你可以看看这个视频，我发现它很好地解释了这两者的不同之处。

*来源:*

[https://youtu.be/2iLVFyYwyRA](https://youtu.be/2iLVFyYwyRA)

[https://youtu.be/XgSjoHgy3Rk](https://youtu.be/XgSjoHgy3Rk)

https://dev.to/sarah_chima/var-let-and-const[69e](https://dev.to/sarah_chima/var-let-and-const--whats-the-difference-69e)