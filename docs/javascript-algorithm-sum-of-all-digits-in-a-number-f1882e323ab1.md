# JavaScript 算法:一个数中所有数字的总和

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-sum-of-all-digits-in-a-number-f1882e323ab1>

## 写一个函数，计算一个数中所有数字的和。

![](img/d84c96df316870b329e8da4ee2dc38a4.png)

伯纳德·赫尔曼特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们将编写一个名为`sumDigits`的函数，它将接受一个整数`n`作为参数。

给你一个包含两个或更多数字的正整数。该函数的目标是返回数字中每个数字的总和。让我们看一个例子。

示例:

```
let num = 1235231;
sumDigits(num) // output: 17
```

我们取`1235231`中的每一个数字，把它们加起来，得到我们的总数。

```
1 + 2 + 3 + 5 + 2 + 3 + 1 = 17
```

让我们把这变成代码。

因为我们想单独查看每个数字，所以我们要把我们的数字变成一个数组。我们如何做到这一点？我们需要使用`toString()`将数字转换成字符串，并使用`split()`将其转换成数组。我们将包含数字的字符串数组赋给一个名为`numArr`的变量。

```
let numArr = n.toString().split("");
```

现在，我们需要找到一种方法来遍历数组，并获得所有这些数字的总和。

我们将使用`reduce()`方法。我在这篇文章中解释了 reduce 方法的作用。

```
let sum = numArr.reduce(function(*a*, *b*){
    return parseInt(a) + parseInt(b);
}, 0);
```

我们的 helper 函数将数组中的所有值相加，但在当前状态下，它们是字符串。为了将它们转换成整数，我们使用`parseInt()`。数组中所有数字的总和将进入一个名为`sum`的变量。

我们返回`sum`。

```
return sum;
```

以下是完整的代码:

如果您发现这个算法很有帮助，请查看我的其他 JavaScript 算法解决方案文章:

[](/javascript-algorithm-array-plus-array-19e17c70e9fe) [## JavaScript 算法:数组加数组

### 写一个计算两个数组之和的函数。

levelup.gitconnected.com](/javascript-algorithm-array-plus-array-19e17c70e9fe) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-zero-fuel-7e73be4fe91d) [## JavaScript 算法:零燃料

### 我们写了一个函数，它将决定你减少的燃料量是否足以让你到达最近的加油站…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-zero-fuel-7e73be4fe91d) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-age-appropriate-drinks-1912d2a09d9f) [## JavaScript 算法:适合年龄的饮料

### 我们编写了一个函数，根据一个人的年龄返回他应该喝什么样的饮料。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-age-appropriate-drinks-1912d2a09d9f)