# JavaScript 最佳实践——意想不到的价值等等

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-unexpected-values-and-more-c55a1c503d16>

![](img/7072c782bb19893fdef3cdfc1a53adbb.png)

照片由[米妮周](https://unsplash.com/@marslady?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何其他编程语言一样，JavaScript 有自己的最佳实践列表，使程序更容易阅读和维护。JavaScript 有许多棘手的部分，有许多事情需要避免。我们可以遵循一些最佳实践来使我们的 JavaScript 代码易于阅读。

在本文中，我们研究比较对象和函数参数的最佳方式，添加一个缺省值来切换语句和函数参数，并避免使用`eval`函数。

# 使用===比较对象

在 JavaScript 中，有两个运算符用于相等比较。一个是`==`操作员，另一个是`===`操作员。`==`通过自动将操作数转换为相同类型来进行比较。

`===`通过比较两个操作数的内容和类型进行比较。

我们想使用`===`，因为它比较类型和内容，这样我们就不会在类型上有任何不匹配。

例如，如果我们使用`==`运算符，我们可以将下面的表达式求值为`true`:

```
0 == "";
0 == null;
0 == undefined;
null == undefined;
1 == "1";
```

在前 4 行中，一切都是假的，所以在用`==`运算符比较相等性之前，它们都被转换为`false`。

在最后一行中，`'1'`在比较之前被转换为`1`，这样我们就可以得到返回值`true`。

为了避免这些混乱的结果，我们可以使用`===`操作符，它比较值的类型和内容。有了`===`，上面的一切都会被评估为`false`。

如果要用`===`运算符比较内容相同类型不同的事物，可以先转换成相同的类型。

例如，如果我们想要比较`1`和`'1'`，我们可以写:

```
+1 === +'1'
```

然后它们都将被转换为`1`，表达式将计算为`true`。

通过这种方式，我们不会比较任何意外类型。

# 默认参数

JavaScript 函数可以接受任意数量的参数，即使它们有自己的签名。这意味着我们可以传入比预期更少的参数。

对于 ES6，我们得到默认的参数值特性。这让我们可以设置函数参数的默认值。

例如，我们可以写:

```
const add = (a, b = 0) => a + b;
```

然后，我们不必为函数传递第二个参数来返回一个数值。如果我们写:

```
console.log(add(1));
```

我们得到 1，因为如果没有传递任何东西，`b`的值将被设置为 0，所以我们得到 1 + 0，也就是 1。

![](img/047f653e95b9dac53abc3f2ef97effe8.png)

照片由[克里斯蒂娜·帕帕罗](https://unsplash.com/@krispaparo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 为 Switch 语句添加默认值

我们应该总是在`switch`语句中添加一个`default`块，因为我们可能会将意想不到的值传递给`switch`语句。

例如，我们应该写:

```
const getDayName = (dayOfWeek) => {
  let day;
  switch (dayOfWeek) {
    case 0:
      day = "Sunday";
      break;
    case 1:
      day = "Monday";
      break;
    case 2:
      day = "Tuesday";
      break;
    case 3:
      day = "Wednesday";
      break;
    case 4:
      day = "Thursday";
      break;
    case 5:
      day = "Friday";
      break;
    case 6:
      day = "Saturday";
      break;
    default:
      day = "Unknown";
  }
  return day;
}
```

获取星期几，因为我们可以向`getDayName`函数传递任何东西。例如，我们可以写:

```
console.log(getDayName(0));
console.log(getDayName(1));
console.log(getDayName(1000));
```

如果我们传入 1000，我们需要处理它，所以我们需要一个默认值。

# 不要使用 Eval

`eval`函数接受包含 JavaScript 代码的字符串，该字符串将由该函数运行。

我们不应该使用它，因为它让未知来源的代码运行，这是一个很大的安全风险。

此外，它也比普通代码慢，因为它是动态解释的。这是因为代码不能被 JavaScript 引擎优化。

现代解释器把代码转换成机器码，这是传入`eval`的代码做不到的。这意味着解释器必须在运行中进行变量查找，以设置正确的变量。

如果正在运行传递给`eval`函数的代码，对变量的任何操作都将迫使浏览器重新评估代码。

# 结论

当我们比较对象是否相等时，我们应该使用`===`操作符，因为它比较对象的内容和类型。此外，我们应该为参数设置默认值，以防在调用函数时它们没有被传递到函数中。此外，我们应该在`switch`语句中添加一个`default`块来处理意外值。最后，不要使用`eval`函数，因为它是一个很大的安全性，允许人们向我们的程序注入恶意代码，并且用`eval`运行代码很慢。