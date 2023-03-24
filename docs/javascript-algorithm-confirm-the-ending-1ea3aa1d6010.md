# JavaScript 算法:确认结局

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-confirm-the-ending-1ea3aa1d6010>

## 我们编写一个函数，如果字符串的末尾包含目标子字符串，它将返回 true 或 false

![](img/be4b1fa5597ac1f059c902f2c05d03fd.png)

我们将编写一个名为`confirmEnding`的函数，它将接受两个字符串(`str`和`target`)作为参数。

该函数的目标是确定`str`是否以给定的`target`字符串结束。如果输入是一个多单词的字符串，那么只关注字符串中的最后一个单词。

```
let str = "Open sesame";
let target = "sam";
// returns false
```

如果我们查看字符串`"Open sesame"`中的最后一个单词，子字符串`"sam"`确实存在于字符串`"sesame"`中，但它不在末尾。字符`'e'`阻止函数返回 true。如果输入了以下子字符串:`"esame"`、`"same"`、`"ame"`、`"me"`或`"e"`，该函数将返回`true`。

如果字符串输入只是一个单词，那么我们将以同样的方式搜索目标子字符串。

我们可以使用`.endsWith()`来解决这个算法，但是我们将不用它来编写这个函数。

首先，我们将把字符串分割成一个单词数组，并将它赋给一个名为`split`的变量。

```
let split = str.split(" ");
```

我们写一个 if 语句，看看我们的分割数组长度是否大于 1。如果它大于 1，这意味着我们的字符串输入是一个多单词的字符串。

```
if(split.length > 1){
    let lastWord = split[split.length - 1];
    return lastWord.indexOf(target) >= 0;
}
```

在条件中，我们使用数组括号符号来检索字符串中的最后一个单词，并将其赋给一个名为`lastWord`的变量。

```
let lastWord = split[split.length - 1];
```

接下来，我们将使用`lastIndexOf()`和`substring()`方法的组合来确定字符串是否以目标子字符串结尾。

```
return lastWord.substring(lastWord.lastIndexOf(target)) === target;
```

我们使用 lastIndexOf 来检索目标字符串的索引。我们使用`lastIndexOf()`而不是`indexOf()`,因为我们想从字符串的末尾开始搜索。我们获取该索引并在 substring 方法中使用它。

substring 方法将返回从索引开始一直到单词末尾的字符串部分。如果该子串匹配我们的`target`子串，函数将返回`true`。如果不匹配，条件将返回`false`。

如果我们的输入字符串只是一个单词，我们重复最后一个 return 语句，例外的是我们直接从参数中获取字符串，而不处理它。

```
else{
    return str.substring(str.lastIndexOf(target)) === target;
}
```

下面是该函数的其余部分:

如果你觉得这个算法有帮助，看看我的其他 JavaScript 算法解决方案:

[](/javascript-algorithm-dragon-survival-a06404e65595) [## JavaScript 算法:龙族生存

### 我们写了一个函数，它将决定我们的童话英雄是否有足够的子弹击落每一条火龙

levelup.gitconnected.com](/javascript-algorithm-dragon-survival-a06404e65595) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-soccer-goal-totals-93223792b67f) [## JavaScript 算法:足球进球总数

### 我们将解决并研究在 JavaScript 中，在一个函数中添加多个数字总和的许多方法。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-soccer-goal-totals-93223792b67f) [](https://codeburst.io/javascript-algorithm-maximum-toys-7a337e9a5b1f) [## JavaScript 算法:最大玩具

### 对于今天的算法，我们要写一个函数来找出在不超出预算的情况下我们可以买多少玩具。

codeburst.io](https://codeburst.io/javascript-algorithm-maximum-toys-7a337e9a5b1f)