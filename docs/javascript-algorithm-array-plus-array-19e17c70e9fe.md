# JavaScript 算法:数组加数组

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-array-plus-array-19e17c70e9fe>

## 写一个计算两个数组之和的函数。

![](img/5eef452c663d49d50aac8673f2d35361.png)

我们将编写一个名为`arrayPlusArray`的函数，它将接受两个数组(`arr1`和`arr2`)作为参数。

给你两个数组。两个数组都包含正数。该函数的目标是返回两个数组中所有数字的总和。

示例:

```
let arr1 = [1, 2, 3];
let arr2 = [4, 5, 6];//output: 21
```

第一个数组中所有数字之和为`1 + 2 + 3 = 5`。第二个数组中所有数字的总和是`4 + 5 + 6 = 16`。当我们得到两个数组的和时，我们得到`5 + 16 = 21`。

让我们把这变成代码。

我们要做的第一件事是将`arr2`连接到`arr1`来创建一个大数组。我们将该数组赋给一个名为`newArr`的变量。

```
let newArr = arr1.concat(arr2);
```

接下来，我们使用`reduce()`方法计算`newArr`中所有数字的总和。我们的 reducer 函数累加数组中的所有值并返回总数。

我们将总数分配给一个名为`val`的变量。

```
let val = newArr.reduce(function(accumulator, currentValue){
    return accumulator + currentValue;
});
```

现在我们有了总数，我们可以返回`val`。

```
return val;
```

下面是该函数的其余部分:

如果您发现这个算法很有帮助，请查看我的其他 JavaScript 算法解决方案文章:

[](https://medium.com/@endubueze00/javascript-algorithm-validate-code-with-simple-regex-827bbbc066dd) [## JavaScript 算法:用简单的正则表达式验证代码

### 我们编写一个函数，使用正则表达式验证数字代码。

medium.com](https://medium.com/@endubueze00/javascript-algorithm-validate-code-with-simple-regex-827bbbc066dd) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-convert-html-entities-99719d8ca118) [## JavaScript 算法:转换 HTML 实体

### 编写一个函数，将选定数量的字符转换成相应的 HTML 实体。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-convert-html-entities-99719d8ca118) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-hex-to-decimal-3400f3742d1e) [## JavaScript 算法:十六进制到十进制

### 写一个将十六进制数转换成十进制数的函数。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-hex-to-decimal-3400f3742d1e)