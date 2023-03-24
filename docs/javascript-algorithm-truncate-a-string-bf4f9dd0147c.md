# JavaScript 算法:截断字符串

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-truncate-a-string-bf4f9dd0147c>

## 我们编写了一个函数，如果字符串长度超过给定的长度限制，它将截断字符串

![](img/84a9fe7cd51529eb46182e30fbb37727.png)

我们将编写一个名为`truncateString`的函数，它将接受一个字符串(`str`)和一个整数(`num`)作为参数。

该函数的目标是查看给定字符串的长度是否大于给定的最大字符串长度(`num`)。如果是，则在最大长度处截断字符串，并在末尾用省略号(…)返回。如果字符串短于或等于截止字符串长度，则按原样返回字符串。

示例:

```
let str = "A-tisket a-tasket A green and yellow basket";
let num = 8;// output: "A-tisket..."
```

对于我们上面的例子，我们的字符串截止在 8 个字符。由于字符串长度超过 8 个字符，我们将字符串截短为 8 个字符，并返回末尾带有省略号的字符串。

让我们开始写函数。

首先，我们检查字符串(`str`)的长度是否大于我们的最大字符串长度(`num`)。

```
if (str.length > num) {
    let subStr = str.substring(0, num);
    return subStr + "...";
} else {
    return str;
}
```

如果`str`大于`num`，我们使用`substring()`方法返回一部分字符串，从索引 0 一直到(但不包括)`num`。我们将被截断的字符串赋给一个名为`subStr`的变量。我们返回带有省略号的截断字符串。

如果`str`不大于`num`，那么我们原样返回我们的字符串输入。

下面是该函数的其余部分:

如果你觉得这个算法有帮助，看看我的其他 JavaScript 算法解决方案:

[](https://medium.com/@endubueze00/javascript-algorithm-zero-fuel-7e73be4fe91d) [## JavaScript 算法:零燃料

### 我们写了一个函数，它将决定你减少的燃料量是否足以让你到达最近的加油站…

medium.com](https://medium.com/@endubueze00/javascript-algorithm-zero-fuel-7e73be4fe91d) [](https://medium.com/@endubueze00/javascript-algorithm-pre-populate-an-array-461c758ce903) [## JavaScript 算法:预先填充一个数组

### 我们编写了一个函数，它将输出一个从 1 到 n 的数组，而不使用 for 循环

medium.com](https://medium.com/@endubueze00/javascript-algorithm-pre-populate-an-array-461c758ce903) [](https://medium.com/@endubueze00/javascript-algorithm-return-largest-numbers-in-arrays-476914b717a7) [## JavaScript 算法:返回数组中最大的数字

### 我们编写了一个函数，从数组参数中返回一个数组，该数组包含每个子数组中最大的数字

medium.com](https://medium.com/@endubueze00/javascript-algorithm-return-largest-numbers-in-arrays-476914b717a7)