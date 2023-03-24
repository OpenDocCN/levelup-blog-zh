# JavaScript 算法:简介

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-introduction-10a316c91b52>

![](img/99da00626dfdbbc4fb204cca3ced939f.png)

今天的算法很简单，但是我们要写一个名为`intro`的函数，它接受一个整数`v`和一个数组`arr`。

给你一个从最小到最大排列的数组。你也被给了一个整数`v`，目标是输出`v`在数组中的索引位置。这里有一个例子:

```
let arr = [2, 5, 7, 12];
let v = 2;
```

我们看到`v`等于 2，因此我们必须在`arr`数组中找到 2 的索引位置。因为数组是零索引的，所以从 0 而不是 1 开始计数。2 是数组中的第一项，这意味着 2 的索引位置是 0。该函数将输出 0。

让我们用这个一行程序把它变成代码。

```
return arr.indexOf(v);
```

我们使用`indexOf()`方法来查找指定值的第一个索引位置。在我们的例子中，指定的值是`v`。

```
function intro(v, arr) {
    return arr.indexOf(v);
}
```

这就是我们的代码。

这里是我最近的一些 JavaScript 算法文章，你可以看看:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-minimum-distances-65dfde1ca83f) [## JavaScript 算法:最小距离

### 对于今天的算法，我们要写一个名为 minimumDistances 的函数，它将接受一个数组 a 作为…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-minimum-distances-65dfde1ca83f) [](/javascript-algorithm-sherlock-and-squares-e1a39b5edef1) [## JavaScript 算法:夏洛克和正方形

### 对于今天的算法，我们将创建一个名为 squares 的函数，它将接受两个整数作为输入:a 和…

levelup.gitconnected.com](/javascript-algorithm-sherlock-and-squares-e1a39b5edef1) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-find-digits-8c639e9ab81a) [## JavaScript 算法:查找数字

### 对于今天的算法，我们将编写一个名为 findDigits 的函数，它将接受一个整数 n 作为输入。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-find-digits-8c639e9ab81a)