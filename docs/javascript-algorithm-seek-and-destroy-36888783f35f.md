# JavaScript 算法:寻找和破坏

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-seek-and-destroy-36888783f35f>

## 我们编写了一个函数，它将从一个数组中删除与所提供的参数值相同的所有元素。

![](img/4b376c5c9c9f31fc73178b1a21026d9c.png)

我们将编写一篇名为`destroyer`的文章，它将接受一个必需的参数，这是一个数组(`arr`)。至少会有一个或多个额外的参数。

给你一个包含字符串、整数或者两者都包含的数组。数组后面是一个或多个参数。该函数的目标是删除数组中匹配这些附加参数的任何元素。

示例:

```
let arr = [1, 2, 3, 1, 2, 3];
arguments[1] = 2;
arguments[2] = 3;
```

如果我们看看我们的附加参数(`arguments[i]`，我们删除了数组中每个等于`2`和`3`的数字。完成后，该函数将返回`[1, 1]`。这些是仅存的价值。

让我们写我们的函数。

由于我们收到的参数数量可能不同，我们将使用`arguments`对象来获取函数收到的每个参数。`arguments`是一个类似数组的对象，包含传递给函数的参数值。

为了获得数组参数后面的所有值，我们将首先创建一个数组。

```
let values = [];
```

我们将循环遍历我们的参数，获取数组参数之后的所有值，并将它们推入`values`数组。

```
for (let i = 1; i < arguments.length; i++) {
    values.push(arguments[i]);
}
```

接下来，我们使用`filter()`方法过滤出`arr`中存在于`values`数组中的每个值。

我们使用`indexOf()`方法查看`arr`中的任何值是否存在于`values`数组中。如果它们不存在，我们保留那些值。

我们将新数组赋给一个名为`arr2`的变量。

```
let arr2 = arr.filter(function(currentValue) {
    return values.indexOf(currentValue) === -1;
});
```

我们返回`arr2`数组。

```
return arr2;
```

我们的功能到此结束。以下是剩余的代码:

如果您发现这个算法很有帮助，请查看我的其他 JavaScript 算法解决方案文章:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-sum-all-numbers-in-a-range-49f8d37a3fec) [## JavaScript 算法:对一个范围内的所有数字求和

### 我们编写一个函数，返回两个整数范围内所有数字的和。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-sum-all-numbers-in-a-range-49f8d37a3fec) [](/javascript-algorithm-where-do-i-belong-1dfc397fc887) [## JavaScript 算法:我属于哪里

### 如果我们把一个数字排序后放入一个数组，这个数字的索引是什么？

levelup.gitconnected.com](/javascript-algorithm-where-do-i-belong-1dfc397fc887) [](https://codeburst.io/javascript-algorithm-chunky-monkey-337991491b24) [## JavaScript 算法:矮胖猴子

### 我们编写一个函数，通过将一个一维数组分割成子数组组来创建一个二维数组…

codeburst.io](https://codeburst.io/javascript-algorithm-chunky-monkey-337991491b24)