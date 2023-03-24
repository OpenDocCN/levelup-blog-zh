# JavaScript 算法:将 ASCII 转换成字符串字符

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-convert-ascii-into-string-characters-4eb80639698>

## 创建一个将包含 ASCII 码的数组转换成字符串的函数。

![](img/7bef7e034b7cfb4ef936889d649c3c5a.png)

照片由[阿玛多·洛雷罗](https://unsplash.com/@amadorloureiroblanco?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在我的[上一篇文章](https://javascript.plainenglish.io/javascript-algorithm-convert-string-characters-into-ascii-bb53ae928331)中，我们写了一个函数，将字符串中的每个字符转换成 Unicode 或 ASCII 码值的数组。对于这个函数，我们将反过来。

我们将编写一个名为`codesToString`的函数，它将接受一个数组`arr`作为参数。

在这个函数中，给你一个包含 ASCII 码值的数组。目标是将 ASCII 码翻译成字符串字符并返回该字符串。

示例:

```
codesToString([73,32,108,105,107,101,32,74,97,118,97,83,99,114,105,112,116])//output: "I like JavaScript"
```

当您将所有的 ASCII 值转换成对应的字符串字符时，数组会拼出`I like JavaScript`。

这个函数很短，因为我们只写一行。为了从 Unicode 中获取一个字符串，我们将使用 JavaScript 方法`String.fromCharCode()`。

在括号内，我们输入我们的数字序列。因为我们有一个数组，所以我们将使用 spread 语法将数组包含在括号中。这将查看数组中的所有值并输出我们的字符串。

```
String.fromCharCode(...arr);
```

仅此而已。

下面是完整的函数:

如果你觉得这个算法有帮助，看看我的其他 JavaScript 算法解决方案:

[](/javascript-algorithm-where-do-i-belong-1dfc397fc887) [## JavaScript 算法:我属于哪里

### 如果我们把一个数字排序后放入一个数组，这个数字的索引是什么？

levelup.gitconnected.com](/javascript-algorithm-where-do-i-belong-1dfc397fc887) [](/javascript-algorithm-sum-of-all-digits-in-a-number-f1882e323ab1) [## JavaScript 算法:一个数中所有数字的总和

### 写一个函数，计算一个数中所有数字的和。

levelup.gitconnected.com](/javascript-algorithm-sum-of-all-digits-in-a-number-f1882e323ab1) [](https://javascript.plainenglish.io/javascript-algorithm-convert-string-characters-into-ascii-bb53ae928331) [## JavaScript 算法:将字符串转换成 ASCII

### 创建一个函数，该函数将返回一个包含字符串中每个字符的 ASCII 码的数组。

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-algorithm-convert-string-characters-into-ascii-bb53ae928331)