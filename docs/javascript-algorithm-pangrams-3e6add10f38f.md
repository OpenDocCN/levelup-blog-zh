# JavaScript 算法:Pangrams

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-pangrams-3e6add10f38f>

![](img/039dc3269c80b5ea2faa91e849892388.png)

对于今天的算法，我们将编写一个名为`pangrams`的函数，它将接受一个字符串`s`作为输入。

盘符是包含字母表中每个字母的句子。这个函数的目标是确定给定的句子是否是一个 pangram。如果不是盘符，函数将返回`not pangram`。如果是 pangram，函数会返回`pangram`。这里有一个例子:

```
"**The** **quick** **brown** **f**o**x** **j**u**mps** o**v**er the **lazy** **d**o**g**"
```

在上面的例子中，所有粗体字母都是字母表中的所有字母。在这种情况下，该函数将返回`pangram`。

```
"**We** **prom**p**t**l**y** **ju**d**g**e**d** **an**t**iq**ue i**v**ory **b**u**ckl**e**s** **f**or t**h**e pri**z**e"
```

对于上面的字符串，除了 x 之外的所有字母都在那里，所以函数将返回`no pangram`。

让我们把这个翻译成代码。

```
let alphabet = "abcdefghijklmnopqrstuvwxyz";
let regex = /\s/g;
let lowercase = s.toLowerCase().replace(regex, "");
```

我们的`alphabet`变量是一个包含字母表中所有字母的字符串。

我们的`regex`变量是我们的正则表达式。当我们第一次在我们的[came case 算法](https://codeburst.io/javascript-algorithm-camelcase-4df119b6216e)中使用正则表达式时，我们讨论了它的基础。我们使用的模式是`\s`，它寻找字符串中的所有空格。

在我们的`lowercase`变量中，为了我们如何检查 pangrams 和方便起见，我们想要小写所有的字母，所以我们可以只关注小写字母。最重要的是，我们希望删除字符串中的所有空格。使用`replace()`方法，在正则表达式的帮助下，我们可以通过用 nothing 替换所有出现的空白来删除所有空白，`""`。

接下来，我们使用 for 循环遍历字母表字符串。

```
for(let i = 0; i < alphabet.length; i++){
    if(lowercase.indexOf(alphabet[i]) === -1){
      return "not pangram";
    }
}
```

在 if 语句中，我们使用`indexOf()`在`lowercase`字符串中查找字母表中每个字母的索引。如果`indexOf`找不到一个字母，循环结束，返回语句`not pangram`。如果 for 循环进行到最后，这意味着`indexOf`在我们的字符串中找到了字母表中的所有字母。此时，函数可以返回`pangram`。

```
return "pangram";
```

这就结束了我们的功能。下面是剩余的代码:

```
function pangrams(s) {
   let alphabet = "abcdefghijklmnopqrstuvwxyz";
   let regex = /\s/g;
   let lowercase = s.toLowerCase().replace(regex, "");

   for(let i = 0; i < alphabet.length; i++){
    if(lowercase.indexOf(alphabet[i]) === -1){
      return "not pangram";
    }
   }

  return "pangram";
}
```

以下是我的一些其他 JavaScript 算法，你可以看看:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-mars-exploration-b6bcc9f3d71e) [## JavaScript 算法:火星探索

### 对于今天的算法，我们将编写一个名为 marsExploration 的函数，它将接受一个字符串 s 作为输入。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-mars-exploration-b6bcc9f3d71e) [](/javascript-algorithm-utopian-tree-b38100fff575) [## JavaScript 算法:乌托邦树

### 对于今天的算法，我们要写一个名为 utopianTree 的函数，它接受一个输入:一个整数 n。

levelup.gitconnected.com](/javascript-algorithm-utopian-tree-b38100fff575) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-sock-merchant-de9ffa754dfc) [## JavaScript 算法:袜子商人

### 对于今天的算法，我们将编写一个名为 sockMerchantthat 的函数，它将接受两个输入，一个整数 n…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-sock-merchant-de9ffa754dfc)