# ES6 深度探讨:可迭代程序和迭代器

> 原文：<https://levelup.gitconnected.com/es6-deep-dive-iterables-iterators-fee729db692f>

![](img/8e29ce28a9095b9934d95d1431d816d9.png)

照片由[杰瑞米·毕肖普](https://unsplash.com/@jeremybishop?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# JavaScript ES6 中的数据结构循环

今天，我将仔细看看 ECMAScript 标准第 6 版(通常称为 ES6)中导航数据结构的新方法。在 2015 年 JavaScript 中增加的所有升级和语法糖中，现在也支持迭代器和可迭代对象。这些不仅在迭代数组和字符串时允许更少的代码，而且支持新的集合，比如映射和集合，以及为它们创建定制对象和迭代器。你可以认为这些类似于(但不相同于)类固醇上 for-in 循环中发生的循环。

好的，你可能想知道，迭代器和可迭代对象是什么？一个**可迭代的**是一个可以迭代的顺序数据结构。Iterables 也可以有自己的访问方法，该方法定义了它的**迭代器**。这使得 iterables 可以公开访问。迭代器就像一个指针，以特定的方式遍历集合。正如我们稍后将要讨论的，ES6 中的这些新的自定义迭代器可以循环遍历不仅仅是一个简单的数组。

这是一大堆术语，我将用几分钟的时间用一个例子来解释。但是对于初学者来说，让我们看看 for-of 循环中 ES6 迭代器的默认用法。

## for-of 循环

在 ES5 和之前的版本中，我们习惯于看到 for 和 while 循环来迭代数组或字符串。现在，有了 ES6，我们可以跳过一个数组或一个字符串，而不必为这些循环设置参数。默认情况下，for-of 循环知道如何顺序迭代这些数据类型:

在这些例子中，数组和字符串现在都是*可迭代的*数据结构，当我们使用 for-of 循环迭代集合时，我们为*迭代器*声明一个变量名，以使用 *—* ，如`x`或`char`。

## 添加地图和集合

除了快速导航数组和字符串之外，for-of 循环还可以用在地图和集合上——这两者现在在 ES6 中都可以使用。两种数据结构都可以使用`new`关键字初始化(遵循伪经典实例化模式)。

除了它们的键值对是有序的之外，映射与标准对象没有太大区别。集合更像数组，即有序值的集合，但是每个值在一个集合中只能存在一次。在这篇文章中，我不会过多地讨论地图和集合，但是会展示一个在地图上使用 for-of 循环的快速示例:

## 迭代还是…枚举？

你可能已经注意到对象没有出现在这里，尽管我在开始的时候提到了 iterable 对象。原因是现成的 ***JavaScript 对象不是可迭代的数据结构，而是可枚举的***——它们不像数组、字符串以及现在的映射和集合那样具有内部顺序。对象包含一个无序的键值对集合。然而，我们仍然可以使用 for-in 循环来枚举对象的键和/或值。但是，正如我们将看到的，我们也可以在 ES6 中创建定制的可迭代对象和迭代器。

## 符号迭代器

虽然 for-of 循环默认情况下可以访问数组、字符串、集合和映射，但是 ES6 也允许使用`Symbol.iterator`定制可迭代项和迭代器。符号是 ES6 中新的原始数据类型。它为任何规模和复杂度的集合定义了迭代器和迭代过程。它像一个对象上的方法一样工作，因此，`Symbol.iterator`作为一个键存储在一个定制的可迭代对象上。它还可以用来定义在提供的 JavaScript 数据结构上使用的迭代模式。`Symbol.iterator`也是在幕后发生的代码，使 for-of 循环默认地为上述数据结构工作。

为了简单起见，并感受一下这个符号是如何工作的，让我们构建一个迭代器，它将反向遍历数组。在这个例子中，我们不会将这个符号作为一个键添加到一个自定义对象中，因为它将在数组中使用——所以这个是一个独立的迭代器。但是如果`Symbol.iterator`是自定义 iterable 对象中的一个键，那么它的结构将是相似的。首先，我们将创建反向迭代器:

类似于 for 循环，我们将跟踪索引并从集合的末尾开始(第 3 行)。`Symbol.iterator`使用`next`键定义何时以及如何迭代(第 5 行)。所有迭代器都依赖下一个方法来定义它的迭代模式。在我们的反向迭代器中，每次调用`next()`时，它将反向遍历整个结构(第 6 行)，当`i`小于零时停止(第 7 行)。

现在，我们可以在 for-of 循环中调用 reverse，然后*瞧*！在数组上反向迭代:

注意，在上面的例子中，迭代器并不局限于 for-of 循环:它们也可以和 ES6 中的 spread 操作符一起使用。在第 8 行，spread 操作符将`reverse`迭代器应用于我们的数组，将结果展开。

最后，我们看了一下 ES6 让我们使用 for-of 循环遍历大量数据类型的一些快捷方式——从数组到字符串，以及新提供的集合和映射。我们还接触了用`Symbol.iterator`构建我们自己的迭代器的介绍。显而易见，熟悉 JavaScript 的 ES6 中的 for-of 循环、迭代器和可重复项不仅会缩短我们的代码，还会打开自定义数据结构和工具的另一个维度，我们可以使用它们来导航和访问它们存储的信息。

参考资料—更深入地了解 ES6 迭代:

*   [Vijay pra sanna 介绍 JavaScript](https://www.digitalocean.com/community/tutorials/js-iterables) 中的可迭代程序和迭代器
*   [ES6 深度:迭代器和 for-of 循环](https://hacks.mozilla.org/2015/04/es6-in-depth-iterators-and-the-for-of-loop/)杰森·奥伦多夫著
*   蒂亚戈·洛佩斯·费雷拉的[揭秘 ES6 可迭代程序&迭代器](https://www.freecodecamp.org/news/demystifying-es6-iterables-iterators-4bdd0b084082/)
*   Arfat Salman 的《JavaScript 中 ES6 迭代器的简单指南及示例》
*   Deepak Gupta 的 Javascript ES6-Iterables 和迭代器
*   [JavaScript 中的' for…in '和' for…of '有什么区别？](https://medium.com/better-programming/what-is-the-difference-between-for-in-and-for-of-in-javascript-650952654e97)作者乔纳森·许