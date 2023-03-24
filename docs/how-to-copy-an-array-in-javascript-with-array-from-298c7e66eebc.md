# 如何用 Array.from()在 JavaScript 中复制数组

> 原文：<https://levelup.gitconnected.com/how-to-copy-an-array-in-javascript-with-array-from-298c7e66eebc>

## 使用`Array.from()`在 JavaScript 中复制数组很容易，但这是一种浅层复制——嵌套数组和对象容易出现错误。

![](img/df202d3fd098683bd1f47b23bbb38c28.png)

新浪 Katirachi 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 复制 JavaScript 数组的最简单方法

> `Array.from()`方法从一个类似数组或可迭代的对象创建一个新的浅拷贝的`Array`实例—`[Array.from()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from)`上的 MDN 文档

JavaScript 内置的帮助器方法`[Array.from()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from)`将对一个 JavaScript 数组做一个*浅拷贝*，这意味着对原始数组值的改变不会影响被拷贝数组的值。

`Array.from()`是现代浏览器[支持的 ES6 特性，但 Internet Explorer](http://kangax.github.io/compat-table/es6/#test-Array_static_methods) 不支持。旧的浏览器支持将需要一个[多填充](https://medium.com/better-programming/compiling-vs-polyfilling-in-javascript-6bbc5707a253)。

查看以下代码示例:

如上所述，使用`Array.from()`相当于使用[JavaScript 扩展操作符](https://medium.com/coding-at-dawn/how-to-use-the-spread-operator-in-javascript-b9e4a8b06fab) `[…](https://medium.com/coding-at-dawn/how-to-use-the-spread-operator-in-javascript-b9e4a8b06fab)`和方括号`[]`来创建数组的浅层副本。

![](img/abb4b328671597e0a1e965671c7d50ff.png)

[freestocks](https://unsplash.com/@freestocks?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# 等等，`Array.from()`不是和`.map()`一样吗？

熟悉 [JavaScript 函数式编程](https://medium.com/javascript-in-plain-english/what-are-javascript-programming-paradigms-3ef0f576dfdb)的精明程序员可能会注意到`Array.from()`的行为类似于`[.map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)`。

这是因为没有映射函数的`Array.from()`默认为映射函数`x => x`，其中每个输入值按原样返回。

> `“Array.from(obj, mapFn, thisArg)`的结果与`Array.from(obj).map(mapFn, thisArg)`相同，只是它不创建中间数组。”—上的 MDN 文档`[Array.from()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from)`

让我们看一个代码示例:

实际上，当我需要将每个值映射到一个复制数组中的一个新值时，我使用`.map()`，而将`Array.from()`映射到浅层复制数组。

不过，这只是个人偏好，您可以使用`.map()`或`Array.from()`来复制数组或使用任何任意的映射函数。

![](img/57a954cdd099d893999852c5253f1ca8.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[杰米街](https://unsplash.com/@jamie452?utm_source=medium&utm_medium=referral)拍摄

# 嵌套很深的“抓住你了！”

如果数组只包含原始值，那么 hallow copy 会工作得很好，因为数组中的每个值实际上都被复制了。

这意味着对新数组的更改不会影响原始数组。

但是，如果数组包含嵌套对象，包括其他数组，那么只有对这些数组的引用才会被复制。

这意味着深度嵌套的数组需要一个[深度拷贝](https://medium.com/javascript-in-plain-english/how-to-deep-copy-objects-and-arrays-in-javascript-7c911359b089)——`Array.from()`不会这样做，除非你编写一个定制的递归函数。

![](img/1a47bf14df271813869e86b88a0a339e.png)

[FOODISM360](https://unsplash.com/@foodism360?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 但是如果我需要深层拷贝呢？

我以前写过关于[如何深度复制 Javascript 对象和数组](https://medium.com/javascript-in-plain-english/how-to-deep-copy-objects-and-arrays-in-javascript-7c911359b089)，比较了浅复制和深复制方法:

[](https://medium.com/javascript-in-plain-english/how-to-deep-copy-objects-and-arrays-in-javascript-7c911359b089) [## 如何在 JavaScript 中深度复制对象和数组

### 复制对象或数组的常用方法只能进行浅层复制，所以深度嵌套的引用是个问题…

medium.com](https://medium.com/javascript-in-plain-english/how-to-deep-copy-objects-and-arrays-in-javascript-7c911359b089) 

在那篇文章中，我深入介绍了深度复制的 5 种方法，它们适用于任何嵌套层次的嵌套 JavaScript 对象(包括数组)。

![](img/e66e17c2e153d3413e4021c6e8ff316c.png)

JOSHUA COLEMAN 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 关于[JSON . parse/JSON . stringify](https://medium.com/javascript-in-plain-english/how-to-use-stringify-and-parse-in-javascript-6b637b571a32)

深度克隆的一个简单方法是`[JSON.parse(JSON.stringify())](https://medium.com/@pmzubar/why-json-parse-json-stringify-is-a-bad-practice-to-clone-an-object-in-javascript-b28ac5e36521)`，但这只适用于某些原语:[数字](https://medium.com/javascript-in-plain-english/how-to-check-for-a-number-in-javascript-8d9024708153)、[字符串](https://medium.com/javascript-in-plain-english/how-to-check-for-a-string-in-javascript-a16b196915ff)、布尔和[空值](https://medium.com/javascript-in-plain-english/how-to-check-for-null-in-javascript-dffab64d8ed5)——不包括函数、`undefined`、`[NaN](https://medium.com/coding-in-simple-english/how-to-check-for-nan-in-javascript-4294e555b447?source=post_stats_page---------------------------)`或`[Infinity](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Infinity)`。

[parse / stringify](https://medium.com/javascript-in-plain-english/how-to-use-stringify-and-parse-in-javascript-6b637b571a32) 方法对其他对象也不起作用，这些对象将被字符串化(如 [Date 对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date))或直接丢失(如 [RegExp 对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp))。

更好的深度复制函数是自定义函数，或者使用库`[rfdc](https://github.com/davidmarkclements/rfdc)` [(真正快速的深度克隆)](https://github.com/davidmarkclements/rfdc)，这是深度克隆非常大的 JavaScript 数组(10MB 或更大，即> 100，000 个元素)的最快方法。

![](img/33d58382d9dfd3357333f73b89b989ef.png)

[Toa Heftiba](https://unsplash.com/@heftiba?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

到重述:`Array.from()`是 JavaScript 中最简单的浅拷贝方式:`const copiedArray = Array.from(oldArray)`。

与使用扩展操作符`…`相比，使用`Array.from()`在代码中更容易阅读——因为`Array.from()`清楚地声明我们正在从旧数组创建一个新数组(浅层拷贝)*。*

当然，使用其他浅层复制方法也没有错，比如`[...oldArray]`或`const copy = oldArray.map(x=>x)`。

但是要想快速、简单、可靠地在 JavaScript 中浅层复制数组，只需使用`Array.from()`就可以了！干杯！🍾🥂🥳

![](img/5002a94b35366407b9511c757fbca889.png)

照片由[王思然·哈德森](https://unsplash.com/@hudsoncrafted?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 进一步阅读

*   [Amanda Treutler](https://medium.com/u/62a7f3c62753?source=post_page-----298c7e66eebc--------------------------------) 涵盖了`Array.from()`T21【在 LevelUp 编码中的多种用途:

[](/using-javascripts-array-from-method-ca9e9fccfd98) [## 使用 JavaScript 的 Array.from()方法

### JavaScript 有许多内置的数组方法，可以以不同的方式使用或组合来对…执行操作

levelup.gitconnected.com](/using-javascripts-array-from-method-ca9e9fccfd98) 

*   [Wojciech Trawiński](https://medium.com/u/9569ff590dd9?source=post_page-----298c7e66eebc--------------------------------) 在他的媒体博客上详细解释了`Array.from()`:

[](https://medium.com/javascript-everyday/javascript-array-from-53287c195487) [## JavaScript: Array.from

### 静态方法 Array.from 作为 ES6 的一部分引入。它支持从类似数组的对象或可迭代对象创建新的浅复制数组。

medium.com](https://medium.com/javascript-everyday/javascript-array-from-53287c195487) 

*   [Dmitri Pavlutin](https://medium.com/u/b585df90d09e?source=post_page-----298c7e66eebc--------------------------------) 在他的博客上讨论了如何最好地利用`Array.from()` [:](https://dmitripavlutin.com/javascript-array-from-applications/)

[](https://dmitripavlutin.com/javascript-array-from-applications/) [## JavaScript Array.from()的 5 个便捷应用

### 任何编程语言都有超出基本用途的功能。这要归功于成功的设计和…

dmitripavlutin.com](https://dmitripavlutin.com/javascript-array-from-applications/) 

*   [Mayank Gupta](https://medium.com/u/3d82404ecf35?source=post_page-----298c7e66eebc--------------------------------) 深入研究复制对象[在更好的编程中](https://medium.com/better-programming/deep-and-shallow-copy-in-javascript-110f395330c5):

[](https://medium.com/better-programming/deep-and-shallow-copy-in-javascript-110f395330c5) [## JavaScript 中的深层和浅层复制

### 你想知道的关于深度和浅度复制的一切

medium.com](https://medium.com/better-programming/deep-and-shallow-copy-in-javascript-110f395330c5) 

*   [阿伦·拉吉万](https://medium.com/u/c8f3ca0244e5?source=post_page-----298c7e66eebc--------------------------------)在他的媒体博客上比较了浅拷贝和深拷贝[:](https://medium.com/@arunrajeevan/shallow-copy-vs-deep-copy-in-javascript-5ce718725a0)

 [## Javascript 中的浅层复制与深层复制

### 浅层复制对象

medium.com](https://medium.com/@arunrajeevan/shallow-copy-vs-deep-copy-in-javascript-5ce718725a0) ![](img/4dfbae6e00a2bea756e088a29252a530.png)

照片由[埃德加·卡斯特雷洪](https://unsplash.com/@edgarraw?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

[德里克·奥斯汀博士](https://www.linkedin.com/in/derek-austin/)是《职业规划:如何在 6 个月内成为一名成功的 6 位数程序员 一书的作者，该书现已在亚马逊上架。