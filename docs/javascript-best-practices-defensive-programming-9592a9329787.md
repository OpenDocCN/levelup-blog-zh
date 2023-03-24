# JavaScript 最佳实践—防御性编程

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-defensive-programming-9592a9329787>

![](img/8e3c60b4e40c00e54c1c0269efa3c860.png)

[Sheri Hooley](https://unsplash.com/@sherihoo?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是一种非常宽容的语言。编写即使有问题也能运行的代码很容易。

在本文中，我们将看看如何进行防御性编程，以便优雅地处理错误。

# 保护我们的程序免受无效输入的影响

我们必须检查程序中的无效输入，这样它们就不会在我们的系统中传播。

例如，我们需要检查像`null`、`undefined`这样的东西，或者无效的值。

此外，我们必须意识到常见的安全问题，如 SQL 注入或跨站脚本。

为此，我们在程序中做了以下事情。

# 检查来自外部来源的所有数据的值

我们不能相信用户总是输入有效的数据，也不能相信攻击者不会在我们的代码中利用漏洞。

因此，我们应该彻底检查一切，这样我们就可以确保没有无效数据传播到任何地方。

例如，如果代码需要某个范围内的数字，我们应该检查数字范围。

此外，我们不应该使用像`eval`函数或`Function`构造函数这样的东西，它们从字符串中运行代码。

# 检查所有功能输入参数的值

在函数中使用这些值之前，我们需要检查它们。

例如，我们可以编写一个仅向成年人分发啤酒的函数，如下所示:

```
const dispenseBeer = (age) => {
  if (age < 21) {
    return 'you are too young';
  }
  //...
}
```

我们在分发啤酒之前检查`age`，这样我们就不会错误地将啤酒分发给未成年人。

# 决定如何处理不良输入

坏的输入必须以优雅的方式处理。

我们必须检查它们，然后找到一个好的方法来处理它们。

返回消息或抛出异常是处理无效输入的好方法。

例如，我们的`dispenseBeer`函数可以改为显示一个警告框:

```
const dispenseBeer = (age) => {
  if (age < 21) {
    alert('you are too young');
    return;
  }
  //...
}
```

或者我们可以这样写:

```
const dispenseBeer = (age) => {
  if (age < 21) {
    throw new Error('you are too young');
  }
  //...
}
```

我们不需要返回，因为抛出异常会结束函数的执行。

# 断言

此外，在运行函数之前，我们可以使用断言来检查输入。

例如，我们可以使用`assert`模块编写如下代码:

```
const assert = require('assert');const dispenseBeer = (age) => {
  try {
    assert.equal(age >= 21, true);
    //...
  }
  catch {
    return 'you are too young';
  }
}
```

这与抛出一个错误是一样的，因为它检查`age`是否大于 21。

否则，将抛出断言错误并返回`‘you are too young’`。

这与抛出异常是一样的。

我们可以用`assert`模块做出许多其他种类的断言。

它们包括:

*   检查相等性
*   检查不平等
*   检查抛出的异常
*   检查深度相等
*   检查深度不平等
*   …以及其他

![](img/15d9e46cf696cdf12a528267231c721a.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上 [engin akyurt](https://unsplash.com/@enginakyurt?utm_source=medium&utm_medium=referral) 拍摄的照片

# 使用断言的准则

使用断言有一些指导原则。

## 对预期会发生情况使用错误处理代码

错误处理代码应该用于检查可能无效的条件。

在这种情况下，我们应该处理那些情况，而不是使用断言。

像错误的输入值这样的事情应该用错误处理代码来检查。

## 对不应该发生的情况使用断言

如果某些事情永远不会发生，那么我们应该使用断言来阻止代码运行。

我们可以运行剩下的代码，假设我们断言永远不会发生的情况不会发生。

## 避免将可执行代码放入断言中

我们应该只使用断言来检查事物。

这样可以保持干净，防止混淆。

## 使用断言来记录和验证前置条件和后置条件

应该验证的前提条件应该有一个断言。

既然它；s，那么当我们在运行代码的其余部分之前读取代码时，我们就知道代码检查的是什么。

当程序结束时，我们可以用断言来检查后置条件，以检查我们寻找的条件。

# 结论

我们应该在运行任何东西之前检查无效的条件。

为此，我们可以使用断言或错误处理代码。断言更适合于检查不应该发生的情况，而错误处理代码更适合于其他情况。