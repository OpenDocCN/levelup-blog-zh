# TypeScript 与 JavaScript 的关系

> 原文：<https://levelup.gitconnected.com/typescripts-relation-to-javascript-1dc8dc6fe626>

![](img/4f62debb25434a3e83be47ccc3d033c0.png)

照片由[布鲁克·拉克](https://unsplash.com/@brookelark?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

TypeScript 是 JavaScript 的自然扩展，在许多项目中取代了 JavaScript。

然而，并不是每个人都知道它实际上是如何工作的。

在本文中，我们将看看 TypeScript 如何让 JavaScript 项目变得更好。

# 类型强制

JavaScript 在执行某些操作时会对其变量进行数据类型强制。

它产生一致的结果，我们只需要知道它是如何工作的。

如果我们使用`==`操作符来比较对象，那么类型强制将在比较操作完成之前完成。

例如，我们可能有:

```
let applePrice = 1;
let orangePrice = '1';
if (applePrice == orangePrice) {
  //...
}
```

那么在比较之前，两者都将被转换成数字。

当我们写道:

```
let totalPrice = applePrice + orangePrice;
```

那么两者在串联之前都会被转换成字符串，这很可能不是我们想要的。

# 避免无意的类型强制

为了使我们的生活更容易，我们应该采取措施避免无意的数据类型强制。

为此，我们可以使用`===`操作符进行比较，并在进行连接之前先显式转换类型。

例如，我们可以写:

```
let applePrice = 1;
let orangePrice = '1';
if (applePrice === orangePrice) {
  //...
}
```

用于比较，以及:

```
let totalPrice = Number(applePrice) + Number(orangePrice);
```

`===`不应用数据类型强制进行比较。

我们通过使用`Number`函数在将两个操作数相加之前将它们转换成数字来防止用`+`符号连接。

# 显式类型强制的好处

我们可以使用类型强制。

例如，我们可以使用`||`将`null`、`undefined`或其他 falsy 值强制转换为`false`，这样`||`运算符将返回第二个操作数。

例如，如果我们有:

```
let firstName; 
let secondName = firstName || "jane";
```

由于`firstName`是`undefined`，那么`||`运算符会强制`firstName`到`false`并返回第二个操作数，也就是`'jane'`。

因此，我们可以使用`||`操作符返回一个默认值 inc，第一个是 falsy。

# 使用函数

函数是 JavaScript 的组成部分。

我们可以定义函数来运行重复调用的代码。

例如，我们可以写:

```
const addPrices = (first, second) => {
  return first + second;
};
```

那么我们可以这样称呼它:

```
let applePrice = 1;
let orangePrice = 2;
const totalPrice = addPrices(applePrice, orangePrice);
```

当我们在上面调用函数`addPrices`时，它接收数字值。

然而，当我们将变量传递给函数时，JavaScript 不做任何验证。

因此，我们可能会得到意想不到的结果。

这就是 TypeScript 可以帮助我们的地方，当参数传入时，我们可以在调用函数之前验证数据类型。

# 函数结果

在 JavaScript 中，函数返回类型由我们返回的值决定。

它可以是任何东西，取决于我们返回什么。

例如，如果我们有:

```
let applePrice = 1;
let orangePrice = 2;
const totalPrice = addPrices(applePrice, orangePrice);
```

然后我们返回一个数字。

另一方面，如果我们有:

```
let applePrice = 1;
let orangePrice = '2';
const totalPrice = addPrices(applePrice, orangePrice);
```

然后`addPrices`返回一个字符串。

这可能是一个问题，因为我们可能不想连接而不是添加。

只有两个数相加。否则，它是串联的。

同样，我们不需要传递所有的论点。

例如，如果我们向`addPrices`添加第三个参数，如下所示:

```
const addPrices = (first, second, third) => {
  return first + second + third;
};let applePrice = 1;
let orangePrice = 2;
const totalPrice = addPrices(applePrice, orangePrice);
```

然后我们得到`NaN`，因为`third`是`undefined`，将数字和`undefined`相加得到`NaN`。

![](img/7198f11f54504ddc229867d92d975d7a.png)

泰勒·基瑟在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 避免参数不匹配问题

在 JavaScript 中，我们可以为一个我们知道是可选的参数设置默认值。

例如，我们可以写:

```
const addPrices = (first, second, third = 0) => {
  return first + second + third;
};
```

或者，我们可以使用 rest 参数符号将一些或所有参数放入一个数组中。

如果`third`没有传入值，那么`third`被设置为 0。

我们可以写:

```
const addPrices = (...rest) => {
  return rest.reduce((a, b) => a + b, 0);
};
```

那么`rest`就是一个数组，因为 spread 操作符将所有参数放入一个数组中。

# 结论

TypeScript 只是使用了 JavaScript 的类型系统。

它通过限制函数的参数类型和返回类型来驯服 JavaScript 类型系统的动态特性。