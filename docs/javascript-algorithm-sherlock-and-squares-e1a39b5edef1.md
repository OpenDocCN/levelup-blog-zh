# JavaScript 算法:夏洛克和正方形

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-sherlock-and-squares-e1a39b5edef1>

![](img/5be54866b226be32fc112c9866a91dd5.png)

对于今天的算法，我们将创建一个名为`squares`的函数，它将接受两个整数作为输入:`a`和`b`。

想象一下，你有一个叫夏洛克的朋友，每次你们出去玩的时候，你们两个只会给对方出数学题。对于一道数学题，你给他两个数字。一个是起始值，另一个是整数范围的结束值。

他的工作(该函数的目标)是确定包含起始值和结束值的范围内的平方整数的数量。当你把一个数乘以它本身时，结果是一个平方数，或者当你对这个数求平方根时，结果是一个整数。这里有一个例子:

```
let a = 4;
let b = 10;
```

如果我们对从 4 到 10 的每一个数求平方根，我们就会知道哪个数是平方数。

**4** 的平方根是 2。4 是一个平方数。

**5** 的平方根是 2.236。

**6** 的平方根是 2.449。

**7** 的平方根是 2.645。

**8** 的平方根是 2.828。

**9** 的平方根是 3。9 是一个平方数。

**10** 的平方根是 3.162。

在我们的范围内，我们得出两个数字 4 和 9 是平方数的结论。该函数将返回 2。

现在我们把它变成代码。

```
let squareInt = [];
```

我们的`squareInt`变量将保存平方数的个数。

接下来，我们将遍历整个系列。

```
for (let i = a; i <= b; i++){
    let sqrt = Math.sqrt(i);
    if(parseInt(sqrt) === sqrt){
      squareInt.push(i);
    }
}
```

在我们的 for 循环中，我们使用`Math.sqrt()`对每个数字进行平方根运算。

```
let sqrt = Math.sqrt(i);
```

接下来是知道如何检查一个数是否是平方数。如果你对一个数求平方根，结果是一个整数，那么它就是一个平方数。如果平方根的结果是一个浮点数(十进制数)而不是一个整数，那么它就不是一个平方数。我们检查一个数的平方根是否是浮点数的方法之一是使用`parseInt`。

```
if(parseInt(sqrt) === sqrt){
    squareInt.push(i);
}
```

`parseInt`所做的是将输入转换成一个字符串并返回一个整数。我们使用 if 语句来检查数字的平方根是否与整数的平方根相同。如果是，那么它是一个平方数，我们将这个数添加到我们的`squareInt`数组中。

当循环结束时，我们输出平方数。我们通过返回我们的`squareInt`数组的长度来实现。

```
return squareInt.length;
```

代码到此结束。以下是剩余的代码:

```
function squares(a, b) {
  let squareInt = [];

  for (let i = a; i <= b; i++){
    let sqrt = Math.sqrt(i);
    if(parseInt(sqrt) === sqrt){
      squareInt.push(i);
    }
  }

  return squareInt.length;}
```

*请注意，此算法适用于小范围内的数字。如果您想遍历 500 或 1000 个数字，代码可能会(会很慢)或由于超时而无法工作。

如果您觉得这个算法有帮助或有用，请查看我的其他 JavaScript 算法解决方案文章:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-cats-and-a-mouse-fd60fb1811ba) [## JavaScript 算法:猫和老鼠

### 对于今天的算法，我们将创建一个名为 catAndMouse 的函数，它将接受三个整数:x、y 和…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-cats-and-a-mouse-fd60fb1811ba) [](/javascript-algorithm-utopian-tree-b38100fff575) [## JavaScript 算法:乌托邦树

### 对于今天的算法，我们要写一个名为 utopianTree 的函数，它接受一个输入:一个整数 n。

levelup.gitconnected.com](/javascript-algorithm-utopian-tree-b38100fff575) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-find-digits-8c639e9ab81a) [## JavaScript 算法:查找数字

### 对于今天的算法，我们将编写一个名为 findDigits 的函数，它将接受一个整数 n 作为输入。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-find-digits-8c639e9ab81a)