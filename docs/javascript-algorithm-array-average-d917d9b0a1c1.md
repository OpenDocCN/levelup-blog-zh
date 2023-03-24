# JavaScript 算法:数组平均值

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-array-average-d917d9b0a1c1>

## 计算数组中所有数字的平均值。

![](img/cc8263509fa3725f4b4069d2ee521e15.png)

照片由[沃尔坎·奥尔梅斯](https://unsplash.com/@volkanolmez?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我们将编写一个名为`averageArray`的函数，它将接受一个包含整数`ar`的数组作为参数。

该函数的目标是计算并返回数组中所有数字的平均值。为了计算某个东西的平均值，你把所有数字相加，然后除以你计算的数字。听起来很难用语言表达，所以让我们来看一个例子。

示例:

```
let ar = [1, 3, 9, 15, 90];
let avg = averageArray(ar);//output 23.6
```

要获得平均值，需要对数组中的所有数字求和:

```
1 + 3 + 9 + 15 + 90 = 118
```

因为数组中有五个数字，你将计算`118 / 5`来得到`23.6`的输出。

让我们创建我们的函数。

首先，我们将创建一个名为`numCount`的变量。这个变量将保存数组的长度。我们将总和除以这个数，得到我们的平均值。

```
let numCount = ar.length;
```

接下来，我们将获得数组中所有数字的总和。为此，我们将使用`reduce()`方法。为了使用`reduce()`方法，我们需要一个助手函数，所以我们将创建一个名为`reducer`的变量作为助手函数。

```
const reducer = (*accumulator*, *currentValue*) => accumulator + currentValue;
```

助手函数是一个回调函数，`reduce()`方法将为数组中的每个数字调用这个函数。`reducer`函数获取数组中的前一个值，并将下一项添加到数组中。该总和存储在`accumulator`中。随着函数遍历数组，`accumulator`累加所有值的总和。最终，当数组中的所有值相加后，回调函数将返回最终的和。

我们将最终总和放入一个名为`sum`的变量中。

```
let sum = ar.reduce(reducer);
```

接下来，我们通过取`sum`除以`numCount`得到平均值并返回。

```
return sum / numCount;
```

就是这样。以下是剩余的代码:

如果你觉得这个算法有帮助，看看我的其他 JavaScript 算法解决方案:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-angry-professor-89570ba8d863) [## JavaScript 算法:愤怒的教授

### 对于今天的算法，我们将编写一个名为 angryProfessor 的函数，它将接受两个输入:一个整数…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-angry-professor-89570ba8d863) [](https://medium.com/javascript-in-plain-english/solve-me-first-7a2f53789228) [## JavaScript 中的算法:先解决我

### 在 JavaScript 中完成一个名为 solveMeFirst 的函数，这个函数做的是计算

medium.com](https://medium.com/javascript-in-plain-english/solve-me-first-7a2f53789228) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-funny-string-adabc8eac4d7) [## JavaScript 算法:有趣的字符串

### 对于今天的算法，我们要写一个叫做 funnyString 的函数，它接受一个输入:一个字符串，s。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-funny-string-adabc8eac4d7)