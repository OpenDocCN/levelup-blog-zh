# JavaScript 算法:数组差异

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-array-differences-dc0f012b307a>

## 将两个数组之间的差异放到另一个数组中。

![](img/ece9af02608e092a1e3b7563622a5121.png)

由[乔舒亚·柯尔曼](https://unsplash.com/@joshstyle?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们将编写一个名为`mergeExclusive`的数组，它将接受两个数组，`arr1`和`arr2`。

该函数的目标是返回一个新数组，其中包含存在于`arr1`或`arr2`中但不同时存在于两者中的数字。

示例:

```
let arr1 = [1, 2, 3, 10, 5, 43, 14];
let arr2 = [1, 4, 5, 6, 14];mergeExclusive(arr1, arr2)//output: [2,3,10,43,4,6]
```

如果您查看`arr1`和`arr2`中的数字，您会看到数字 1、5 和 14 被排除在外，因为它们在两个数组中都存在。

我们要做的第一件事是使用`filter()`方法过滤掉`arr1`中存在但也存在于`arr2`中的每个元素。

为了检查这一点，我们使用了`includes()`方法，如果`arr2`数组包含该值，该方法将返回 true。如果是这样，我们不希望这样，所以我们包括一个 NOT 操作符来否定这个语句并把它扔掉。

我们将新数组赋给一个名为`arrDiff1`的变量。

```
let arrDiff1 = arr1.filter(function(*e*){
    return !(arr2.includes(e));
});
```

我们对`arr2`做同样的事情，并使用`filter()`方法过滤掉`arr2`中存在但也存在于`arr1`中的每个元素。

我们将新数组赋给一个名为`arrDiff2`的变量。

```
let arrDiff2 = arr2.filter(function(*e*){
    return !(arr1.includes(e));
});
```

现在我们有了两个独立的数组，它们包含了不存在于`arr1`和`arr2`中的元素，我们使用`concat()`方法合并它们并返回新的数组。

```
return arrDiff1.concat(arrDiff2);
```

下面是完整的函数:

如果你觉得这个算法有帮助，看看我的其他 JavaScript 算法解决方案:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-merge-two-arrays-e88e002e1554) [## JavaScript 算法:合并两个数组

### 用 JavaScript 编写合并两个数组的两种方法

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-merge-two-arrays-e88e002e1554) [](/javascript-algorithm-reverse-a-string-c24d06129f03) [## JavaScript 算法:反转字符串

### 在 JavaScript 中创建两种反转字符串的方法

levelup.gitconnected.com](/javascript-algorithm-reverse-a-string-c24d06129f03) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-convert-hours-into-seconds-c2252ad3c171) [## JavaScript 算法:将小时转换成秒

### 将小时转换成秒的函数。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-convert-hours-into-seconds-c2252ad3c171)