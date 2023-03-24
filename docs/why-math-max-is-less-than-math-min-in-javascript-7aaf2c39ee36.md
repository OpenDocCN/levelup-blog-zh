# JavaScript 中为什么 Math.max()小于 Math.min()

> 原文：<https://levelup.gitconnected.com/why-math-max-is-less-than-math-min-in-javascript-7aaf2c39ee36>

## `Math.max() < Math.min() === true`

## 惊讶吗？以下是在没有传递参数的情况下，JavaScript 的最大函数小于最小函数的原因。

![](img/3d67eb4ba2d1a870c4f7f667f4dc4b47.png)

[布雷特·乔丹](https://unsplash.com/@brett_jordan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

D id 你知道 JavaScript 中不带参数的`[Math.max()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/max)`返回值比不带参数的`[Math.min()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/min)`小吗？

这是为什么呢？让我们看看函数返回什么:

这很奇怪——`[Infinity](https://medium.com/swlh/what-is-infinity-in-javascript-%EF%B8%8F-1faf82f100bc)`实际上是 JavaScript 中最大的数字，还有值`[Number.MAX_VALUE](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/MAX_VALUE)`和`[Number.MAX_SAFE_INTEGER](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/MAX_SAFE_INTEGER)`。

没有参数的`Math.min()`返回什么？

同样，我们得到了与预期相反的结果— `[-Infinity](https://medium.com/swlh/what-is-infinity-in-javascript-%EF%B8%8F-1faf82f100bc)`是 JavaScript 中最小的数字，还有`[Number.MIN_VALUE](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/MIN_VALUE)`。

那么为什么`Math.min()`和`Math.max()`好像是反着来的呢？

答案埋藏在 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/max#Description)中:

> "`[-Infinity](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Infinity)`是初始比较值，因为几乎所有其他值都更大，这就是为什么当没有给定参数时，返回- `[Infinity](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Infinity)`。
> 
> 如果至少有一个参数不能转换成数字，那么结果就是`[NaN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/NaN)`。”— [MDN 单据](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/max#Description)

当然啦！`Math.max()`只是一个`for`参数循环的实现(查看[Chrome V8 的实际实现](https://github.com/v8/v8/blob/cd81dd6d740ff82a1abbc68615e8769bd467f91e/src/js/math.js#L77-L102))。

因此，`Math.max()`从搜索值`-Infinity`开始，因为任何其他数字都将大于-无穷大。

类似地，`Math.min()`从`Infinity`的搜索值开始:

> “如果没有给定参数，结果是`[Infinity](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Infinity)`。
> 
> 如果至少有一个参数不能转换为数字，则结果为`[NaN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/NaN)`。”—`[Math.min()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/min#Description)`的 MDN 文档

[ECMAScript 规范](https://www.ecma-international.org/ecma-262/10.0/index.html#sec-math.max)对于`Math.max()`和`Math.min()`也指出了这些函数认为`+0`大于`-0`:

该行为不同于`[>](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Relational_operators)` [大于和](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Relational_operators) `[<](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Relational_operators)` [小于运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Relational_operators)，它们认为`-0` [负零](https://medium.com/coding-at-dawn/is-negative-zero-0-a-number-in-javascript-c62739f80114)等于`+0`正零。

从技术上讲，`-0`负零等于`0`正零根据`[==](https://medium.com/better-programming/making-sense-of-vs-in-javascript-f9dbbc6352e3)`[和](https://medium.com/better-programming/making-sense-of-vs-in-javascript-f9dbbc6352e3) `[===](https://medium.com/better-programming/making-sense-of-vs-in-javascript-f9dbbc6352e3)` [等式运算符，](https://medium.com/better-programming/making-sense-of-vs-in-javascript-f9dbbc6352e3)则不根据`[Object.is()](https://medium.com/coding-at-dawn/es6-object-is-vs-in-javascript-7ce873064719)`。

因此，从某种意义上来说，`Math.max()`和`Math.min()`对于`-0`负零来说比一个天真的实现更聪明([参见 V8 代码](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Relational_operators)中的第 96–99 行)。

喜欢这篇文章？然后你会喜欢我的文章，关于如何最快地[找到 JavaScript 数组](https://medium.com/coding-at-dawn/the-fastest-way-to-find-minimum-and-maximum-values-in-an-array-in-javascript-2511115f8621)中的最小值和最大值——这里我展示了一种使用`Math.max()`和`Math.min()`的方法，它比使用`[...](https://medium.com/coding-at-dawn/how-to-use-the-spread-operator-in-javascript-b9e4a8b06fab)` [扩展操作符](https://medium.com/coding-at-dawn/how-to-use-the-spread-operator-in-javascript-b9e4a8b06fab)要快得多:

[](https://medium.com/coding-at-dawn/the-fastest-way-to-find-minimum-and-maximum-values-in-an-array-in-javascript-2511115f8621) [## 在 JavaScript 中查找数组中最小值和最大值的最快方法

### JavaScript 提供了几种在列表中查找最小和最大数字的方法，包括内置的数学…

medium.com](https://medium.com/coding-at-dawn/the-fastest-way-to-find-minimum-and-maximum-values-in-an-array-in-javascript-2511115f8621) 

现在你知道`Math.max()`和`Math.min()`的所有怪癖了吧！

编码快乐！😊💻😉🔥🙃

![](img/5ca73fb1cd53126855e04ba80153333f.png)

照片由 [DaYsO](https://unsplash.com/@dayso?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

德里克·奥斯汀博士是《职业规划:如何在 6 个月内成为 6 位数的成功程序员》一书的作者，这本书现在已经在亚马逊上出售。