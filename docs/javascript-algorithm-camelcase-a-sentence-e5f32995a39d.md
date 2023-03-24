# JavaScript 算法:Camelcase 一句话

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-camelcase-a-sentence-e5f32995a39d>

## 创建一个将文本中每个单词的首字母大写的函数。

![](img/d944a3b146544cff5186d3763bd367d7.png)

沃尔夫冈·哈塞尔曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我们将编写一个名为`capitalizeWords`的函数，它将接受一个字符串`str`作为参数。

该函数的目标是输出相同的字符串，但字符串中的每个单词都要大写或小写。

示例:

```
captializeWords("Create a function that will capitalize the first letter of each word in a text");//output: "Create A Function That Will Capitalize The First Letter Of Each Word In A Text"
```

我们要做的第一件事是创建一个名为`newWord`的变量。这个变量将包含我们新的骆驼大小写字符串。

我们将结合使用`replace()`方法和正则表达式。

```
let newWord = text.replace(/(\b\w)/g, function(str){      
    return str.toUpperCase();    
});
```

我们的正则表达式`/(\b\w)/g`将匹配单词边界中的每个第一个字符。换句话说，它将匹配每个单词的第一个字符。在我们的回调函数中，对于正则表达式匹配的每个字符，我们用一个大写的字符替换它。

在函数将字符串中的每个单词大写后，我们返回新的字符串。

```
return newWord;
```

下面是完整的函数:

如果你觉得这个算法有帮助，看看我的其他 JavaScript 算法解决方案:

[](https://js.plainenglish.io/javascript-algorithm-word-count-d759738c974c) [## JavaScript 算法:字数

### 创建一个函数，返回文本中的字数。

js .平原英语. io](https://js.plainenglish.io/javascript-algorithm-word-count-d759738c974c) [](/javascript-algorithm-find-distinct-elements-3fd163b27e2b) [## JavaScript 算法:寻找不同的元素

### 一个函数，它将输出一个包含来自其输入数组的不同元素的数组。

levelup.gitconnected.com](/javascript-algorithm-find-distinct-elements-3fd163b27e2b) [](https://js.plainenglish.io/javascript-algorithm-find-difference-between-two-arrays-ed8df86c4924) [## JavaScript 算法:找出两个数组之间的差异

### 返回一个数组的函数，该数组的元素在第一个数组中，但不在第二个数组中。

js .平原英语. io](https://js.plainenglish.io/javascript-algorithm-find-difference-between-two-arrays-ed8df86c4924)