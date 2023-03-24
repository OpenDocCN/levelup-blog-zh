# 您可能错过的 ES6 数字和数学功能

> 原文：<https://levelup.gitconnected.com/es6-number-and-math-features-you-may-have-missed-c906925acea4>

![](img/5b52bd50eb77c9e949f8c90c515b0f07.png)

迈克尔·范·克尔克霍夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

ES6 引入了各种数学和数字方法和属性。在本文中，我们将看看一些我们可能已经错过的有用的`Number`和`Math`属性。

# 新`Number`房产

`Number`对象有我们可以使用的新属性和方法。

## 号码。希腊语字母之第五字

`Number.EPSILON`属性对于比较浮点数是否在舍入误差的容差值内很有用。这是一个只读属性。

它表示 1 和大于 1 的最小浮点数之间的差值。

例如，我们可以编写以下代码来使用它:

```
const result = Math.abs(0.3 - 0.4 + 0.1);
console.log(result);
console.log(result < Number.EPSILON);
```

在上面的代码中，我们有`result`，也就是`Math.abs(0.3–0.4 + 0.1)`。`0.3 — 0.4 + 0.1`应该是 0，但是由于 JavaScript 对浮点值的处理，我们得到`2.7755575615628914e-17`作为`result`的值。

使用`Number.EPSILON`，我们可以检查`result`是否足够小，是否足够接近 0，就像我们在最后一行中所做的那样。

## Number.isInteger(数字)

`Number.isInteger`检查给定的`num`是否为整数。例如，我们可以如下使用它:

```
Number.isInteger(2.05)
Number.isInteger(2)
```

然后第一行返回`false`，第二行返回`true`，。

## Number.isSafeInteger(数字)

还有`isSafeInteger`方法来检查 JavaScript 整数是否在 JavaScript 可以接受的范围内。范围由`Number.MIN_SAFE_INTEGER`和`Number.MAX_SAFE_INTEGER`的值表示。

`Number.MIN_SAFE_INTEGER`是-9007199254740991`Number.MAX_SAFE_INTEGER`是 9007199254740991。

## 数字.伊斯南(数字)

`Number.isNaN`检查`num`是否为`NaN`。与全局函数`isNaN()`不同，这个方法不会将其参数强制为一个数字。因此，对非数字使用它是安全的。

例如， `isNaN(‘abc’)`返回`true`，而`Number.isNaN(‘abc’)`返回`false`。因此，它实际上会检查`num`是否真的是`NaN`，而不是将值强制转换为一个数字，并检查强制值是否是`NaN`。

## `Number.parseInt(num, radix)`

`Number.parseInt`执行与全局`parseInt`功能相同的功能。它接受要转换的数字字符串和基数，基数是数字要转换成的基数，作为第二个参数。基数是可选参数。

例如，我们可以编写以下代码来实现这一点:

```
Number.parseInt('11', 8)
```

上面的代码返回 9，因为我们通过将 8 作为第二个参数传入，假设该字符串是一个八进制数。

`Number.parseInt`没有对八进制或二进制数字文字的特殊支持。例如:

```
Number.parseInt('0o11', 8)
```

返回 0。

但是，它支持将十六进制文字转换为数字。例如，`Number.parseInt(‘0xF’)`返回 15。所以它知道我们传入的是一个十六进制数。

# 新的`Math`方法

`Math`对象有新的数值、三角和位运算方法。

## `Math.sign(num)`

`sign`方法返回一个数字的符号。如果我们有一个负数，它返回-1，如果数字是 0，那么它返回 0。否则，它返回 1。

例如，`Math.sign(-2)`返回-1，`Math.sign(0)`返回 0，`Math.sign(6)`返回 1。

## Math.trunc(数字)

`Math.trunc`删除数字的小数部分并返回。

例如，`Math.trunc(2.1)`返回 2。`Math.trunc(-2.1)`返回`-2`，`Math.trunc(2.9)`返回 2。

## `Math.log10(num)`

`log10`方法计算`num`以 10 为底的对数。比如，`Math.log10(1000)`就是 3。

## Math.hypot(x，y)

`hypot`给定水平边和垂直边的长度，分别为`x`和`y`，计算三角形假设的长度。

例如，`Math.hypot(4, 3)`就是 5。

![](img/0a0267dba2e9c1629045389c239cd789.png)

照片由 [Antoine Dautry](https://unsplash.com/@antoine1003?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 新整数文字

ES6 增加了对二进制和八进制文字的支持。要写一个二进制数，我们在数字前加前缀`0b`，然后写二进制数字。

例如，我们可以将二进制值 3 写成`0b11`。

八进制数字以`0o`为前缀。比如，`0o11`就是 9。

To `toString`方法带有一个`radix`参数，它可以将一个数字转换成我们想要的基数，并以字符串的形式返回。

例如，`(9).toString(8)`是 11。

# 结论

ES6 为`Number`和`Math`对象引入了许多属性和方法。此外，我们有新类型的数值。现在支持二进制和八进制数。

另外，`Number`对象现在有了`parseInt`和`isInteger`方法。我们还可以用`Number.EPLISON`属性检查某个东西是否足够接近 0，这是一个非常小的数字。

`Number`还有`isNaN`和`isSafeInteger`方法来检查`NaN`是否没有转换，还有`isSafeInteger`来检查一个整数是否在 JavaScript 可以接受的范围内。

`Math`方法包括`sign`方法获取数字的符号，`trunc`方法截断数字，`log10`方法获取基数为 10 的对数。