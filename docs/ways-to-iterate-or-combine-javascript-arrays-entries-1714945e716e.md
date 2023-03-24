# 迭代或组合 JavaScript 数组条目的方法

> 原文：<https://levelup.gitconnected.com/ways-to-iterate-or-combine-javascript-arrays-entries-1714945e716e>

![](img/f461c141a56cfccb1c701e76b8e22bd4.png)

布雷特·乔丹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

使用 JavaScript 有很多方法。例如，有很多方法可以遍历数组的元素。

在本文中，我们将研究在数组中迭代和组合数据项的方法。

# `Array.prototype.reduce`

array 实例的`reduce`方法用于将数组中的条目组合成一个值。

它最多需要两个参数。第一个是一个回调函数，它将目前为止的组合值作为第一个参数，将正在处理的当前值作为第二个参数。

第三个参数是当前值的索引，最后一个是原始数组。这两个都是可选的。

第二个参数是用于组合值的初始值。

例如，我们可以用它来对数组中的所有项求和，如下所示:

```
const result = [1, 2, 3].reduce((a, b) => a + b, 0);
```

在上面的代码中，我们调用了`[1, 2, 3]`上的`reduce`,并通过传入来计算条目的总和:

```
(a, b) => a + b
```

其中`a`是到目前为止的总和，`b`是当前正在处理的项目。0 是总和的初始值。

那么`result`应该是 6，因为它是数组中所有数字的总和。

# `Array.prototype.join`

`join`方法将所有的数组条目组合成一个字符串，由我们选择的分隔符分隔。

它有一个参数，这个参数是一个带有我们想要使用的分隔符的字符串。

例如，我们可以如下使用它:

```
const result = [1, 2, 3].join(',');
```

然后我们得到`result`的`1,2,3`。

# 用于循环等待的 ES2018

`for...await...of`循环用于遍历承诺，以确保它们按顺序完成。

就像`for...of`但是是为了承诺。例如，我们可以如下使用它:

```
const promises = [1, 2, 3, 4, 5].map(a => new Promise((resolve) => setTimeout(() => resolve(a), 1000)));(async () => {
  for await (const p of promises) {
    console.log(p);
  }
})()
```

在上面的代码中，我们有一组承诺，我们使用了`for...await...of`循环一次解决一个。

因此，正如我们在`setTimeout`函数中指定的那样，我们应该在 1 秒钟的延迟后看到控制台中记录的数字 1 到 5。

# `Array.prototype.flat`

array 实例的`flat`方法让我们通过减少数组的深度来展平数组。它需要一个正数参数来指定要展平的级别数。或者我们可以指定`Infinity`来递归展平数组。

它返回一个新的扁平数组。

例如，我们可以如下使用它:

```
const arr = [
  [
    [1], 2, 3
  ]
];const flattened = arr.flat(1);
```

在上面的代码中，我们有一个包含嵌套数组的数组。然后当我们用 1 作为参数调用`flat`时，我们得到`flattened`是:

```
[
  [
    1
  ],
  2,
  3
]
```

如果我们指定`Infinity`作为它的自变量如下:

```
const flattened = arr.flat(Infinity);
```

我们得到:

```
[
  1,
  2,
  3
]
```

作为`flattened`的值。

![](img/86a6700f085f1be276949d7425cf041a.png)

Andrew Stutesman 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# `Array.prototype.flatMap`

`flatMap`方法先调用`map`，然后调用`flat`。只有在完成映射后，它才能展平 1 个嵌套级别。

例如，如果我们有:

```
const arr = [
  1, 2, 3
];const flattened = arr.flatMap(a => [a * 2]);
```

那么`flattened`就是:

```
[
  2,
  4,
  6
]
```

因为我们通过将每个条目乘以 2 来映射条目，然后将每个条目放入它们自己的数组中。

然后`flatMap`的`flat`部分展平单个数组，这样我们就可以得到其中没有数组的条目。

如果我们写:

```
const arr = [
  1, 2, 3
];const flattened = arr.flatMap(a => [[a * 2]]);
```

然后我们得到:

```
[
  [
    2
  ],
  [
    4
  ],
  [
    6
  ]
]
```

因为`flatMap`的值仅向上变平一级。

# 结论

array `flat`方法让我们通过展平任何嵌套的数组来展平任意多个级别的数组。

`flatMap`方法依次执行与`map`和`flat(1)`相同的操作。

循环让我们按顺序完成一系列承诺。

Array 实例的`reduce`方法让我们将数组中的值合并成一个。

`join`方法让我们将数组条目组合成一个字符串。