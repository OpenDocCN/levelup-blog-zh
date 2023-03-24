# 替换循环如何帮助您编写更好的代码

> 原文：<https://levelup.gitconnected.com/how-replacing-loops-can-help-you-write-better-code-6eaa91235192>

![W](img/d891666cc9ebd9231a574f9c9cb6a45e.png)  W 你们当中有谁没用过 loop？循环可能是您最先学习的编程概念之一。循环是任何编程语言的必要组成部分，因为它们允许我们多次重复一段代码。JavaScript 支持各种循环，包括 for、while 和 do-while 循环。

然而，有时候循环并不是解决问题的最有效或最优雅的方法。在本文中，我们将研究一些 JavaScript 循环替代方案，以及它们如何帮助我们编写更干净、更简洁的代码。

我们将看到一些内置的数组方法，如 map、filter 和 reduce，它们通常可以用来代替循环来完成同样的事情。我们还将讨论如何使用递归函数和生成器函数来替代循环。

这只是众多关于 JavaScript 和软件开发的文章中的一篇。欢迎关注我们，获取更多关于 JavaScript 和软件开发的精彩内容。我们尝试一周发布多次。请确保不要错过我们的任何精彩内容。

![](img/020c4b54f3884ca96c10387b0f3c8eee.png)

奥比·费尔南德斯在 Unsplash 上的照片

用更有效的替代方法替换 JavaScript 中的循环有几个优点:

*   **性能提升**:循环会消耗大量资源，尤其是在处理大型数据集的时候。用更有效的替代方法替换循环可以提高代码的整体性能。
*   **可读性更强的代码**:高阶函数，比如 map、reduce 和 filter，可以让你在一行代码中表达复杂的运算，让你的代码更加简洁易读。
*   **改进的调试**:高阶函数通常包含错误处理和调试功能，使得识别和修复代码中的问题变得更加容易。
*   **增强的功能**:许多高阶函数扩展了传统循环的功能，例如处理异步数据或对数据集执行复杂转换的能力。
*   **增强的代码重用**:当您使用高阶函数时，您可以将复杂的逻辑封装在可重用的函数中，这些函数可以轻松地在您的代码库中共享和重用。

在 JavaScript 中，有几种替代循环的方法。其中包括:

**使用 forEach()方法**迭代数组。您可以使用 for each()方法为数组中的每个元素指定一个回调函数。以下是如何使用 forEach()方法代替 for 循环的示例:

```
const numbers = [1, 2, 3, 4, 5];
numbers.forEach((number) => {
  console.log(number);
}); // 1 2 3 4 5
```

**使用 map()方法**对数组的元素进行变换。map()方法通过对原始数组的每个元素应用一个转换函数来生成一个新数组。下面是如何使用 map()方法代替 for 循环的示例:

```
const numbers = [1, 2, 3, 4, 5];
const squares = numbers.map((number) => {
  return number * number;
}); // [1, 4, 9, 16, 25]
```

**使用 reduce()** 方法将数组中的元素缩减为单个值。您可以使用 reduce()方法将归约函数应用于数组的元素，并获得单个结果。出于同样的原因，您也可以使用 reduceRight()。它的工作方式就像 reduce()方法一样，但是它不是从左向右迭代，而是从右向左迭代。下面是如何使用 reduce()方法代替 for 循环的示例:

```
const numbers = [1, 2, 3, 4, 5];
const sum = numbers.reduce((a, b) => {
  return a + b;
}, 0); // 15
```

**使用 filter()方法**检查每个元素的某个条件。如果满足该条件，这些项将被放入一个新的数组，该数组将被返回。例如

```
const numbers = [1,2,3,4,5];
const onlyNumbersLessThan4 = numbers.filter(x => x < 4); // [1,2,3]
```

**使用 some()** 方法遍历数组，如果条件为假则停止。您传入一个高阶函数，该函数检查数组中的每一项，并返回 truthy 或 falsy。只要返回 false，它就会继续遍历数组，直到结束。一旦高阶函数返回真，一些停止迭代，并从高阶函数返回值(真或假)。例如:

```
const numbers = [1,2,3,4,5];
const isAtLeastOneLargerThan3 = numbers.some(x => x > 3); // true
```

**使用 every()方法**遍历数组，一旦不满足某个条件就停止。您传入一个高阶函数，该函数计算数组中的每一项，并返回 truthy 或 falsy。一旦高阶函数返回 falsy，every()方法就停止迭代并返回 false。否则，它将迭代到数组的末尾，并返回 true。这里有一个例子:

```
const numbers = [11,12,13,14,15];
const isAllLargerThan10 = numbers.every(x => x > 10); // true
```

**使用递归**迭代一个数组。递归函数是一种调用自身直到满足特定条件的函数。在某些情况下，这可以作为循环的替代方法。然而，根据你使用的递归类型(尾调用递归或头调用递归)和环境，它可能比其他方法消耗更多的内存。下面是递归的一个例子:

```
function countDown(n) {
  if (n === 0) {
    console.log("Happy New Year!");
  } else {
    console.log(n);
    countDown(n - 1);
  }
}

countDown(3);

// 3 2 1 Happy New Year!
```

**使用 find()方法**在数组中搜索某一项。find 方法将返回满足特定条件的第一个项目。您传入一个高阶函数，该函数可以检查项目并返回 truthy 或 falsy。一旦它返回 truthy，它就返回高阶函数正在检查的项目。

```
const objects = [{id: 1, name: 'one'},{id: 2, name: 'two'}, {id: 3, name: 'three'}];
const itemWithId2 = numbers.find(x => x.id === 2); // {id: 2, name: 'two'}
```

**使用 findIndex()方法**获取数组中满足一定条件的第一个元素的索引。您可以传入一个对数组中的每个元素都执行的高阶函数。一旦该函数返回 truthy，findIndex()方法将返回当前项目的索引。让我们看看下面的例子:

```
const numbers = [11,12,13,14];
const indexOf13 = numbers.findIndex(x => x === 13); // 2
```

**使用 includes()方法**检查数组中是否存在某项。includes()方法采用高阶函数。该函数遍历所有数组项并检查某个条件。一旦这个条件为真，它就返回真。否则为假。让我们用一个例子来说明:

```
const numbers = [1,2,3,4];
const hasNumber10 = numbers.includes(x => x === 10); // false
```

这些只是 JavaScript 中存在的替代循环的几个例子。它们中的大多数比传统的 for-loop 更容易读、写和维护。但是，请注意，这些替代方法并不总是适合替换循环，在许多情况下，循环可能是最合适的解决方案。在决定使用哪种方法时，仔细考虑代码的特定需求非常重要。

![](img/5c7fdb823e2c7f4190f716ff6bed224c.png)

这篇文章有帮助吗？如果是这样，请务必关注并订阅我们。我们每周多次发布关于 JavaScript 和软件开发的有趣文章。我们为你将复杂的话题分解成小而易理解的部分。所以，你不想错过任何一个。回头见！