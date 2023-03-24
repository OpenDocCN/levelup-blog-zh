# JavaScript 算法:将字符串转换为骆驼大小写

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-convert-string-to-camel-case-9a72da82287f>

## 将破折号和/或下划线分隔的单词转换为骆驼大小写

![](img/b11daf7b513f8f55e9803a627ea5f4a1.png)

照片由 [Aman Kumar](https://unsplash.com/@amankumar98?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我们将编写一个名为`toCamelCase()`的函数，它将接受一个字符串`str`作为参数。

对于这个函数，给你一个字符串，字符串中的单词用破折号`—`或下划线`_`或者两者分开。该函数的目标是删除所有的破折号和/或下划线，并且下划线或破折号后面的第一个单词应该大写。返回的字符串应该类似于输入字符串，但大小写不同。

如果输入是一个空字符串，函数将返回一个空字符串。

示例:

```
toCamelCase("the_stealth_warrior") // returns "theStealthWarrior"
toCamelCase("A-B-C") // returns "ABC"
```

在第一个例子中，你可以看到所有的下划线都被删除了，下划线后面的每个单词都变成了大写。

在第二个例子中，除了破折号，你可以看到同样的情况。

首先，我们创建一个名为`newStr`的变量。这是函数将返回的变量，因为它将包含我们转换后的字符串。

```
let newStr = "";
```

接下来，我们将创建一个 if 语句来检查函数是否有字符串输入。如果没有，我们将返回一个空字符串或`newStr`。

```
if (str) {
 // blah blah
} else {
    return newStr
}
```

在第一个 if 块中，我们将使用`split()`方法在有破折号或下划线的地方分割字符串。得到的数组将进入另一个名为`wordArr`的变量。

```
if (str) {
    let wordArr = str.split(/[-_]/g);
} else {
    return newStr
}
```

我们使用正则表达式`/[-_]/g`作为`split()`方法的参数来捕获字符串中的每个破折号和下划线。

接下来，我们将循环遍历我们的`wordArr`数组，并将字符串中除第一个单词以外的所有单词都大写。我们只关心下划线和/或破折号后面的单词。

```
for (let i in wordArr) {
    if (i > 0) {
        newStr += wordArr[i].charAt(0).toUpperCase() + wordArr[i].slice(1);
    } else {
        newStr += wordArr[i]
    }
}
```

有很多方法可以将一个单词的第一个字母大写，但其中一种方法是使用`charAt()`方法，该方法在索引`0`处输出第一个字符。然后，我们使用`toUpperCase()`方法将该字符大写，并使用`slice()`方法将该字符与字符串中的剩余单词连接起来。

在函数循环完数组之后，我们返回我们的`newStr`变量。

```
return newStr;
```

下面是该函数的其余部分:

如果您发现这个算法很有帮助，请查看我的其他 JavaScript 算法解决方案文章:

[](/javascript-algorithm-find-the-parity-outlier-666e350883ec) [## JavaScript 算法:寻找奇偶异常值

### 找出奇数(或偶数)

levelup.gitconnected.com](/javascript-algorithm-find-the-parity-outlier-666e350883ec) [](https://javascript.plainenglish.io/javascript-algorithm-how-to-split-strings-cd0d6be0b8b1) [## JavaScript 算法:如何拆分字符串

### 将一个字符串拆分成数组中的两个字符。

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-algorithm-how-to-split-strings-cd0d6be0b8b1) [](https://javascript.plainenglish.io/javascript-algorithm-beginner-series-2-clock-ff163cb493ba) [## JavaScript 算法:初学者系列#2 时钟

### 以毫秒为单位返回自午夜以来的时间

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-algorithm-beginner-series-2-clock-ff163cb493ba)