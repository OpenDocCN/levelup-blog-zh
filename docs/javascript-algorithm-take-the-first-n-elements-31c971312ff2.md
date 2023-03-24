# JavaScript 算法:取前 N 个元素

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-take-the-first-n-elements-31c971312ff2>

## 我们将编写一个函数来返回数组中的前 n 个元素。

![](img/f59b16f11497facdda9733eecd017efb.png)

我们将编写一个名为`take`的函数，它接受一个数组`arr`和一个整数`n`作为输入。

该函数的目标是获取`arr`并只返回数组中的第一个`n`元素。让我们举个例子:

```
let arr = [2, 4, 12, 6, 36];
let n = 4;
```

使用上面的信息，我们只返回数组中的前 4 个元素，只留下`36`。

```
[2, 4, 12, 6]
```

现在，我们已经知道我们在寻找什么，让我们把它变成代码。

有很多方法可以输出数组的特定部分，但是我们将使用一个叫做 `[slice()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)`的[方法。`slice()`所做的是返回数组一部分的浅拷贝。使用 slice 方法，输入开始提取的索引和结束位置。如果不提供结尾，该方法将从提供的索引号一直提取到数组的末尾。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)

我们希望提取并返回数组中从 0 到 n 的一部分。

```
return arr.slice(0, n);
```

我们需要明确声明我们希望提取从 0 开始。对于 slice 方法，如果我们省略一个起始索引，它将默认为零，但是如果我们只写出`.slice(n)`，它将提取数组的一部分，从`n`开始一直到结尾。这不是我们想要的。

这就是我们算法的结尾。以下是剩余的代码:

```
function take(arr, n) {
  return arr.slice(0, n);
}
```

以下是我的一些其他 JavaScript 算法，你可以看看:

[](/javascript-algorithm-pangrams-3e6add10f38f) [## JavaScript 算法:Pangrams

### 对于今天的算法，我们要写一个叫做 pangrams 的函数，它接受一个字符串 s 作为输入。

levelup.gitconnected.com](/javascript-algorithm-pangrams-3e6add10f38f) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-angry-professor-89570ba8d863) [## JavaScript 算法:愤怒的教授

### 对于今天的算法，我们将编写一个名为 angryProfessor 的函数，它将接受两个输入:一个整数…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-angry-professor-89570ba8d863) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-cats-and-a-mouse-fd60fb1811ba) [## JavaScript 算法:猫和老鼠

### 对于今天的算法，我们将创建一个名为 catAndMouse 的函数，它将接受三个整数:x、y 和…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-cats-and-a-mouse-fd60fb1811ba)