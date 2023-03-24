# Python 算法:重复计数

> 原文：<https://levelup.gitconnected.com/python-algorithm-count-duplicate-27dfd5d80505>

## 计算字符串中重复字符的数量

![](img/334522443bebb0f1cb763a447ca50adf.png)

照片由 [Daryan Shamkhali](https://unsplash.com/@daryan?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

今天我们将编写一个名为`duplicate_count`的 python 函数，它将接受一个字符串`text`作为参数。

给你一个字符串。该函数的目标是返回在一个字符串中多次出现的不区分大小写的不同字符(数字或字母字符)的数量。

这里有一个例子:

```
duplicate_count("abcdeaa") //output: 1
duplicate_count("abcdeaB") // output: 2
duplicate_count("Indivisibilities") // output: 2
```

对于第一个例子，只有字母`a`被多次找到，所以函数将返回`1`。即使字符串中有三个`a`字符，该函数的目标不是计算重复字符的数量，而是计算重复字符的数量。

在第二个例子中，`a`和`b`(记住大小写无关紧要)是唯一有重复的字符。

第三个例子，字母`i`和`s`在函数返回`2`中出现不止一次。

为了开始这个函数，因为小写和大写字符看起来是一样的，我们将通过小写我们的字符串输入使事情变得更容易。

我们将小写字符串赋给一个名为`lowerText`的变量。

```
lowerText = text.lower()
```

接下来，我们将创建一个名为`found`的变量，并为其分配一个空数组。我们将使用这个数组来保存所有重复的字符。

```
found = []
```

当我们遍历字符串输入中的所有字符时，通过使用`count`方法，我们可以检查当前迭代的字符在字符串中是否有重复。只要我们还没有把那个字符添加到我们的`found`数组中，我们就可以把它添加进去。

```
for char in lowerText:        
    if(not(char in found) and lowerText.count(char) > 1):               
        found.append(char)
```

在我们循环完字符串之后，我们的`found`数组现在包含了所有重复的不同字符。我们可以通过返回数组的长度来返回字符数。

```
return len(found)
```

下面是该函数的其余部分:

这是第一篇关于 python 算法的文章，但以后还会有更多。与此同时，如果您想继续学习 JavaScript，可以看看我的 JavaScript 算法解决方案文章:

[](/javascript-algorithm-calculate-sum-of-all-numbers-in-a-jagged-array-94230951d726) [## JavaScript 算法:计算交错数组中所有数字的和

### 创建一个计算交错数组中所有数字之和的函数。

levelup.gitconnected.com](/javascript-algorithm-calculate-sum-of-all-numbers-in-a-jagged-array-94230951d726) [](https://javascript.plainenglish.io/javascript-algorithm-rotate-array-to-the-left-3767dcf60773) [## JavaScript 算法:向左旋转数组

### 编写一个函数，将数组向左旋转一个位置。

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-algorithm-rotate-array-to-the-left-3767dcf60773) [](https://javascript.plainenglish.io/javascript-algorithm-hiding-the-card-number-413801b267e9) [## JavaScript 算法:隐藏卡号

### 编写一个函数，只显示信用卡号的最后四位数字。

javascript.plainenglish.io](https://javascript.plainenglish.io/javascript-algorithm-hiding-the-card-number-413801b267e9)