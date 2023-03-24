# JavaScript 算法:两个数组的区别

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-difference-of-two-arrays-5bea5ea50e1f>

## 我们将编写一个函数，返回两个数组的对称差。

![](img/2b2ef9c2c75050c499b9dac59d616239.png)

我们将编写一个名为`diffArray`的函数，它将接受两个数组(`arr1`和`arr2`)作为参数。

给你两个不同长度的数组。该函数的目标是返回一个新数组，该数组包含存在于两个数组之一但不同时存在于两个数组中的项目。该数组不必按照任何特定的顺序排列。

示例:

```
let arr1 = ["andesite", "grass", "dirt", "pink wool", "dead shrub"];
let arr2 = ["diorite", "andesite", "grass", "dirt", "dead shrub"];
```

在上面的示例中，如果我们只强调两个阵列之间的差异，我们会得到:

```
["andesite", "grass", "dirt", **"pink wool"**, "dead shrub"]
[**"diorite"**, "andesite", "grass", "dirt", "dead shrub"]
```

突出显示的项目是只存在于一个数组中而不存在于两个数组中的唯一项目。该函数将返回`["diorite", "pink wool"]`。

让我们开始编写函数。

我们将首先创建两个数组，`arrDiff1`和`arrDiff2`。对于这两个数组，我们将使用`indexOf()`和`filter()`的组合。

对于`arrDiff1`，我们使用 filter 方法只返回存在于`arr1`中而不存在于`arr2`中的值。如果 indexOf 为该值返回一个`-1`，我们将它保存在数组中。

```
let arrDiff1 = arr1.filter(function(currentValue) {
    return arr2.indexOf(currentValue) === -1;
});
```

对于`arrDiff2`，我们做同样的事情，但是我们只返回存在于`arr2`中而不存在于`arr1`中的数字。

```
let arrDiff2 = arr2.filter(function(currentValue) {
    return arr1.indexOf(currentValue) === -1;
});
```

现在我们有了两个包含彼此差异的数组，我们使用`concat()`连接这两个数组并返回结果。

```
return arrDiff1.concat(arrDiff2);
```

下面是该函数的其余部分:

如果您发现这个算法很有帮助，请查看我的其他 JavaScript 算法解决方案文章:

[](/javascript-algorithm-remove-the-final-exclamation-mark-8a7c3c8cd3a2) [## JavaScript 算法:去掉最后一个感叹号

### 我们要写一个函数来删除字符串末尾的感叹号

levelup.gitconnected.com](/javascript-algorithm-remove-the-final-exclamation-mark-8a7c3c8cd3a2) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-convert-a-boolean-to-a-string-d720ac34ecd5) [## JavaScript 算法:将布尔值转换为字符串

### 我们来看看将布尔值转换成字符串的许多方法

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-convert-a-boolean-to-a-string-d720ac34ecd5) [](/javascript-algorithm-utopian-tree-b38100fff575) [## JavaScript 算法:乌托邦树

### 对于今天的算法，我们要写一个名为 utopianTree 的函数，它接受一个输入:一个整数 n。

levelup.gitconnected.com](/javascript-algorithm-utopian-tree-b38100fff575)