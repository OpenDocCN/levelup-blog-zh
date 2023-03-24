# JavaScript 的优点——数组方法和嵌套数组

> 原文：<https://levelup.gitconnected.com/good-parts-of-javascript-array-methods-and-nested-arrays-5e18c130f0db>

![](img/2ece85836710a326acecf1cc209a0c9b.png)

照片由 [Giovanna Gomes](https://unsplash.com/@giisilveira?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是世界上最流行的编程语言之一。它可以做很多事情，并且有一些领先于许多其他语言的功能。

在本文中，我们将研究用方法和嵌套数组操作 JavaScript 数组的方法。

# 数组和对象混淆

可能很容易混淆数组和对象。

对象可能有数字键和`length`属性。

然而，它们不是数组。

`typeof`操作符对检查数组没有帮助。这是因为在数组上使用`typeof`只会返回`'object'`。

要检查一个东西是否是一个数组，我们可以使用`Array.isArray`方法。

# 方法

JavaScript 数组有很多方法。

有一些方法可以将数组中的元素映射到不同的值。

此外，我们可以用不同的方式组合它们。

还有一些方法有助于搜索项目。

例如，我们可以使用`map`方法将数组的元素映射到不同的值，如下所示:

```
const arr = [1, 2, 3];
const mapped = arr.map(x => x ** 3);
```

现在我们返回一个新的数组，其中的条目为`arr`的立方。

所以我们得到`[1, 8, 27]`作为`arr`的值。

为了以某种方式组合数组的所有条目，我们可以使用`reduce`或`reduceRight`方法。

例如，我们可以写:

```
const arr = [1, 2, 3];
const sum = arr.reduce((acc, entry) => acc + entry, 0);
```

上面的代码通过将`arr`中的项目相加来组合它们。中间结果被分配给`ac`并且`entry`是被合并的数组条目。

0 是组合结果的初始值。

`reduce`从左到右组合条目。

要从右到左组合值，我们可以写:

```
const arr = [1, 2, 3];
const sum = arr.reduceRight((acc, entry) => acc + entry, 0);
```

`reduceRight`从`arr`结束开始。所以`acc`从 3 开始，然后 2 加 3 得 5，5 加 1 得 6。

`find`和`findIndex`方法获取对象的第一个实例。

返回符合我们得到的标准的第一个实例。

`findIndex`返回已找到的第一个实例的索引。

所以我们可以写:

```
const arr = [1, 2, 3];
const result = arr.find(x => x === 1);
```

而`result`是 1。

或者我们可以用`findIndex`:

```
const arr = [1, 2, 3];
const result = arr.findIndex(x => x === 1);
```

并得到`result`为 0，是 1 的索引。

`filter`方法返回一个数组，该数组包含满足给定条件的条目。

例如，我们可以写:

```
const arr = [1, 2, 3];
const result = arr.filter(x => x % 2 === 1);
```

那么`result`就是`[1, 3]`，因为我们指定了`x % 2 === 1`。我们指定的条件是返回所有奇数。

![](img/039753af2a0d32d85fb7b6ce5b3cf84d.png)

[布兰登·哈维](https://unsplash.com/@brandenharvey?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 规模

JavaScript 中没有多维数组。

然而，我们可以有数组的数组。

例如，我们可以通过编写以下代码来创建数组的数组:

```
const arr = [];
arr[0] = [1, 2, 3];
arr[1] = [4, 5, 6];
```

现在我们可以通过编写以下代码来循环遍历它们:

```
const arr = [];
arr[0] = [1, 2, 3];
arr[1] = [4, 5, 6];for (let i = 0; i < arr.length; i++) {
  for (let j = 0; j < arr[i].length; j++) {
    console.log(arr[i][j]);
  }
}
```

我们遍历了顶层数组中的项目。

然后在循环体中，我们遍历每个数组的项。

如果我们运行这个循环，我们会得到:

```
1
2
3
4
5
6
```

从控制台日志中。

此外，我们可以使用 for-of 循环来使这一点更清楚。例如，我们可以写:

```
const arr = [];
arr[0] = [1, 2, 3];
arr[1] = [4, 5, 6];for (const a of arr) {
  for (const b of a) {
    console.log(b);
  }
}
```

我们得到了同样的结果。我们只是使用 for-of 循环来使我们的迭代代码更干净。

# 结论

数组方法会让我们的生活更轻松。我们可以调用`find`和`findIndex`方法来查找项目。

`map`将数组条目从一个值映射到另一个值。

`reduce`和`reduceRight`让我们将数组条目合并成一个值。

我们可以创建 JavaScript 嵌套数组，并将其用作多维数组。