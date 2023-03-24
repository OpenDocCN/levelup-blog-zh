# 可以用普通 JavaScript 轻松实现的 Lodash 方法

> 原文：<https://levelup.gitconnected.com/lodash-methods-that-can-be-easily-implemented-in-plain-javascript-bbe22509827e>

![](img/2a5dadfddab9331d7e4ce8167ddbca05.png)

照片由 [Bernal Fallas](https://unsplash.com/@ingbfallas?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Lodash 是一个实用程序库，有很多操作对象的方法。它有我们一直在使用的东西，也有我们不常使用或不想使用的东西。

我们可以用普通的 JavaScript 实现许多 Lodash 方法。在本文中，我们将研究一些可以用普通 JavaScript 实现的数组方法。

# `chunk`

在 Lodash 中，`chunk`方法根据大小将数组分成不同的组。我们可以用普通的 JavaScript 实现如下:

```
const chunk = (arr, size) => {
  const chunkedArr = [];
  for (let i = 0; i < arr.length; i += size) {
    chunkedArr.push(arr.slice(i, i + size));
  }
  return chunkedArr;
}
```

在上面的代码中，我们循环通过`arr`，获取`size`给定大小的块，并将从`slice`返回的每个数组推入`chunkedArr`。

因此，如果我们这样称呼它:

```
chunk([1, 2, 3, 4, 5, 6], 2)
```

我们得到:

```
[
  [
    1,
    2
  ],
  [
    3,
    4
  ],
  [
    5,
    6
  ]
]
```

# `compact`

`compact`方法返回一个移除了 falsy 值的数组。JavaScript 中的 Falsy 值包括`false`、0、`null`、`undefined`和`NaN`。

例如，我们可以编写以下代码来实现`compact`:

```
const compact = (arr) => {
  return arr.filter(a => !!a);
}
```

我们所要做的就是用回调函数`a => !!a`调用`filter`方法，该函数返回一个数组，其中包含原始数组中的所有真值元素。

那么当我们这样称呼它的时候:

```
compact([1, 2, 3, false, undefined])
```

我们得到:

```
[
  1,
  2,
  3
]
```

从我们的`compact`函数返回。

# `concat`

Lodash `concat`方法将多个数组连接成一个数组并返回它。它的工作原理是将嵌套数组的嵌套深度减少一级，然后将它们放入返回的数组中。

例如，我们可以编写以下代码来创建我们自己的`concat`方法:

```
const concat = (arr) => {
  return arr.reduce((a, b) => a.concat(b), [])
}
```

在上面的代码中，我们使用了`reduce`方法和`concat`方法将多个数组组合成一个。

Array 的内置`concat`方法对于组合数组很有用。如果我们用以下方式调用我们的`concat`方法:

```
concat([1, 2, 3, [4],
  [
    [5]
  ]
])
```

我们得到:

```
[
  1,
  2,
  3,
  4,
  [
    5
  ]
]
```

就像我们用`concat`方法做的那样。JavaScript array 内置的`concat`方法也将嵌套深度减少了 1。

# 差异

`difference`方法让我们从一个给定的数组中找到不在另一个数组中的元素。

我们可以用 JavaScript 的内置数组方法实现或拥有等价的函数，如下所示:

```
const difference = (arr, arr2) => {
  return arr.filter(a => !arr2.includes(a));
}
```

在上面的代码中，我们有`difference`方法，它是通过使用`filter`方法实现的。在我们传递给`filter`的回调中，我们只是返回`!arr2.includes(a)`来查找不在`arr2`中的项目。

# `drop`

`drop`方法创建一个数组，从开始处删除给定数量的元素。

例如，我们可以通过编写以下代码来实现它:

```
const drop = (arr, n) => {
  return arr.slice(n);
}
```

我们只是将`n`直接传递给`slice`来返回一个数组，从原始数组中删除第一个`n`项。

那么当我们这样称呼它的时候:

```
drop([1, 2, 3, 4, 5], 2)
```

我们得到:

```
[
  3,
  4,
  5
]
```

![](img/bf280105c7332755a84fbab97695da61.png)

由 [Lukas Spitaler](https://unsplash.com/@lukispitaler?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# `dropRight`

`dropRight`方法与`drop`相反。它返回一个数组，从右边开始丢弃给定数量的元素。

同样，我们可以使用`slice`来实现`dropRight`，如下所示:

```
const dropRight = (arr, n) => {
  return arr.slice(0, -n);
}
```

我们只是将乘以 1 的`n`传递给`slice`来返回一个数组，其中第一个`n`项从原始数组中删除。

那么当我们这样称呼它的时候:

```
dropRight([1, 2, 3, 4, 5], 2)
```

我们得到:

```
[
  1,
  2,
  3
]
```

因为最后两个元素已从返回的数组中移除。

# 结论

Lodash 中的许多方法都可以用普通的 JavaScript 通过一些方法调用和操作轻松实现。

我们可以遍历我们的数组，并使用数组的`slice`方法来实现`chunk`。`slice`对`drop`和`dropRight`也有用。

要实现`compact`，我们只需要用返回真值的回调函数调用`filter`。

最后，为了实现`concat`，我们可以使用数组的内置`concat`方法来重复连接`reduce`。