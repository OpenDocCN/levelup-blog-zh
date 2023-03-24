# JavaScript 算法:我属于哪里

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-where-do-i-belong-1dfc397fc887>

## 如果我们将`a number` 排序后加入到`an array` 中，这个数的索引是什么？

![](img/3eb79fbfbbe712ce58976b2ffee035b6.png)

我们将编写一个名为`getIndex`的函数，它接受一个数组(`arr`)和一个整数(`num`)。

我们得到了一个数字和一个数组。如果我们将`num`排序后加入到`arr`中，这个数的索引是什么？该函数的目标是找到并返回该索引。

示例:

```
let arr = [10, 40, 30, 20, 50];
let num = 35;// output: 3
```

在上面的例子中，如果我们对数组进行排序，它将是:

`[10, 20, 30, 40, 50]`

既然已经排序了，我们看一下`35`并确定我们的数字的索引，如果我们把它添加到数组中的话。因为`35`在 30 到 40 之间，`35`会在 30 之后。我们的数组应该是这样的:

`[10, 20, 30, 35, 40, 50]`

如果将我们的`num`添加到排序数组中，它将有一个索引`3`。该函数将返回`3`。

让我们写我们的函数。

为了确定给定的数字在数组中应该有什么索引，我们必须首先将数字插入数组。

```
arr.push(num)
```

然后，我们使用`sort()`方法和比较回调函数对数组进行升序排序。我们将排序后的数组赋给一个名为`arr2`的变量。

```
let arr2 = arr.sort(function(a, b) {
    return a - b;
});
```

现在我们的数组已经排序了，我们使用`indexOf()`方法来检索`num`的索引。我们返回索引。

```
return arr2.indexOf(num);
```

我们的功能到此结束。以下是剩余的代码:

如果你觉得这个算法有帮助，看看我的其他 JavaScript 算法解决方案:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-remove-first-and-last-character-a80b7bc28536) [## JavaScript 算法:删除第一个和最后一个字符

### 我们编写了一个函数来删除字符串的第一个和最后一个字符

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-remove-first-and-last-character-a80b7bc28536) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-age-appropriate-drinks-1912d2a09d9f) [## JavaScript 算法:适合年龄的饮料

### 我们编写了一个函数，根据一个人的年龄返回他应该喝什么样的饮料。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-age-appropriate-drinks-1912d2a09d9f) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-alternating-characters-617eb0764cbc) [## JavaScript 算法:交替字符

### 对于今天的算法，我们将编写一个名为 alternatingCharacters 的函数，它将一个字符串 s 作为…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-alternating-characters-617eb0764cbc)