# JavaScript 中合并两个数组的不同方式及其利弊

> 原文：<https://levelup.gitconnected.com/different-ways-of-merging-2-arrays-in-javascript-and-its-benefits-and-trade-offs-77146875b29f>

![O](img/709408cfc9acccb3f7e693966c5f5992.png)  O 编程中常见的任务之一就是需要将两个或多个数组合并成一个数组。在 JavaScript 中有几种方法可以实现这一点，每种方法都有自己的权衡和考虑。在本文中，我们将探索 JavaScript 中合并数组的各种方法，并研究它们的优点和缺点，以便您做出明智的决定。

这只是众多关于 JavaScript 文章中的一篇。欢迎关注我们，获取更多关于 JavaScript 和软件开发的精彩内容。我们尝试一周发布多次。请确保不要错过我们的任何精彩内容。

![](img/28308f2d2bc383f97aa5fa52f9b60708.png)

兰斯·格兰达尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在 JavaScript 中有几种方法可以合并两个数组。这里有几个
选项:

## concat()方法

```
const array1 = [1, 2, 3];
const array2 = [4, 5, 6];
const mergedArray = array1.concat(array2);
// mergedArray is [1, 2, 3, 4, 5, 6]
```

好处:

*   简单明了地使用。
*   不修改原始数组。
*   在新的浏览器上，它对小型和大型数组都很快。

权衡:

*   在较旧的浏览器上，对于大数组来说可能效率较低，因为它创建了一个新数组来保存合并的元素。

## 扩展运算符(…)

```
const array1 = [1, 2, 3];
const array2 = [4, 5, 6];
const mergedArray = […array1, …array2];
// mergedArray is [1, 2, 3, 4, 5, 6]
```

好处:

*   简洁明了的语法。
*   不修改原始数组。

权衡:

*   对于大型数组可能效率较低，因为它创建了一个新数组来保存合并的元素。

## Array.prototype.push.apply()方法

```
const array1 = [1, 2, 3];
const array2 = [4, 5, 6];
const mergedArray = [].push.apply(array1, array2);
// mergedArray is [1, 2, 3, 4, 5, 6]
```

好处:

*   可以就地修改原始数组，从而节省内存。

权衡:

*   对于某些人来说，语法可能不太熟悉，也很难读懂。
*   修改原始数组，这在某些情况下可能是不需要的。

## forEach()方法

```
const array1 = [1, 2, 3];
const array2 = [4, 5, 6];
const mergedArray = [];
array1.forEach(element => mergedArray.push(element));
array2.forEach(element => mergedArray.push(element));
// mergedArray is [1, 2, 3, 4, 5, 6]
```

好处:

*   可以就地修改原始数组，从而节省内存。

权衡:

*   语法更加冗长，对某些人来说可能更难读懂。
*   修改原始数组，这在某些情况下可能是不需要的。

这些方法各有利弊，因此合并两个阵列的最佳方法将取决于您的特定需要和要求。

![](img/5c7fdb823e2c7f4190f716ff6bed224c.png)

你上一次需要合并两个数组是什么时候？你的用例是什么，你决定采用哪种方法，为什么？读完这篇文章后，你会改变你的决定吗？我们还发表了一篇[文章，专门研究了 concat()方法和用于合并数组的 spread 操作符](https://pandaquests.medium.com/merging-arrays-with-concat-vs-spread-operator-in-javascript-67a2ff89cc31)。你可能想去看看。

如果你认为这篇文章是有帮助的，一定要关注并订阅我们的文章。我们每周多次发布关于 JavaScript 和软件开发的有趣文章。我们为你将复杂的话题分解成小而易理解的部分。所以，你不想错过任何一个。回头见！

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰更多内容请查看[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)