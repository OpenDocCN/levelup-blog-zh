# JavaScript 算法:搜索和替换

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-search-and-replace-6895e17ccfd7>

## 我们编写了一个函数，可以对字符串执行简单的搜索和替换操作。

![](img/b40106146445d80ce2a30faf2d646131.png)

我们将编写一个名为`myReplace`的函数，它将接受三个字符串(`str`、`before`和`after`)作为参数。

该函数的目标是获取第二个参数(`before`)并在给定的字符串(`str`)中搜索术语，然后用第三个参数(`replace`)替换它。该函数将返回结果字符串。

替换字符串应该保留它所替换的字符串中第一个字符的大小写。

示例:

```
let str = "Let us go to the Store";
let before = "Store";
let after = "mall";// output: "Let us go to the Mall"
```

在上面的字符串中，由于`"Store"`是大写的，当用`"mall"`替换那个单词时，`after`字符串也要大写。

```
let str = "This has a spellngi error";
let before = "spellngi";
let after = "spelling";// output: "This has a spelling error"
```

让我们开始函数。

我们首先创建两个变量:

如果`capitalizedBefore`变量是大写的，那么它就是`before`字符串。这同样适用于`after`字符串的`capitalizedAfter`变量。

我们将字符串的第一个字符大写，然后使用`substring()`方法将该字符连接到剩余的字符串。

接下来，我们编写 if 语句。

```
if (before === capitalizedBefore) {
    return str.replace(before, capitalizedAfter);
} else {
    return str.replace(before, after.toLowerCase());
}
```

我们创建这两个变量，这样如果`before`字符串是大写的，我们可以保留第一个字母 can 的大小写并用`capitalizedAfter`替换它。

if 语句检查是否是这种情况，如果是，用`capitalizedAfter`变量替换`before`字符串。我们返回字符串。

如果不是这种情况，那么我们简单地用`after`字符串替换`before`字符串并返回它。我们将`after`字符串小写，以防`after`字符串最初是大写的。

我们的代码到此结束。下面是该函数的其余部分:

如果你觉得这个算法有帮助，看看我的其他 JavaScript 算法解决方案:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-pig-latin-82402739bc3f) [## JavaScript 算法:猪拉丁

### 我们写了一个函数，将一个单词转换成拉丁文。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-pig-latin-82402739bc3f) [](https://medium.com/@endubueze00/javascript-algorithm-seek-and-destroy-36888783f35f) [## JavaScript 算法:寻找和破坏

### 我们编写了一个函数，它将从一个数组中删除与所提供的参数具有相同值的所有元素。

medium.com](https://medium.com/@endubueze00/javascript-algorithm-seek-and-destroy-36888783f35f) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-wherefore-art-thou-1e6eb6c10ba2) [## JavaScript 算法:你是为什么

### 我们编写一个函数，它返回一个数组中的所有对象，该数组包含来自源的所有名称/值对…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-wherefore-art-thou-1e6eb6c10ba2)