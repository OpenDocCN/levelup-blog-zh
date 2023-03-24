# 我们可能忽略的 ES6 静态数字和数学属性

> 原文：<https://levelup.gitconnected.com/es6-static-number-and-math-properties-that-we-may-have-missed-9bf04e4e3067>

![](img/00104fe12ba8c02c513e190031d895a4.png)

由 [Roksolana Zasiadko](https://unsplash.com/@cieloadentro?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

ES6 引入了各种数学和数字方法和属性。在这篇文章中，我们将看看一些有用的静态`Number`属性，我们可能已经错过了。

# 数字是有限的

`Number.isFinite(num)`是一种让我们检验一个数是否有限的新方法。例如:

```
Number.isFinite(Infinity)
```

返回`false`，但是:

```
Number.isFinite(1)
```

返回`true`。

`Number.isFinite(NaN)`也返回`false`。

# `Number.isNaN(num)`

`Number.isNaN`检查`num`是否为值`NaN`。这很有用，因为在 JavaScript 中`NaN`不等于它自己。

全局`isNaN`和`Number.isNaN`的区别在于`isNaN`在做检查之前会强制`num`变成一个数字，而`Number.isNaN`不会这样做。

例如:

```
Number.isNaN('NaN')
```

返回`false`，但是:

```
Number.isNaN(NaN);
```

返回`true`。反之，`isNaN('NaN')`返回`true`。

# 数学静态方法

从 ES6 开始，有新的静态方法包含在`Math`中。以下是新方法。

## Math.cbrt(x)

`Math.cbrt(x)`返回`x`的立方根。例如，`Math.cbrt(8)`返回 2。

## 对指数运算和对数运算使用 0 而不是 1

我们可以使用`Math.expm1(x)`方法返回`x`减 1 的自然对数。

所以和`Math.exp(x) — 1`一样。例如:

```
Math.expm1(0)
```

返回 0，而`Math.exp(0) — 1`也返回 0。

`Math.expm1(x)`方法是`Math.log1p()`的逆方法。

`Math.log1p(x)`返回`Math.log(1 + x)`。例如，`Math.log(1 + 1e-19)`和`Math.log(1 + 0)`都返回 0。

## 以 2 和 10 为基数的对数

我们可以在 JavaScript 中以 2 和 10 为基数计算 log，而无需自己更改 log。

现在`Math`对象有了`Math.log2(x)`和`Math.log10(x)`方法来分别计算以`x`为基数 2 的 log 和以`x`为基数 10 的 log。

例如，`Math.log2(16)`返回 4，`Math.log10(1000)`返回 3。

## 双曲线方法

新的双曲线静态方法被添加到`Math`对象中。它们是:

*   `Math.sinh(x)` —计算`x`的双曲正弦值
*   `Math.cosh(x)` —计算`x`的双曲余弦值
*   `Math.tanh(x)` —计算`x`的双曲正切值
*   `Math.asinh(x)` —计算`x`的反双曲正弦值
*   `Math.acosh(x)` —计算`x`的反双曲余弦值
*   `Math.atanh(x)` —计算`x`的双曲正切值

## math . hypot(…值)

`Math.hypot(...values)`返回其参数平方和的平方根。例如，我们可以写:

```
Math.hypot(1,2,3,4,5)
```

返回 7.416198487095663。

![](img/4e89c768153dd5be1b579b6e0745e613.png)

照片由[舒伊布·阿卜尔哈萨尼](https://unsplash.com/@shoeibabhn?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

ES6 增加了在不强制数字的情况下检查有限数字和`NaN`实际值的方法。

我们还可以用常数`Number.EPSILON`检查是否有接近 0 的东西。

Math methods 包括`cbrt`方法，它计算一个数的立方根。`hypot`计算一系列数字的平方和的平方根。

同样，我们可以用内置的`Math`方法得到基数为 2 或 10 的对数。`log2`返回基数为 2 的对数，`log10`返回基数为 10 的对数。

`expm(x)`返回`1 + x`的自然日志。双曲线函数也被添加为`Math`对象的方法。