# JavaScript 算法:预先填充一个数组

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-pre-populate-an-array-461c758ce903>

## 我们编写了一个函数，它将输出一个从 1 到 n 的数组，而不使用 for 循环

![](img/3be073870f4bbe60666e033f363013a3.png)

我们将编写一个名为`arrFill`的函数，它将接受一个整数(`n`)作为参数。

给你一个大于或等于 1 的整数。该函数的目标是输出一个从 1 到 n(包括 1 和 n)的正数数组。

```
let n = 4;
// output [1, 2, 3, 4]
```

虽然我们可以用 for 循环来解决这个问题，并在循环中填充数组。我们可以使用`map()`方法尝试另一种方法。

在使用 map 方法之前，我们需要创建一个数组并预先填充它。我们将从使用`Array.fill`建立一个带有`n`插槽的数组开始，并用零填充它。

```
new Array(n).fill(0)
```

现在我们有一个长度为`n`的随机数组。接下来，我们将使用 map 方法从 1 到 n 填充数组。

在我们的回调函数中，我们的数组只有零，所以我们不能对数组中的数字做任何事情。我们可以对每个数字的数组索引做一些事情。因为我们想从 1 数到 n，我们将用数组索引填充数组(`index + 1`说明零索引)。

我们将把新数组赋给一个名为 arr 的变量。

```
let arr = new Array(n).fill(0).map(function(num, index) {
    return index + 1;
});
```

然后，我们可以返回我们的数组。

```
return arr;
```

下面是该函数的其余部分:

```
function arrFill(n) {
  let arr = new Array(n).fill(0).map(function(num, index) {
    return index + 1;
  });

  return arr;
}
```

使用 for 循环来预填充数组没有任何问题。这只是用从 1 到 n 的数字预填充数组的另一种方式，不需要使用标准的 JavaScript 循环。

如果你想阅读更多的 JavaScript 算法文章，这里有一些最近的:

[](/javascript-algorithm-golf-code-c9547ba4f5e5) [## JavaScript 算法:高尔夫代码

### 我们将编写一个函数，它将根据您完成的击球次数返回您的得分项…

levelup.gitconnected.com](/javascript-algorithm-golf-code-c9547ba4f5e5) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-generate-range-of-integers-73f739b4871) [## JavaScript 算法:生成整数范围

### 我们要写一个函数，它会在每隔一段时间后产生一个在给定的最小值和最大值之间的整数范围…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-generate-range-of-integers-73f739b4871) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-viral-advertising-168a872cb557) [## JavaScript 算法:病毒式广告

### 对于今天的算法，我们要写一个叫做 viralAdvertising 的函数，它接受一个整数 n 作为…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-viral-advertising-168a872cb557)