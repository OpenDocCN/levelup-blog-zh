# JavaScript 数组和条件技巧来节省我们的时间和挫折

> 原文：<https://levelup.gitconnected.com/javascript-arrays-and-conditional-tricks-to-save-us-time-and-frustration-4eba161b859d>

![](img/98b1c8afe8342ff4a8a32df34e01e0c7.png)

[埃尔南·卢西奥](https://unsplash.com/@hernanlucio?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在编写 JavaScript apps 时，我们经常要编写代码来解决经常遇到的问题。

在本文中，我们将研究 JavaScript 代码中遇到的一些常见问题，这样我们就不必进一步寻找这些问题的答案。

# 展平多维数组

我们可以通过对数组使用各种方法来展平多维数组。

有`flat`、`flatMap`和`concat`方法来展平数组。其中一些与扩展运算符一起使用。

例如，我们可以使用数组实例的`concat`方法和 spread 操作符，如下所示:

```
const arr = [1, [2, 3],
  [4, 5], 6
];
const flattened = [].concat(...arr);
```

在上面的代码中，我们在一个空数组上调用了`concat`方法，将`arr`数组中的项目放入一个新的数组中。

spread 操作符将数组中的项作为参数展开，这样就可以用`concat`将它们填充到新数组中。

然后我们得到`flattened`是`[1, 2, 3, 4, 5, 6]`。

如果我们有一个深度嵌套的数组，那么我们需要更加努力地递归展平数组。

例如，我们可以这样做:

```
const flatten = (arr) => {
  const flat = []; arr.forEach(a => {
    if (Array.isArray(a)) {
      flat.push(...flatten(a));
    } else {
      flat.push(a);
    }
  }); return flat;
}
```

在上面的代码中，我们有一个`flatten`函数，它接受一个数组`arr`作为参数。

在函数体中，我们对`arr`调用`forEach`。在回调中，我们通过用`a`调用`Array.isArray`来检查每个数组条目是否是一个数组条目，其中`a`是被循环的数组条目。

如果是，那么我们将展平的数组推入到`flat`数组中，并对其调用`flatten`来展平嵌套的数组。

否则，我们只需将条目推入`flat`。然后我们返回`flat`，这是递归展平的数组。

那么我们可以这样称呼它:

```
const arr = [1, [2, 3],
  [[4, 5]], 6
];
const flattened = flatten(arr);
```

然后我们得到`flattened`就是`[1, 2, 3, 4, 5, 6]`。

JavaScript 的 array 实例也有一个`flat`方法，可以将数组展平任意深度。

它采用一个正整数或`Infinity`作为参数，按照给定的深度级别展平嵌套数组或分别展平所有级别。

`flat`返回新的展平数组。

例如，我们可以这样称呼它:

```
const arr = [1, [2, 3],
  [
    [4, 5]
  ], 6
];
const flattened = arr.flat(1);
```

然后我们得到:

```
[
  1,
  2,
  3,
  [
    4,
    5
  ],
  6
]
```

如果我们通过`Infinity`如下:

```
const arr = [1, [2, 3],
  [
    [4, 5]
  ], 6
];
const flattened = arr.flat(Infinity);
```

那么`flattened`就是`[1, 2, 3, 4, 5, 6]`。

`flatMap`方法调用`map`然后用 level 1 调用`flat`。

例如，我们可以如下使用它:

```
const arr = [1, 2, 3];
const flattened = arr.flatMap(a => [a])
```

那么`flattened`就是`[1, 2, 3]`，因为`flatMap`调用`map`将每个数字映射到一个只有一个数字的数组。然后它调用`flat`将数组展平 1 个嵌套层次。

因此，我们得到`flattened`就是`[1, 2, 3]`。

![](img/fb6c73eb1f788885e1d02601c0517901.png)

照片由[加里·尼萨姆](https://unsplash.com/@cathus?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 短路条件

如果我们有如下的条件语句:

```
if (condition) {
  doSomething();
}
```

我们可以用`&&`操作符将其缩短，如下所示:

```
condition && doSomething();
```

这两段代码是等价的，因为在第二段代码中，只有当两个操作数都为真时，`&&`运算符才返回`true`。

因此，当`condition`为真值时，它将继续计算第二个操作数，即运行`doSomething()`。

这与`if`语句的作用相同。如果`condition`是真实的，那么`doSomething()`是运行的。

# 结论

使用 JavaScript 有多种方法来展平数组。一个是`flat`方法，通过任意深度级别展平数组，包括`Infinity`。

`concat`方法可以和 spread 操作符一起使用来做同样的事情。

`flatMap`方法调用深度级别为 1 的`map`和`flat`。

为了缩短我们的条件语句，如果一个条件为真，我们可以使用`&&`操作符来代替它。