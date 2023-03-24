# JavaScript 中 map()、filter()和 reduce()的快速概述

> 原文：<https://levelup.gitconnected.com/sneak-peak-of-map-filter-and-reduce-in-javascript-79d38181a48>

![](img/d696bfe15702f7276610ea2ca661c294.png)

图片由[内森·杜姆劳](https://unsplash.com/@nate_dumlao?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

学习如何在 JavaScript 中使用`map`、`filter`和`reduce`。

## 高阶函数

`**higher-order function**`是一个以一个或多个函数作为参数或者返回一个函数作为结果的函数。`map`、`filter`、`reduce`都是高阶函数，以一个函数作为自变量。

## 映射、过滤、减少基本要素

所有的函数都是 JavaScript 原型的一部分，这意味着它们必须直接在数组上被调用。

```
const arr = [1, 2, 3]arr.map()
arr.filter()
arr.reduce()
```

当我们使用`map`、`filter`和`reduce`时，我们不能:

*   `break`循环
*   使用`continue`

# map →对数组的每个元素执行函数

数组的每个元素都被传递给回调函数，并返回一个相同长度的新数组。

何时使用`map`:如果我们想对数组中的每个元素执行相同的操作/转换，并用转换后的值得到一个相同长度的新数组。

示例 1:

```
var numbers= [1,2,3,4,5];var doubled  = numbers.map(n => n * 2);doubled; // [2,4,6,8,10]
```

# 过滤→删除不满足条件的项目

数组的每个元素都被传递给回调函数。在每次迭代中，如果回调返回`true`，那么该元素将被添加到新数组中，否则，它不会被添加到新数组中。

```
var numbers = [1,2,3,4,5];var greaterThan2 = numbers.filter(n => n > 2);greaterThan2; // [3,4,5]
```

# Reduce →从数组元素创建单个值

在使用`reduce`时，我们需要声明`accumulator(final result)`的初始值。在每次迭代中，我们在回调函数中执行一些操作，这些操作将被添加到`accumulator`中。

示例 1:数字的和

```
var numbers = [1,2,3,4,5];var initialVal = 0;let result=numbers.reduce(**(accu, val) => val + accu** , initialVal);console.log(result) // 15
```

示例 2:根据用户名创建对象数组

# 真实世界的例子

让我们创建一个真实世界的实际例子:进行面试

*   `map`:对多名考生进行测试
*   `filter`:选择通过测试的候选人
*   `reduce`:从选中的候选人中创建一个团队

和我一起加入你的 hand🖐[JavaScript jeep🚙💨](https://medium.com/u/f9ffc26e7e69?source=post_page-----79d38181a48--------------------------------)。

> [请我喝杯咖啡](https://www.buymeacoffee.com/Jagathish)

![](img/b6bf5ad0e294280e9f04b1c5cd49ad15.png)

[**请我喝咖啡**](https://www.buymeacoffee.com/Jagathish)