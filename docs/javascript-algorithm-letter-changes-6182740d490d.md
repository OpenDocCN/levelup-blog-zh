# JavaScript 算法:字母变化

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-letter-changes-6182740d490d>

![](img/82d543e0656b517f11f4c9fd895c7dbe.png)

对于今天的算法，我们将编写一个名为`LetterChanges`的函数，它将把一个字符串`str`作为输入。

给你一个字符串，对于给定的每个字符串，你可以通过将字母表中的每个字母替换为后面的字母来修改它。您只需更改字母字符。替换掉所有必要的字母后，你就可以从这个新创建的字符串中提取所有的元音，并把它们大写。该函数将在所有更改后返回字符串。

让我们举个例子:

```
let str = "hello*3";
```

对于我们的输入字符串，我们将所有按字母顺序排列的字符(不包括`*3`)更改为字母表中位于其后的字母，我们的字符串变成:

```
ifmmp*3
```

然后我们回去，找到所有的元音，如果有的话，在我们的新字符串和大写他们。最后，我们的功能将返回:

```
Ifmmp*3
```

现在我们开始把它变成代码。

首先，我们创建一个名为`strArray`的变量。使用这个变量，我们将字符串输入转换成一个数组。在分割字符以创建数组之前，我们首先使用`toLowerCase()`方法将字符串小写。

```
let strArray = str.toLowerCase().split("");
```

接下来，我们创建另一个名为`letterChange`的变量。这个变量保存着我们的字符串，在字母表中把每个字母变成它后面的字母。为了迭代数组，我们使用数组`map()`方法。

```
let letterChange = strArray.map(function(value, index, array){
    if(str.charCodeAt(index) < 97 || str.charCodeAt(index) > 122){
      return value
    }else{
      return String.fromCharCode(str.charCodeAt(index)+1)
    }
});
```

map 方法允许我们迭代数组，并对数组中的每个元素使用回调函数。

```
if(str.charCodeAt(index) < 97 || str.charCodeAt(index) > 122){
  return value
}else{
  return String.fromCharCode(str.charCodeAt(index)+1)
}
```

在回调函数中，我们使用`String.fromCharCode()`和`charCodeAt()`的组合来获取每个字符的 Unicode 值以及与特定 Unicode 值相关联的字符。我们只想更改 a-z 字母(不是 A-Z，大写字母有不同的 Unicode 值)。任何不能转换成我们单独留下的字符的 Unicode 值。如果我们查看一个 ASCII 表，这些非字母(以及大写字母)字符的 Unicode 值小于 97 或大于 122。

要得到字母表中的下一个字母，我们首先要得到当前字符的 Unicode 值，然后加上 1。我们检索当前字符的索引，以便在`charCodeAt()`方法中使用。`String.fromCharCode()`将为我们提供与该 Unicode 值相关联的字符。

现在我们有了修改后的字符串(仍然是数组形式),但是我们还没有完成。下一件事是检查字符串中是否有元音，这样我们就可以将它们大写。

我们再次使用 map 方法来迭代和修改数组变量`letterChange`,但是这次要包含大写的元音字母。

```
letterChange = letterChange.map(function(value, index, array){
    if(/[aeiou]/.test(value)){
      return value.toUpperCase();
    }else{
      return value;
    }
});
```

在我们的 map 方法中，我们使用了一个来自正则表达式对象的方法，名为`test()`。`test()`所做的是搜索正则表达式和输入字符串之间的匹配，如果字符串中存在正则表达式模式，该方法将输出 true。如果不是，则为假。

我们使用我们的正则表达式模式`/[aeiou]/`来查看每个字符是否是元音。如果是，我们简单地使用`toUpperCase()`方法来利用它。如果字符根本不是元音或字母，则按原样返回字符。

现在我们完成了，我们可以返回我们的`letterChange`变量了，但是首先，我们连接它，这样我们可以把它作为一个字符串返回。

```
return letterChange.join("");
```

这就是我们的代码。可能有些部分需要改进，但这是解决这个问题的一种方法。以下是剩余的代码:

```
function LetterChanges(str) { 

  let strArray = str.toLowerCase().split("");
  let letterChange = strArray.map(function(value, index, array){
    if(str.charCodeAt(index) < 97 || str.charCodeAt(index) > 122){
      return value
    }else{
      return String.fromCharCode(str.charCodeAt(index)+1)
    }
  });

  letterChange = letterChange.map(function(value, index, array){
    if(/[aeiou]/.test(value)){
      return value.toUpperCase();
    }else{
      return value;
    }
  });

  return letterChange.join(""); 
}
```

如果你觉得这个算法有帮助，可以看看我最近的其他 JavaScript 算法解决方案:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-alternating-characters-617eb0764cbc) [## JavaScript 算法:交替字符

### 对于今天的算法，我们将编写一个名为 alternatingCharacters 的函数，它将一个字符串 s 作为…

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-alternating-characters-617eb0764cbc) [](/javascript-algorithm-introduction-10a316c91b52) [## JavaScript 算法:简介

### 今天的算法很简单，但是我们要写一个叫做 intro 的函数，它将接受一个…

levelup.gitconnected.com](/javascript-algorithm-introduction-10a316c91b52) [](/javascript-algorithm-beautiful-days-at-the-movies-81561dd03212) [## JavaScript 算法:看电影的美好时光

### 对于今天的算法，我们要写一个名为 beautifulDays 的函数，它接受三个整数作为…

levelup.gitconnected.com](/javascript-algorithm-beautiful-days-at-the-movies-81561dd03212)