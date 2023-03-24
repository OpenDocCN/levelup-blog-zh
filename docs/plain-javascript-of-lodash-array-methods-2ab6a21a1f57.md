# Lodash 数组方法的普通 JavaScript

> 原文：<https://levelup.gitconnected.com/plain-javascript-of-lodash-array-methods-2ab6a21a1f57>

![](img/ca03f61875c497ce8b5e57768e9b28d2.png)

照片由[乔丹·戴维斯](https://unsplash.com/@thedavis42?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Lodash 是一个非常有用的工具库，让我们可以轻松地处理对象和数组。

然而，现在 JavaScript 标准库正在赶上诸如 Lodash 之类的库，我们可以用简单的方法实现许多功能。

在本文中，我们将看看如何用普通 JavaScript 实现一些 Lodash 方法。

# dropRightWhile

`dropRightWhile`函数让我们从右边开始创建数组的一部分，直到满足一个条件。条件由回调决定。

我们可以用如下的普通 JavaScript 实现它:

```
const dropRightWhile = (arr, condition) => {
  const newArr = [];
  for (let i = arr.length - 1; i >= 0; i--) {
    if (condition(arr[i])) {
      return newArr;
    }
    newArr.push(arr[i]);
  }
  return newArr;
}
```

在上面的代码中，我们有一个反向循环的`for`循环。如果将一个数组条目作为值的`condition`函数返回`true`，那么我们返回`newArr`，它有我们想要保留的被推入数组的条目。

如果循环结束而没有返回`newArr`，那么它也将返回`newArr`。

因此，当我们有:

```
const arr = [1, 2, 3]
const dropped = dropRightWhile(arr, (a) => a === 2)
```

我们得到`dropped`是`[3]`，因为我们在满足回调条件时停止了函数，也就是当数组条目为 2 时。

`dropRightWhile`也可以带键名的字符串，在返回切片数组之前检查它是否是`true`。

我们可以用下面的代码轻松地添加检查:

```
const dropWhile = (arr, key) => {
  const newArr = [];
  for (let i = arr.length - 1; i >= 0; i--) {
    if (arr[i][key]) {
      return newArr;
    }
    newArr.push(arr[i]);
  }
  return newArr;
}
```

然后当我们有了下面的数组并如下调用它:

```
const arr = [{
    name: 'foo',
    active: false
  },
  {
    name: 'bar',
    active: true
  },
  {
    name: 'baz',
    active: false
  }
]
const dropped = dropRightWhile(arr, 'active');
```

我们得到:

```
[
  {
    "name": "foo",
    "active": false
  }
]
```

因为我们返回数组，我们从右边开始推送条目，直到`active`为`true`。

# 下降时间

`dropWhile`是`dropRightWhile`的反义词。它通过从头开始并将这些项推入一个新的数组来获取数组的一部分，并在循环结束时或回调中的条件返回时返回它`true`。

像`dropRightWhile`一样，回调使用一个数组条目。例如，我们可以自己实现它，如下所示:

```
const dropWhile = (arr, condition) => {
  const newArr = [];
  for (const a of arr) {
    if (condition(a)) {
      return newArr;
    }
    newArr.push(a);
  }
  return newArr;
}
```

在上面的代码中，我们使用了`for...of`循环而不是常规的`for`循环，因为我们只是通过`arr`向前循环，这可以通过`for`循环轻松完成。

`dropWhile`也可以在返回切片数组之前，取一个带有键名的字符串来检查它是否是`true`。

我们可以用下面的代码轻松地添加检查:

```
const dropWhile = (arr, key) => {
  const newArr = [];
  for (const a of arr) {
    if (a[key]) {
      return newArr;
    }
    newArr.push(a);
  }
  return newArr;
}
```

然后当我们有了下面的数组并如下调用它:

```
const arr = [{
    name: 'foo',
    active: false
  },
  {
    name: 'bar',
    active: true
  },
  {
    name: 'baz',
    active: false
  }
]
const dropped = dropWhile(arr, 'active');
```

我们得到:

```
[
  {
    "name": "foo",
    "active": false
  }
]
```

因为我们返回数组，我们将条目放入其中，直到`active`为`true`。

![](img/8c31e05b92d8eb6c860cdf40c6208835.png)

[Kate Joie](https://unsplash.com/@katejoie?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 充满

`fill`方法通过用我们提供的从起始索引开始到结束索引结束的值替换原始数组中的值，用从起始索引开始但不到结束索引的值填充数组的元素。

我们可以如下实现`fill`:

```
const fill = (arr, value, start, end) => arr.fill(valu, start, end)
```

在上面的代码中，我们有 4 个像 Lodash 的`fill`方法一样的参数，它们是原始数组的`arr`，`value`是我们想要填充数组的值。`start`，这是我们要替换的数组条目的起始索引，以及`end`，这是我们要替换的数组的结束索引。

那么当我们调用`fill`时如下:

```
const filled = fill([1, 2, 3, 4, 5], 8, 0, 3);
```

我们得出`filled`是:

```
[8, 8, 8, 4, 5]
```

正如我们所见，数组实例的`fill`方法与 Lodash 的`fill`方法做同样的工作。

# 结论

我们可以用普通 JavaScript 的数组方法轻松实现`dropWhile`、`dropRightWhile`和`fill`。通过几行代码，我们消除了使用 Lodash 数组方法的需要。