# JavaScript 基础—变量、语句和条件

> 原文：<https://levelup.gitconnected.com/javascript-basics-variables-statements-and-conditionals-a67a46dfaf76>

![](img/d2f634e7ff4502408c20fad78635528a.png)

照片由[伊芙琳](https://unsplash.com/@evelynnnnn?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

JavaScript 是世界上最流行的编程语言之一。为了有效地使用它，我们必须了解它的基本知识。

在本文中，我们将了解如何定义和使用变量、if 语句和函数。

# 命名

变量名或绑定名可以是任何单词。我们的命名中也可以有数字。

它们也可能包括`$`和`_`符号。

含有特殊字符的单词不能用作变量名。

保留字列表位于[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Lexical _ grammar # Keywords](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Lexical_grammar#Keywords)

如果我们试图用这些词来命名一个 JavaScript 变量，我们会得到一个错误。

# 环境

在给定时间存在的绑定及其值的集合称为环境。当程序启动时，环境不是空的。它具有与加载到内存中的用户进行交互所需的绑定。

# 功能

函数是包装在值中的一段程序。它们可以被应用以便运行。我们通过传入参数来实现。

例如，`alert`是一个内置的浏览器功能，可以显示一个带有文本的框。

我们可以如下使用它:

```
alert('hello');
```

然后我们看到一个显示文本`'hello'`的框。

运行是一种功能，也称为调用、调用或应用它。

我们通过在产生函数值的表达式后面加上括号来运行函数。参数在括号内。一个函数中可以有 0 个或多个参数。

# Console.log

`console.log`是 JavaScript 标准库中内置的一个函数，让我们显示变量和其他表达式的值。

这让我们可以通过检查某个值是否是我们想要的来调试我们的程序。

输出将显示在控制台中。

在 Chrome 上，我们可以按 F12 来显示为表达式记录的值。

# 返回值

函数可以产生可以赋给变量的值。它们通过返回值来实现。

例如，我们可以写:

```
Math.max(1, 2, 3);
```

返回 3 个数之间的最大值。

我们可以通过编写以下内容将其记录到控制台中:

```
console.log(Math.max(1, 2, 3));
```

然后我们在控制台上得到 3。

# 控制流

如果我们没有任何控制流，那么每条语句都是一行一行地运行，没有任何东西阻止它们。

此外，除了复制和粘贴之外，没有其他方法可以重复我们想要运行的内容。

# 条件执行

我们可以让一些代码只在遇到条件语句的情况下运行。

JavaScript 为我们提供了`if`语句来做到这一点。

例如，我们可以使用它仅在满足条件时将某些内容记录到控制台:

```
if (Math.random() < 0.5) {
  console.log('you win');
}
```

然后，当`Math.random`返回小于 0.5 的值时，我们只能在控制台上看到`'you win'`。

`if`后面的大括号和圆括号用于对满足条件时运行的语句进行分组。

在这个例子中，我们用`Math.random`运行`console.log`函数，产生一个小于 0.5 的值。

通常，我们还需要处理相反的情况。

我们可以用关键字`else`和它后面的块来实现。

例如，我们可以写:

```
if (Math.random() < 0.5) {
  console.log('you win');
} else {
  console.log('you lose');
}
```

现在我们添加了一个块来处理当`Math.random`返回 0.5 或更大的数字时的情况。

我们运行`console.log`函数来记录`'you lose'`。如果我们只有两个案例，这是可行的，但通常我们有两个以上的案例。

在这种情况下，我们可以一起使用`else`关键字和`if`。

例如，我们可以写:

```
if (num < 5) {
  console.log("a");
} else if (num < 10) {
  console.log("b");
} else {
  console.log("c");
}
```

上面的代码有 3 种情况。如果`num`小于 5，我们记录`'a'`。

如果`num`小于 10，则记录`'b'`。`'c'`否则记录。

![](img/c79ad4ba5df556139eeca74205e7eb2c.png)

安德鲁·斯莫尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 结论

JavaScript 有一个命名变量的约定。我们不能使用保留字，但我们可以使用字母、数字、问号和下划线。

函数是可以重复运行的代码单元。

`if`当遇到某种情况时，语句对于运行代码很有用。