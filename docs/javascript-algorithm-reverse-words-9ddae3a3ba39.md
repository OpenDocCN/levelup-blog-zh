# JavaScript 算法:反转单词

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-reverse-words-9ddae3a3ba39>

## 反转字符串中的每个单词

![](img/fb76d28f87d3e8f2e84d0750eed6e62d.png)

由 [Lukas Robertson](https://unsplash.com/@sheetstothewind?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我们将编写一个名为`reverseWords`的函数，它接受一个字符串`str`作为参数。

你会得到一串不同字数的单词。该函数的目标是反转字符串中的每个单词并返回它。

示例:

```
reverseWords('The quick brown fox jumps over the lazy dog.')
// output: 'ehT kciuq nworb xof spmuj revo eht yzal .god'
```

我们要做的第一件事是创建一个名为`reverseWordArr`的变量。这个变量将包含数组形式的字符串。

```
let reverseWordArr = str.split(" ")
```

因为我们想要创建另一个数组，但是包含字符串中所有反转的单词，所以我们将使用`map`方法。

```
.map(word => word.split("").reverse().join(""));
```

这是完整的变量:

```
let reverseWordArr = str.split(" ").map(word => word.split("").reverse().join(""));
```

现在我们已经创建了数组，它包含了字符串中的所有单词，但反过来，我们需要将数组转换回字符串并返回它。

```
return reverseWordArr.join(" ");
```

下面是完整的函数:

如果您发现这个算法很有帮助，请查看我的其他 JavaScript 算法解决方案文章:

[](https://medium.com/swlh/javascript-algorithm-sorted-union-9e316654561f) [## JavaScript 算法:排序联合

### 我们编写一个函数，它将返回从两个或更多其他数组中获取的唯一值的数组。

medium.com](https://medium.com/swlh/javascript-algorithm-sorted-union-9e316654561f) [](https://javascript.plainenglish.io/javascript-algorithm-square-n-sum-c43649711749) [## JavaScript 算法:平方和

### 如何对数组中的每个数字求平方，并返回所有数字的总和。

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-algorithm-square-n-sum-c43649711749) [](https://javascript.plainenglish.io/javascript-algorithm-reverse-an-array-without-using-reverse-238c9f58362a) [## JavaScript 算法:不使用 Reverse()反转数组

### 编写一个函数，不使用内置的 JavaScript reverse 方法来反转一个小数组。

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-algorithm-reverse-an-array-without-using-reverse-238c9f58362a)