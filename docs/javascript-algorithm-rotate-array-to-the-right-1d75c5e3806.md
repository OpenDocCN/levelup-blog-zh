# JavaScript 算法:向右旋转数组

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-rotate-array-to-the-right-1d75c5e3806>

## 编写一个函数，将一个数组向右旋转一个位置。

![](img/e9c8a0d532d273c05b4c01964863334a.png)

照片由[尼克·费因斯](https://unsplash.com/@jannerboy62?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在[之前的文章](https://medium.com/javascript-in-plain-english/javascript-algorithm-rotate-array-to-the-left-3767dcf60773)中，我们写了一个将数组向左旋转一个位置的函数。对于今天的函数，我们将反其道而行之，将其向右旋转一个位置。

我们将要编写的函数叫做`rotateRight`，它将接受一个数组`arr`作为参数。

```
let arr = [2, 4, 6, 8];
rotateRight(arr);// output [8, 2, 4, 6]
```

当您查看输出时，到程序结束时，每个条目的输入数组索引应该是它在输入数组中的索引+ 1。第一个元素变成第二个元素，第二个变成第三个，第三个变成第四个，第四个送到数组的开头。

因为我们要将数组向右旋转，所以我们需要做的就是获取数组中的最后一个元素，并将其发送到开头或使其成为第一个元素。为了获得数组中的最后一项，我们将使用`pop()`方法。此方法从数组中移除最后一个元素并返回该元素。我们还想在去掉元素的时候改变数组，这也是这个方法要做的。

我们将最后一个元素赋给一个名为`last`的变量。

```
let last = arr.pop();
```

接下来，我们将把最后一个元素重新定位为数组中的第一项。我们不能使用`push()`方法，因为它会把`last`送回原来的位置。相反，我们将使用`unshift()`方法。

`unshift()`方法的作用是向数组的开头添加一个或多个元素，并返回数组的新长度。

```
arr.unshift(last);
```

现在我们向右旋转了数组，我们返回数组。

```
return arr;
```

下面是该函数的其余部分:

如果您发现这个算法很有帮助，请查看我的其他 JavaScript 算法解决方案文章:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-rotate-array-to-the-left-3767dcf60773) [## JavaScript 算法:向左旋转数组

### 编写一个函数，将数组向左旋转一个位置。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-rotate-array-to-the-left-3767dcf60773) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-find-the-perimeter-of-a-rectangle-96ecb7ab0ea2) [## JavaScript 算法:求矩形的周长

### 创建一个寻找矩形周长的函数。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-find-the-perimeter-of-a-rectangle-96ecb7ab0ea2) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-hex-to-decimal-3400f3742d1e) [## JavaScript 算法:十六进制到十进制

### 写一个将十六进制数转换成十进制数的函数。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-hex-to-decimal-3400f3742d1e)