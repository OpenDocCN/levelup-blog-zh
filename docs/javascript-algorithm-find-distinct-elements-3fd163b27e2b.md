# JavaScript 算法:寻找不同的元素

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-find-distinct-elements-3fd163b27e2b>

## 一个函数，它将输出一个包含来自其输入数组的不同元素的数组。

![](img/5e86914f3a954016cc0cbd27f3016a28.png)

威尔·迈尔斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我们将编写一个名为`getDistinctElements`的函数，它将接受一个包含整数`arr`的数组作为参数。

该函数的目标是从给定的数组输入中返回一个包含不同元素的新数组。换句话说，如果给定的数组包含一组重复的数字，那么新数组中的每个数字应该只有一个。没有重复。

示例:

```
let arr = [1, 2, 3, 6, -1, 2, 9, 9, 7, 10, -1, 100];
getDistinctElements(arr);// output: [1,2,3,6,-1,9,7,10,100]
```

首先，我们将创建一个空数组，并将其赋给一个名为`distinctArr`的变量。这是函数将在最后返回的数组。

```
let distinctArr = [];
```

接下来，我们将使用 for 循环和循环遍历`arr`。对于每一次迭代，我们都要检查当前的号码是否在`distinctArr`中不存在。如果这是真的，我们将该数字添加到`distinctArr`数组中。`arr`中所有其他相同数值的数字将被忽略。

如果迭代的元素存在于`distinctArr`中，那么`includes()`方法将返回`true`，但是因为我们寻找相反的元素，所以我们将 NOT 操作符放在语句的前面。

```
for (let i = 0; i < arr.length; i++) {
  if (!(distinctArr.includes(arr[i]))) {
    distinctArr.push(arr[i]);
  }
}
```

现在我们的`distinctArr`数组包含了来自`arr`的所有不同元素，我们可以返回它了。

```
return distinctArr;
```

下面是完整的函数:

如果你觉得这个算法有帮助，看看我的其他 JavaScript 算法解决方案:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-find-difference-between-two-arrays-ed8df86c4924) [## JavaScript 算法:找出两个数组之间的差异

### 返回一个数组的函数，该数组的元素在第一个数组中，但不在第二个数组中。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-find-difference-between-two-arrays-ed8df86c4924) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-hex-to-decimal-3400f3742d1e) [## JavaScript 算法:十六进制到十进制

### 写一个将十六进制数转换成十进制数的函数。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-hex-to-decimal-3400f3742d1e) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-dna-to-rna-conversion-cceb6abc1336) [## JavaScript 算法:DNA 到 RNA 的转换

### 我们编写一个函数，给定一个 DNA 字符串序列，并将其转换成 RNA 序列。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-dna-to-rna-conversion-cceb6abc1336)