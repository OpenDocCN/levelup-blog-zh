# JavaScript 错误值简介— NaN 和 Infinity

> 原文：<https://levelup.gitconnected.com/introduction-to-javascript-error-values-nan-and-infinity-1df6d5e7ea91>

![](img/cd06dc58b84f579d375ad64349b8c8fc.png)

照片由 [Erik-Jan Leusink](https://unsplash.com/@ejleusink?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 有几个错误值是由一些无效的计算产生的。

在本文中，我们将研究它们及其属性。

# 圆盘烤饼

`NaN`表示不是数字。尽管有这个名字，JavaScript 仍然认为它是一个数字。

如果我们写:

```
typeof NaN
```

If 返回`'number'`。

每当我们试图用`Number`函数将没有数字内容的东西转换成数字时，它就会返回。

因此，如果我们写:

```
Number('foo')
```

我们得到`NaN`返回。

另外，`NaN`是无效数学运算的返回结果。

例如，如果我们有:

```
Math.log(-2)
```

然后我们得到返回的`NaN`。

如果我们使用负数的`sqrt`,如下所示:

```
Math.sqrt(-1)
```

我们也得到了`NaN`的回报。

任何涉及到`NaN`的计算也会返回`NaN`。下列表达式:

```
NaN - 3
7 ** NaN
```

都会双双返回`NaN`。

注意`NaN`不等于自身。例如，两者:

```
NaN == NaN
```

和

```
NaN === NaN
```

返回`false`。

## 伊斯南

为了测试`NaN`，我们可以使用`Number.isNaN()`或`isNaN()`功能。

例如，我们可以如下使用它:

```
isNaN(Math.sqrt(-1))
```

然后我们得到`true`。

如果我们写:

```
isNaN(2)
```

然后我们得到返回的`false`。

## 在数组中查找`NaN`

有些数组方法找不到`NaN`。例如，`indexOf`没有抓住`NaN`。

如果我们写:

```
[1, NaN].indexOf(NaN)
```

我们得到返回的`-1`。这意味着它找不到`NaN`。

但是其他像`includes`、`findIndex`、`find`的方法都可以找到`NaN`。

所以`[1, NaN].includes(NaN)`返回`true`。

`[1, NaN].findIndex(x => Number.isNaN(x))`返回 1。

`[1, NaN].find(x => Number.isNaN(x))`返回`NaN`。

![](img/663e1d841e7bcb782cb2c523a8fe4e35.png)

照片由[内森·赖利](https://unsplash.com/@nrly?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# `Infinity`

`Infinity`数量过大时返回。

例如，如果`Math.pow`返回的值大于一个 JavaScript 数字的最大值，那么我们将返回`Infinity`。

因此:

```
Math.pow(2, 2024)
```

返回`Infinity`,因为它大于最大 JavaScript 数。

`Math.log(0)`返回`-Infinity`。

`Infinity`的值是`Number.POSITIVE_INFINITY`。

它的表现与数学上的无穷大略有不同:

*   `POSITIVE_INFINITY` 乘以任意正值就是`POSITIVE_INFINITY`
*   任何负值乘以`POSITIVE_INFINITY`就是`NEGATIVE_INFINITY`
*   `POSITIVE_INFINITY`乘以 0 就是`NaN`
*   `NaN`乘以`POSITIVE_INFINITY`就是`NaN`。
*   `POSITIVE_INFINITY`除以除`NEGATIVE_INFINITY`以外的任何负值，即为`NEGATIVE_INFINITY`
*   `POSITIVE_INFINITY`除以除`POSITIVE_INFINITY`以外的任意正值，为`POSITIVE_INFINITY`
*   `POSITIVE_INFINITY`除以`NEGATIVE_INFINITY`或`POSITIVE_INFINITY`，得到`NaN`
*   当我们将一个数除以 0 时，我们也得到了`Infinity`
*   将一个数除以`Infinity`将得到 0
*   在`Infinity`上加任何东西都会得到`Infinity`。从`Infinity`中减去一个数将得到`Infinity`

如果我们希望它比我们能想象的任何数字都大，我们也可以使用`Infinity`作为默认值。

例如，我们可以使用它来获取数组中的最小数，如下所示:

```
const min = (numbers) => {
  let min = Infinity;
  for (const n of numbers) {
    if (n < min) {
      min = n;
    }
  }
  return min;
}
```

我们将`min`的初始值设为`Infinity`，然后如果我们发现`n`小于`min`，我们将`n`设为`min`。

然后我们返回`min`。

## 检查`Infinity`

我们可以通过使用`Number.isFinite`函数或等式运算符来检查`Infinity`。

例如，我们可以写:

```
Math.pow(2, 2024) === Infinity
```

哪个应该返回`true`或者:

```
Number.isFinite(Math.pow(2, 2024))
```

这应该会返回`false`。

# 结论

`NaN`是一个错误值，表示不是数字。然而，JavaScript 认为`NaN`的类型是数字。

`Infinity`是一个大到无法用 JavaScript 数字表示的值。它的行为很像数学上的无穷大。