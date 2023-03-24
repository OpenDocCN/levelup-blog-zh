# JavaScript 调试:操作顺序

> 原文：<https://levelup.gitconnected.com/javascript-debugging-order-of-operations-2113b8332d62>

![](img/e76ec65edd53ef384f2efabfeb70a192.png)

今天的文章不同于我以前写过的许多文章。我们不是真的要解决一个算法，而是要解决一个问题。我们将调试一小段代码。

调试是定位和消除计算机程序缺陷、错误或异常的常规过程。调试是编程的一个正常部分，你几乎会一直遇到它。调试你写的代码更容易，但不会总是这样。

今天我们将学习如何调试预先编写的代码。我们得到了一个名为`orderOperations`的函数，但是有一个问题。该函数应该返回`32`，但它一直返回`10`。我们将在不改变数量或运算符的情况下修复函数。

```
function orderOperations () {
  return 2 + 2 * 2 + 2 * 2
}
```

当 JavaScript 做数学运算时，它根据[运算符优先级](https://www.sitepoint.com/javascript-operators-conditionals-functions/#operatorprecedence)来解决问题。对于上面的问题，JavaScript 会在做加法之前先解决乘法。简而言之，这就是 JavaScript 正在做的事情:

```
2 + **2 * 2** + **2 * 2** 2 + 4 + 4
= 10
```

一旦所有操作符都相同，JavaScript 就会从左到右解决问题。从技术上来说，如果这是我们想要和期望的，那么问题就是正确的，但这不是我们想要 JavaScript 如何计算的。

有了对运算符优先级的理解，我们可以在不改变数字和运算符的情况下修复函数。我们可以用括号和把数字分组。分组的优先级高于乘法和加法，因此我们可以控制 JavaScript 解决数学问题的顺序。

我们可以将我们的数学分组来创建:

`(2 + 2) * (2 + 2) * 2`导致`4 * (4) * 2`导致`16 * 2`等于`32`。

```
function orderOperations () {
  return (2 + 2) * (2 + 2) * 2
}
```

对于这个调试，问题不是返回一个错误消息。JavaScript 在做它的工作，但是它以一种我们没有预料到的方式在做。不返回错误信息但同时也不返回您想要的信息的程序调试起来会很痛苦。

这个调试问题很简短，但可能对您有所帮助。

如果您觉得这篇调试文章很有帮助，并且您想了解一些 JavaScript 算法及其解决方案，请查看我最近的其他文章:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-alternating-characters-617eb0764cbc) [## JavaScript 算法:交替字符

### 对于今天的算法，我们将编写一个名为 alternatingCharacters 的函数，它将一个字符串 s 作为…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-alternating-characters-617eb0764cbc) [](/javascript-algorithm-the-hurdle-race-93447c054b38) [## JavaScript 算法:跨栏赛跑

### 对于今天的算法，我们将编写一个名为跨栏赛跑的函数，它将接受一个整数 k 和一个数组高度。

levelup.gitconnected.com](/javascript-algorithm-the-hurdle-race-93447c054b38) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-migratory-birds-848ad6a99ac3) [## JavaScript 算法:候鸟

### 对于今天的算法，我们将编写一个名为 migratoryBirds 的函数，在这个函数中，我们将接受一个…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-migratory-birds-848ad6a99ac3)