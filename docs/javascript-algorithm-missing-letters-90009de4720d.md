# JavaScript 算法:缺少字母

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-missing-letters-90009de4720d>

## 我们编写了一个函数，它将返回按字母顺序排列的字母序列中缺失的字母。

![](img/0541d8e339b96c7c75de3d18b97d2222.png)

我们将编写一个名为`fearNotLetter`的函数，它将接受一个字符串(`str`)。

给你一个字符串，它包含一系列按字母顺序排列的字母。该函数的目标是在字母序列中找到丢失的字母并返回它。如果所有字母都存在，返回`undefined`。

示例:

```
let str "fghjklmno";// output: i
```

在我们上面的例子中，所有的字母都是按字母顺序排列的，但是只有字母`i`在这个序列中丢失了。该函数将返回`i`。

我们来写函数吧。

我们创造了两个变量。

```
let alphabet = "abcdefghijklmnopqrstuvwxyz";
let startingPoint = alphabet.indexOf(str[0]);
```

`alphabet`变量是一个包含英语字母表中所有字符的字符串。

`startingPoint`变量保存了`alphabet`变量中`str`第一个字符的索引。

接下来，我们遍历`str`中的所有角色。

```
for (let i = 0; i < str.length + 1; i++) {
    if (str[i] !== alphabet[startingPoint + i]) {
        return alphabet[startingPoint + i];
    }
}
```

在 if 语句中，我们使用`startingPoint + i`在`alphabet`中定位来自`str`的当前字符。我们扫描`str`中的所有字符，如果当前字符与`alphabet`中的字符不匹配，我们返回缺失的字符。

如果循环结束，这意味着没有丢失字符，所以我们返回`undefined`。

```
return undefined;
```

我们的代码到此结束。下面是该函数的其余部分:

如果您发现这个算法很有帮助，请查看我的其他 JavaScript 算法解决方案文章:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-repeat-a-string-repeat-a-string-696dbc73882b) [## JavaScript 算法:重复一个字符串

### 我们编写了一个函数，它将返回一个字符串 x 次，而不使用循环或 repeat 方法。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-repeat-a-string-repeat-a-string-696dbc73882b) [](https://medium.com/swlh/javascript-algorithm-stand-in-line-92e1e21e2f52) [## JavaScript 算法:排队

### 我们将编写一个基于计算机科学概念的函数，称为队列，在这里我们添加和删除项目到…

medium.com](https://medium.com/swlh/javascript-algorithm-stand-in-line-92e1e21e2f52) [](/javascript-algorithm-two-strings-f85c9d3117fc) [## JavaScript 算法:两个字符串

### 对于今天的算法，我们要写一个名为 twoStrings 的函数，它接受两个字符串，s1 和 s2 作为…

levelup.gitconnected.com](/javascript-algorithm-two-strings-f85c9d3117fc)