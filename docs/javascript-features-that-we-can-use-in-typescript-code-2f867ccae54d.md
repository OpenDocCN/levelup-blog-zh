# 我们可以在类型脚本代码中使用的 JavaScript 特性

> 原文：<https://levelup.gitconnected.com/javascript-features-that-we-can-use-in-typescript-code-2f867ccae54d>

![](img/4f994764567178a4eb78ac81151f14db.png)

由 [Max Duzij](https://unsplash.com/@max_duz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

TypeScript 是 JavaScript 的自然扩展，在许多项目中取代了 JavaScript。

然而，并不是每个人都知道它实际上是如何工作的。

在本文中，我们将研究可以在 JavaScript 代码中采用的 JavaScript 特性。

# 使用箭头功能

箭头函数是一种简洁定义函数的方法。

它们通常用于定义作为其他函数参数的函数。

在 JavaScript 中，函数是常规对象，因此它们可以作为参数传递给另一个函数。

例如，我们下面有一个例子:

```
const addPrices = (...rest) => {
  return rest.reduce((total, b) => total + b, 0);
};
```

在上面的代码中，外层函数是一个函数。

我们还向`reduce`方法传递了一个函数。

箭头函数有三个部分。

我们有输入参数、等号、大于号和结果值。

只有当箭头函数需要运行多条语句时，才需要花括号。

任何使用函数的地方都可以使用箭头函数。

# 使用数组

JavaScript 数组类似于其他编程语言中的数组。

但是，它们可以动态调整大小，并且可以包含值的任意组合。

这意味着它们可以是任何类型的组合。

数组的大小在创建时没有指定，它会随着项目的添加或删除而自动分配。

JavaScript 数组从零开始，用方括号定义。

它可以包含由逗号分隔的初始内容。

例如，我们可以写:

```
const names = ['joe', 'jane', 'alex'];
```

然后我们创建一个包含所有字符串的数组。

此外，在使用一些数组方法中，可以使用方括号读取或设置数组中的 we 元素。

它有一个`concat`方法，该方法将一个或多个数组作为参数，以将其与另一个数组组合。

`join`获取一个`separator`字符串，该字符串将条目组合成一个字符串并返回它，每个值由`separator`分隔。

移除并返回数组的最后一项。

移除并返回数组中的第一个元素。

`push`接受一个`item`作为参数，并将其添加到数组的末尾。

`unshiift`获取一个`item`并将其添加到数组的开头。

`reverse`返回一个新数组，其中原始数组的元素被反转。

`slice(start, end)`返回一个数组的秒数。

`sort`使用自定义排序的比较函数对数组进行排序。

`splice(index, count)`从`index`直到`count`移除一个物品。

当用数组的所有值调用`test`函数时，如果该函数返回`true`，则`every(test)`返回`true`。

当用数组中的一些条目调用`test`函数时，如果该函数返回`true`，则`some(test)`返回`true`。

`filter(test)`返回一个数组，其中包含用`test`调用时返回`true`的项目。

`find(test)`返回用`test`调用时返回`true`的东西的第一个实例。

`findIndex(test)`返回用`test`调用时返回`true`的第一个实例的索引。

`forEach(callback)`循环遍历并对每个项目调用`callback`。

如果`value`在数组内部，则`includes(value)`返回`true`。

`map(callback)`返回一个新数组，该数组包含对数组中所有项目调用`callback`的结果。

`reduce(callback)`返回数组中每一项运行`callback`产生的累加值。

![](img/7dbe41df2dc08ae0a3b5b142b04fad63.png)

照片由[雷切尔·帕克](https://unsplash.com/@therachelstory?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 传播算子

扩展运算符可以以多种方式用于数组。

我们可以使用它们将数组条目作为参数传播到函数调用中。

例如，我们可以写:

```
const addPrices = (...rest) => {
  return rest.reduce((a, b) => a + b, 0);
};const prices = [1, 2];
const totalPrice = addPrices(...prices);
```

然后我们将`prices`数组作为参数传播到`addPrices`函数中。

# 结论

箭头函数在我们的类型脚本代码中很有用。

数组方法对于查找事物和操作项目也非常有用。

还有一些就地操作方法的方法，比如添加和删除项目。

spread 运算符将数组中的项扩展到函数的参数中。