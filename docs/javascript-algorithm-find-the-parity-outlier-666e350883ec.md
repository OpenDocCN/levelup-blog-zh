# JavaScript 算法:寻找奇偶异常值

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-find-the-parity-outlier-666e350883ec>

## 找出奇数(或偶数)

![](img/3b2ad01726e1f9ff972c66b06e89dba1.png)

鲁珀特·布里顿在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们将编写一个名为`findOutlier`的函数，它接受整数数组`integers`作为参数。

给你一个整数数组，这个数组要么全是奇数，要么全是偶数，只有一个数字例外。该函数的目标是输出一个与其他数字不同或相反的数字。

示例:

```
[2, 4, 0, 100, 4, 11, 2602, 36] // returns 11
[160, 3, 1719, 19, 11, 13, -21] // returns 160
```

在第一个例子中，除了`11`，数组中的每个数字都是偶数，所以函数将返回`11`。

在第二个例子中，除了`160`，数组中的所有数字都是奇数，所以函数返回`160`。

为了开始这个函数，我们创建了两个数组:

```
let oddArr = [];
let evenArr = [];
```

`oddArr`数组将包含来自数组输入的每个奇数。`evenArr`变量对偶数也是如此。

我们遍历数组输入:

```
for (let i = 0; i < integers.length; i++) {
    if (integers[i] % 2 === 0) {
        evenArr.push(integers[i]);
    } else {
        oddArr.push(integers[i]);
    }
}
```

在 for 循环中，我们检查当前迭代的数字是奇数还是偶数。我们将该数字放入适当的数组中。

循环完成后，我们比较两个数组，并检查哪个数组包含异常值:

```
if (evenArr.length == 1) {
    return evenArr[0];
} else {
    return oddArr[0];
}
```

离群值将属于长度为 1 的数组，因此函数将返回该数字。

下面是完整的函数:

如果您发现这个算法很有帮助，请查看我的其他 JavaScript 算法解决方案文章:

[](https://javascript.plainenglish.io/javascript-algorithm-detect-pangram-9d57ca713d0d) [## JavaScript 算法:检测 Pangram

### 盘符是一个包含字母表中每个字母至少一次的句子。

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-algorithm-detect-pangram-9d57ca713d0d) [](/javascript-algorithm-calculate-sum-of-all-numbers-in-a-jagged-array-94230951d726) [## JavaScript 算法:计算交错数组中所有数字的和

### 创建一个计算交错数组中所有数字之和的函数。

levelup.gitconnected.com](/javascript-algorithm-calculate-sum-of-all-numbers-in-a-jagged-array-94230951d726) [](/javascript-algorithm-camelcase-a-sentence-e5f32995a39d) [## JavaScript 算法:Camelcase 一句话

### 创建一个将文本中每个单词的首字母大写的函数。

levelup.gitconnected.com](/javascript-algorithm-camelcase-a-sentence-e5f32995a39d)