# JavaScript 算法:将分钟转换成秒钟

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-convert-minutes-into-seconds-4d4a0d750b6c>

## 这是一个关于如何将分钟转换成秒钟的简单函数。

![](img/e451f780660b1c827ed3246a3e7850f9.png)

照片由[索尼娅·兰福德](https://unsplash.com/@sonjalangford?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

今天，我们将编写一个名为`convert`的函数，它将接受一个整数`minutes`作为参数。

这个函数的目标是将整数或分钟转换成秒并输出结果。这是一个非常简单的算法。我们来看几个例子。

示例:

```
convert(5) → 300
convert(3) → 180
convert(2) → 120
```

对于上面的例子，你把整数输入乘以 60，因为一分钟有 60 秒。因此，该函数将输出这些分钟的秒数。

让我们把这变成代码。

我们可以创建一个名为`seconds`的变量，并将其乘以 60，然后返回该变量。

```
let seconds = minutes * 60;
return seconds;
```

或者我们可以直接返回方程。

```
return minutes * 60;
```

就是这样。下面是该函数的其余部分:

如果您发现这个算法很有帮助，请查看我的其他 JavaScript 算法解决方案文章:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-hex-to-decimal-3400f3742d1e) [## JavaScript 算法:十六进制到十进制

### 写一个将十六进制数转换成十进制数的函数。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-hex-to-decimal-3400f3742d1e) [](https://codeburst.io/javascript-algorithm-chunky-monkey-337991491b24) [## JavaScript 算法:矮胖猴子

### 我们编写一个函数，通过将一个一维数组分割成子数组组来创建一个二维数组…

codeburst.io](https://codeburst.io/javascript-algorithm-chunky-monkey-337991491b24) [](https://codeburst.io/javascript-algorithm-return-largest-numbers-in-arrays-476914b717a7) [## JavaScript 算法:返回数组中最大的数字

### 我们编写了一个函数，从数组参数中返回一个数组，该数组包含每个子数组中最大的数字

codeburst.io](https://codeburst.io/javascript-algorithm-return-largest-numbers-in-arrays-476914b717a7)