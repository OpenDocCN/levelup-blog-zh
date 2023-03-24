# JavaScript 最佳实践—函数参数、错误和循环

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-functions-parameters-errors-and-loops-aaf75677255e>

![](img/d53f0647c2d700239b24079fbafe7bee.png)

塞巴斯蒂安·佩纳·兰巴里在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是一种简单易学的编程语言。编写运行并执行某些操作的程序很容易。然而，很难考虑所有的用例并编写健壮的 JavaScript 代码。

在本文中，我们将探讨定义和使用函数的最佳方式。

我们还会看到抛出异常和循环。

# 参数和返回类型

我们必须小心如何在函数中添加参数和返回类型。

# 默认参数

自 ES6 以来，默认参数一直是一个特性。

我们可以用它们来定义带有默认值的参数。

这样，我们可以跳过它们或将它们设置为`undefined`，我们的参数仍将被设置为一个值。

例如，我们可以用默认值定义一个参数，写为:

```
function foo(a, b = 2) {
  //...
}
```

`b`是默认参数。其默认值为 2。

这样，当我们这样称呼它时:

```
foo(1);
```

`b`将设为 2。

# 传播算子

应该使用 spread 运算符，而不是使用`apply`来调用使用数组条目的多参数函数。

它既适用于传统函数，也适用于箭头函数，不像`apply`方法只适用于传统函数。

例如，我们可以写:

```
fn(...foo, ...bar, ...baz);
```

假设`foo`、`bar`和`baz`是数组，我们可以用它们的所有条目作为参数来调用它们。

此外，我们可以用它来检索没有作为数组赋给参数的变量。

例如，如果我们有:

```
function fn(...elements) {}
```

如果我们通过运行调用它:

```
fn(1, 2, 3);
```

那么`elements`就是`[1, 2, 3]`。

# 字符串文字

使用 JavaScript 字符串时很容易陷入陷阱。因此，我们应该遵循一些最佳实践。

# 使用单引号

单引号更容易键入，所以我们可以考虑使用它们，这样我们就不必按 shift 键来添加双引号。

普通字符串不应该跨越多行。

# 模板文字

如果我们考虑连接字符串，那么我们应该使用模板文字。

它也非常适合创建多行字符串。

例如，我们可以写:

```
function add(a, b) {
  return `${a} + ${b} = ${a + b}`;
}
```

然后我们在字符串表达式中插入`a`和`b`。

既然我们已经有了模板字符串，我们就不应该再连接字符串了。

# 没有行延续

`\`字符在形成多行字符串时有问题。

如果有任何尾随空白，那么我们将得到错误。

因此，我们应该使用模板字符串来制作长字符串。

例如，我们可以写:

```
const veryLongStr = 'very long string';
const longerStr = 'very very long string';
const longestStr = 'very very very long string';
const str = `${veryLongStr} ${longerStr} ${longestStr}`;
```

现在，我们将字符串变量嵌入模板字符串中，将它们放在一起。

# 数字文字

数字可以用十进制、十六进制、八进制或二进制来指定。

除非后面有`x`、`o`或`b`，否则我们不应该包含前导 0，这样它们就不会与基数混淆。

# 控制结构

在创建控制结构时，有许多事情需要考虑。

我们应该意识到它们。

# 对于循环

`for`循环有几种。一种是常规 for 循环。另一个是 for-in 和 for-of 循环。

for-in 不应用于迭代数组。它们应该用于迭代对象的键。

我们应该使用`Object.prototype.hasOwnProperty`来检查该财产是自有财产还是继承财产。

这样，我们可以在使用它们之前检查一个键是否被继承。

`for-of`是一个可以遍历任何可迭代对象的循环，包括数组。

我们可以使用`Object.keys`方法获得一个对象自己的字符串键，并用 for-of 循环遍历它们。

这比 for-in 要好，因为`Object.keys`返回一个数组并遍历它们。

![](img/11d0235ba0b53237187d2b682f53ef4c.png)

[百合班克斯](https://unsplash.com/@lvnatikk?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 例外

异常是 JavaScript 的重要组成部分。

每当出现异常情况时，我们都应该使用它们。

要正确使用，我们应该抛出`Error`或者`Error`的子类。

其他对象不应该被抛出，否则我们将丢弃有用的信息，如错误发生的行号和堆栈跟踪。

抛出异常比其他特定的错误处理方法要好。

# 结论

我们应该抛出异常以使错误更加明显。

当我们使用`throw`时，我们应该抛出一个`Error`对象或者它的子类。

JavaScript 有默认参数。当参数没有传入时，我们可以用它们来设置参数的值。

`for-of` loop 功能多，简单，用起来比较好。