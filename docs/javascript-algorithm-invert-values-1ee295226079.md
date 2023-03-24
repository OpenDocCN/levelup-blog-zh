# JavaScript 算法:反转值

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-invert-values-1ee295226079>

## 给定一组数字，返回每个数字的加法逆运算。

![](img/511f381b3e3442938bf2a51845644000.png)

照片由[托尼·汉德](https://unsplash.com/@mr_t55?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我们将编写一个名为`invert`的函数，它将接受一组数字`array`作为参数。

给你一个包含正值和负值的数组。该函数的目标是返回每个数字的倒数。换句话说，把所有负值都变成正值，把所有正值都变成负值。

示例:

```
invert([-17,-6,-15,0,-20,-77,-47])
// output: *[17, 6, 15, -0, 20, 77, 47]*
```

从上面的例子中，你会看到在输出数组中所有的负值都是正的，所有的负值都是正的。同样在输出数组中，您会看到一些奇怪的东西。零是负数。

即使是零也需要有一个负号。正负零是一个东西。我要详细说明这是如何实现的吗？不。除了告诉你它的存在之外，我不知道其他任何事情。

但是函数非常简单。

首先，我们将创建一个名为`invert`的数组。这个数组将包含所有的反转值。

```
let invert = [];
```

接下来，我们循环通过`array`:

```
for (let i = 0; i < array.length; i++) {
    if (array[i] > 0) {
        invert.push(array[i] - (array[i] * 2));
    } if (array[i] < 0) {
        invert.push(Math.abs(array[i]));
    } if (array[i] == 0) {
        invert.push(-0);
    }
}
```

从第一个 if 语句开始，检查值是否大于零。如果是的话，我们用正数乘以 2 把它转换成负数。

```
if (array[i] > 0) {
    invert.push(array[i] - (array[i] * 2));
}
```

在下一个 if 语句中，我们检查数字是否是负数。因为这个更容易转换成正数，所以我们用`Math.abs`。

```
if (array[i] < 0) {
    invert.push(Math.abs(array[i]));
}
```

还记得我上面提到的那个负零吗？最后一个 if 语句只是将所有的零变成负零。

```
if (array[i] == 0) {
    invert.push(-0);
}
```

在将所有的数字改变并添加到`invert`数组后，我们返回它。

```
return invert;
```

仅此而已。

下面是完整的函数:

如果您发现这个算法很有帮助，请查看我的其他 JavaScript 算法解决方案文章:

[](https://javascript.plainenglish.io/javascript-algorithm-beginner-series-2-clock-ff163cb493ba) [## JavaScript 算法:初学者系列#2 时钟

### 以毫秒为单位返回自午夜以来的时间

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-algorithm-beginner-series-2-clock-ff163cb493ba) [](/javascript-algorithm-find-the-parity-outlier-666e350883ec) [## JavaScript 算法:寻找奇偶异常值

### 找出奇数(或偶数)

levelup.gitconnected.com](/javascript-algorithm-find-the-parity-outlier-666e350883ec) [](https://javascript.plainenglish.io/javascript-algorithm-detect-pangram-9d57ca713d0d) [## JavaScript 算法:检测 Pangram

### 盘符是一个包含字母表中每个字母至少一次的句子。

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-algorithm-detect-pangram-9d57ca713d0d)