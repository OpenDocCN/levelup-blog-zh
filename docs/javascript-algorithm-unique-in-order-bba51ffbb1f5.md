# JavaScript 算法:顺序唯一

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-unique-in-order-bba51ffbb1f5>

## 返回唯一的项目列表，而不改变元素的原始顺序

![](img/c41a623456de8245bde587aea442670a.png)

布雷特·乔丹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们将编写一个名为`uniqueInOrder`的函数，它将接受一个字符串或数组`iterable`作为参数。

给你一个字符串或一组数字。该函数的目标是返回一个数组，该数组包含输入中的所有元素，但没有任何连续的重复。该函数还应该保持元素的原始顺序。

示例:

```
uniqueInOrder('ABBCcAD')
// output: ['A', 'B', 'C', 'c', 'A', 'D']uniqueInOrder([1,2,2,3,3])
// output: [1,2,3]
```

在第一个例子中，函数从字符串中移除了多余的`'B'`。即使有两个字母 c，它们也不是连续的重复，因为一个是大写字母，另一个是小写字母。

对于第二个例子，唯一有连续重复的数字是`2`和`3`，因此多余的 2 和 3 被删除。

让我们创建我们的函数。

我们做的第一件事是创建一个名为`strArr`的变量。在这个变量中，我们可以用它来把字符串转换成数组。问题不在于函数的所有输入都是字符串。我们需要检查输入是否是一个数组。如果是数组，我们直接把输入赋值给`strArr`。

如果不是，而是一个字符串，我们将字符串转换为数组，然后将数组赋给`strArr`。

```
let strArr = Array.isArray(iterable) ? iterable : iterable.split('');
```

接下来，我们创建另一个名为`unique`的变量。这个变量将包含我们唯一的数组，没有连续的重复数组元素。我们使用`filter()`方法过滤掉任何相邻的值相同的元素，只保留其中的一个。

```
let unique = strArr.filter((letter, i) => {
    return strArr[i] != strArr[i + 1];
});
```

我们通过返回数组来结束。

```
return unique;
```

下面是完整的函数:

如果您发现这个算法很有帮助，请查看我的其他 JavaScript 算法解决方案文章:

[](https://javascript.plainenglish.io/javascript-algorithm-square-every-digit-932de6046c54) [## JavaScript 算法:平方每个数字

### 将一个数字的每一个数字平方，然后连接起来

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-algorithm-square-every-digit-932de6046c54) [](https://javascript.plainenglish.io/javascript-algorithm-array-diff-90e109b9acf4) [## JavaScript 算法:Array.diff

### 实现从一个列表中减去另一个列表的差函数

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-algorithm-array-diff-90e109b9acf4) [](/javascript-algorithm-reverse-a-string-c24d06129f03) [## JavaScript 算法:反转字符串

### 在 JavaScript 中创建两种反转字符串的方法

levelup.gitconnected.com](/javascript-algorithm-reverse-a-string-c24d06129f03)