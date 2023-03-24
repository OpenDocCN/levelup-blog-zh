# JavaScript 算法:两个字符串

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-two-strings-f85c9d3117fc>

![](img/8d7e72aeb1766b703e66ca8def60e732.png)

对于今天的算法，我们将编写一个名为`twoStrings`的函数，它将接受两个字符串`s1`和`s2`作为输入。

给定两个字符串，函数的目标是确定这两个字符串是否共享一个公共子字符串。子字符串可以少至一个字符。如果两个字符串至少有一个相同的字符，函数将返回`YES`。如果两个字符串不共享一个公共子字符串，函数将返回`NO`。

这里有一个例子:

```
let s1 = "**ap**ple";
let s2 = "**pa**rk";
```

如果我们观察这两个字符串，特别是粗体字符，我们会发现这两个字符串有共同的子字符串，`a`和`p`。该函数将返回`YES`。

对于这个例子:

```
let s1 = "pork";
let s2 = "wait";
```

对于上面的两个字符串，它们都没有任何共同的子字符串，所以函数将返回`NO`。

让我们把这变成代码:

```
let shortStr;
let longStr;
```

我们创建两个变量，`shortStr`和`longStr`。这两个变量的目的是将两个字符串输入中较长的一个分配给`longStr`变量，将较短的一个分配给`shortStr`变量。我们如何知道哪根弦比另一根短或长？

```
if(s1.length < s2.length){
    shortStr = s1;
    longStr = s2;
}else{ 
    shortStr = s2;
    longStr = s1;
}
```

在我们的 if 语句中，我们检查`s1`是否比`s2`短。如果是，`s1`被分配给`shortStr`变量，`s2`被分配给`longStr`变量。如果不是，我们切换变量分配，这样`s2`被分配给`shortStr`变量，而`s1`被分配给`longStr`变量。如果两个字符串恰好长度相同，则默认使用 else 块中的变量赋值。

我们这样做是为了循环遍历最短的字符串，看看在`longStr`变量中是否存在任何字符。它节省时间。我肯定有更有效的方法来做这件事，但是这种 ***一种*** 的方法。

```
for(let i = 0; i < shortStr.length; i++){
    if(longStr.indexOf(shortStr[i]) !== -1){
      return "YES";
    }
 }
```

在我们的 for 循环中，我们遍历我们的`shortStr`变量的字符。我们使用`indexOf()`方法来查看`shortStr`中的一个字符是否存在于`longStr`字符串中。请记住，两个字符串需要有一个共同的子字符串，该子字符串可以只有一个字符。当我们找到一个匹配时，我们结束循环并返回`YES`。

如果循环结束，这意味着函数没有找到任何公共子串，所以它返回`NO`。

```
return "NO";
```

这就结束了我们的功能。以下是剩余的代码:

```
function twoStrings(s1, s2) {
  let shortStr;
  let longStr;

  if(s1.length < s2.length){
    shortStr = s1;
    longStr = s2;
  }else{
    shortStr = s2;
    longStr = s1;
  }

  for(let i = 0; i < shortStr.length; i++){
    if(longStr.indexOf(shortStr[i]) !== -1){
      return "YES"
    }
  }

  return "NO"
}
```

如果你觉得这个算法有帮助，可以看看我最近的其他 JavaScript 算法解决方案:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-alternating-characters-617eb0764cbc) [## JavaScript 算法:交替字符

### 对于今天的算法，我们将编写一个名为 alternatingCharacters 的函数，它将一个字符串 s 作为…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-alternating-characters-617eb0764cbc) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-viral-advertising-168a872cb557) [## JavaScript 算法:病毒式广告

### 对于今天的算法，我们要写一个叫做 viralAdvertising 的函数，它接受一个整数 n 作为…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-viral-advertising-168a872cb557) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-electronics-shop-240a4fcb0e85) [## JavaScript 算法:电子商店

### 对于今天的算法，我们将编写一个名为 getMoneySpent 的函数，它将接受三个输入:两个数组…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-electronics-shop-240a4fcb0e85)