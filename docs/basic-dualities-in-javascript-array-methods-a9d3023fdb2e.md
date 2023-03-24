# Javascript 数组方法中的基本对偶

> 原文：<https://levelup.gitconnected.com/basic-dualities-in-javascript-array-methods-a9d3023fdb2e>

## Javascript 数组方法完全指南

![](img/83dac612d9192ca5b9769385f2d61b4d.png)

由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

在我作为软件工程师的职业生涯中，我和许多软件开发人员一起工作过，开发了各种各样的 Javascript 应用程序。正如我在[上一篇文章](/improve-code-readability-while-working-with-javascript-arrays-419d48dd21b)中提到的，多年来，我观察到大多数开发人员在选择如何使用 Javascript 数组时缺乏想象力和多样性。在过去的几年里，Javascript `Array`全局对象中增加了许多有用的函数。

随着这些功能中许多功能的增加，其逻辑上相反的版本也增加了，这意味着有可能使用一个功能来执行某个动作，并使用其相反的孪生功能来撤销该动作，如果需要的话，通过在相反的方向上执行工作。当我通读代码并看到开发人员使用这些方便的函数之一来执行某项任务，同时手动实现一堆效率较低的逻辑来执行与该函数完全相反的任务时，我有时会感到惊讶，而有用的内置对立函数一直都在那里准备使用。

由于明显普遍缺乏关于多年来添加到 Javascript `Array`全局对象中的许多函数对的知识，我觉得有必要调用所有这些函数对。一起使用这些函数对带来的好处不仅是节省了开发人员实现自己的解决方案所花费的时间，而且还有助于清理他们的代码并提高效率，因为核心 Javascript 函数通常比手工实现更有效。

# Array.indexOf()和 Array.lastIndexOf()函数

谈到使用数组，最古老和最广泛使用的函数之一是`[indexOf()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf)`函数。毫无疑问，这是开发人员在开始使用 Javascript 数组时最先接触到的功能之一。

`indexOf()`函数的工作方式很简单:给定一个搜索参数，从头到尾搜索整个数组，并返回该搜索参数在数组中第一次出现的索引，如果数组不包含搜索参数，则返回`-1`。以下示例演示了这两种结果:

```
var letters = ['a', 'b', 'c', 'd', 'e', 'd', 'c', 'b', 'a'];var dIndex = letters.indexOf('d');
// dIndex: 3
var zIndex = letters.indexOf('z');
// zIndex: -1
```

如上所述，该函数找到并返回数组中包含的搜索参数的第一个匹配项的索引`3`，同时返回数组中不包含的搜索参数的默认索引`-1`。在内容被预先排序成某种有用的顺序的情况下，使用`indexOf()`函数来寻找一个特定数组元素的索引，该元素预计在数组的开始处，这对于使用该函数来使代码更快、更有效有很大的帮助。

但是如果有一个特殊的数组元素应该在数组的末尾呢？在使用`indexOf()`函数查找索引并计算数组元素反转前该值与哪个索引相关之前，是否应该首先使用`[reverse()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse)`函数执行数组元素的就地重排，就像我之前在一些代码示例中看到的那样？当然不是。使用`[lastIndexOf()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/lastIndexOf)`函数来完成这样的任务要容易得多。

`lastIndexOf()`函数的工作方式和`indexOf()`函数一样简单:给定一个搜索参数，从头到尾搜索整个数组，并返回该搜索参数在数组中最后一次出现的索引，如果数组不包含搜索参数，则返回`-1`。以下示例演示了这两种结果:

```
var letters = ['a', 'b', 'c', 'd', 'e', 'd', 'c', 'b', 'a'];var dIndex = letters.indexOf('d');
// dIndex: 5
var zIndex = letters.indexOf('z');
// zIndex: -1
```

如上所述，该函数找到并返回数组中包含的搜索参数最后一次出现的索引`5`，同时返回数组中不包含的搜索参数的默认索引`-1`。

因此，如果您有一个数组元素的有序列表，并且您知道某个数组元素位于数组的哪一端(开头还是结尾),您可以使用`indexOf()`或`lastIndexOf()`函数更有效地查找该数组元素的索引，只要您根据对数组元素位置的预期做出正确的选择。当然，如果数组元素是无序的，并且无法知道特定元素在数组中的位置，那么使用这两个函数都会产生相同的结果。虽然，在这种情况下，我建议最好使用`indexOf()`函数，因为在需要索引的情况下，它总是被期望使用的函数，所以最好遵循那些可能阅读你的代码的人的一般期望。

最后，在进入下一个函数对之前，根据我在本文介绍中提到的前一篇文章，我想就如何提高在某些上下文中可能使用`indexOf()`函数的代码的可读性提出一个建议。大多数时候，我看到代码中使用的`indexOf()`函数纯粹是为了判断一个数组是否包含一个元素，并没有对该数组元素做进一步的处理。在这种情况下，我认为`[includes()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)`功能是一个更好的选择。下面的例子清楚地证明了我的论点:

```
var letters = ['a', 'b', 'c', 'd', 'e', 'd', 'c', 'b', 'a'];var containsE = letters.indexOf('e') !== -1;var includesE = letters.includes('e');
```

虽然发现了相同的结果，但是使用`indexOf()`函数的代码不如使用`includes()`函数的代码读起来清晰。你需要停下来仔细阅读`indexOf()`函数代码，以验证它确实在检查数组是否包含`e`，而`includes()`函数代码清楚地描述了正在检查的内容，正如变量名所暗示的那样。因此，在只检查数组元素包含的情况下，我强烈建议使用`includes()`函数，以便更清楚地向读者传达您的意图。

# Array.pop()和 Array.push()函数

我想讨论的下一对函数是`[pop()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/pop)`和`[push()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push)`函数，我相信许多人对它们的了解几乎和他们对`indexOf()`函数的了解一样多。我想将它们包含在本文中的一个主要原因是为了完整性。这两个函数在处理数组的 Javascript 代码中几乎和`indexOf()`函数一样无处不在的原因是，在处理数组和操作它们的元素时，它们是无价的。

`pop()`函数的工作方式从它的名字可以清楚地看出:数组的最后一个元素被*弹出*或者从数组中移除并返回。当然，这将改变数组的长度，减少一个。以下示例演示了`pop()`函数的工作原理:

```
var letters = ['a', 'b', 'c', 'd', 'e'];
var lastLetter = letters.pop();
// lastLetter: e
// letters: ['a', 'b', 'c', 'd']
```

就像`pop()`函数一样，`push()`函数就像它的名字所暗示的那样工作:一个或多个指定的元素被*推送到*或者添加到数组的末尾，并且数组的新长度被返回。以下示例演示了`push()`函数的工作原理:

```
var letters = ['a', 'b', 'c'];
var length = letters.push('d', 'e');
// length: 5
// letters: ['a', 'b', 'c', 'd', 'e']
```

`pop()`函数执行与`push()`函数完全相反的工作，这使得这些函数成为一对完美的 Javascript `Array`函数。

# Array.shift()和 Array.unshift()函数

我想讨论的下一对函数是`[shift()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/shift)`和`[unshift()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift)`函数，它们的工作方式类似于`pop()`和`push()`函数，但根据我整个职业生涯的经验，它们远没有这些函数使用得广泛。尽管用得不多，但在处理数组和操作数组元素时，`shift()`和`unshift()`函数同样有价值。

`shift()`函数的工作方式类似于`pop()`函数:数组的第一个元素从数组中移除并返回，而数组中所有剩余的元素都下移一个索引。当然，这将改变数组的长度，减少一个。以下示例演示了`shift()`函数的工作原理:

```
var letters = ['a', 'b', 'c', 'd', 'e'];
var firstLetter = letters.shift();
// firstLetter: a
// letters: ['b', 'c', 'd', 'e']
```

就像`push()`函数一样，`unshift()`函数的工作方式与`shift()`函数完全相反:一个或多个指定的元素被添加到数组的开头，数组的新长度被返回。以下示例演示了`unshift()`函数的工作原理:

```
var letters = ['c', 'd', 'e'];
var length = letters.unshift('a', 'b');
// length: 5
// letters: ['a', 'b', 'c', 'd', 'e']
```

`shift()`功能执行与`unshift()`功能完全相反的工作。就像`pop()`和`push()`函数一样，这些函数是一对完美的相反的 Javascript `Array`函数。

# Array.some()和 Array.every()函数

离开改变数组大小的函数，我想讨论的下一个函数是`[some()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)`和`[every()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every)`函数。这些函数不是以任何方式改变数组或其任何元素，而是提供用于确定数组中的一个或多个元素是否通过指定逻辑测试的功能。此外，它们因其提供的功能而被恰当地命名，这意味着在您的代码中使用它们可以比试图提供类似逻辑的自行开发的实现提供更高级别的可读性。

`some()`函数的工作方式非常简单:指定的测试函数从头到尾对每个数组元素运行，只要该测试函数对至少一个数组元素返回`true`，`some()`就会返回`true`。否则，如果没有数组元素导致测试函数返回`true`，则`some()`将返回`false`。以下示例演示了`some()`函数的工作原理:

```
var numbers = [1, 2, 3, 4, 5];
var lessThanTen = numbers.some(number => number < 10);
// lessThanTen: true
var greaterThanTen = numbers.some(number => number > 10);
// greaterThanTen: false
```

关于`some()`函数运行方式的一个有用之处是，一旦它找到满足测试函数中逻辑的数组元素，它将停止执行并返回`true`，而不是继续遍历整个数组直到结束。关于`some()`函数需要记住的一点是，当它在一个空数组上被调用时，它将总是返回`false`，所以在执行它的`some()`函数之前，要确保一个数组至少包含一个元素。

`every()`函数的工作方式与`some()`函数的工作方式正好相反，但同样简单:指定的测试函数从头到尾对每个数组元素运行，只要该测试函数对每个数组元素返回`true`，则`every()`将返回`true`。否则，`every()`一找到导致测试函数返回`false`的数组元素，就会返回`false`。以下示例演示了`every()`函数的工作原理:

```
var numbers = [1, 2, 3, 4, 5];
var lessThanTen = numbers.every(number => number < 10);
// lessThanTen: true
var greaterThanTen = numbers.every(number => number > 10);
// greaterThanTen: false
```

类似于`some()`函数，`every()`函数将停止执行并返回`false`，只要它发现一个不满足测试函数中逻辑的数组元素，而不是继续遍历整个数组直到结束。从上面对`some()`函数的描述可以看出，`every()`函数的工作方式完全相反，但同样有用且高效。关于`every()`函数需要记住的一点是，当它在一个空数组上被调用时，它将总是返回`true`，所以在执行它的`every()`函数之前，要确保一个数组至少包含一个元素。

# Array.reduce()和 Array.reduceRight()函数

我想在本文中讨论的最后一对函数是`[reduce()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)`和`[reduceRight()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/ReduceRight)`函数。与本文中讨论的其他函数不同，这两个函数要复杂得多，但是在处理数组时非常有用。它们都类似于`some()`和`every()`函数，因为它们接受一个函数并对每个数组元素运行一次，但这是相似性的终点。

`reduce()`函数的工作方式如下:第一个参数是 reducer 函数，第二个参数是传递给 reducer 函数的*可选的*初始值。reducer 函数的参数是一个累加器值，它具有 reducer 函数第一次执行期间的初始值(如果提供了的话)，以及 reducer 函数正在执行的当前数组元素的值。当 reducer 函数在后续数组元素上执行时，累加器值将具有 reducer 函数的前一次执行返回的值。以下示例演示了`reduce()`函数的工作原理:

```
var letters = ['a', 'b', 'c', 'd', 'e'];
var word = array.reduce((word, letter) => word + letter, '')
// word: abcde
```

使用`reduce()`功能时，有几件非常重要的事情需要记住。首先，如果没有提供初始值，reducer 函数将不会在数组的第一个元素上执行。此外，如果数组为空并且提供了初始值，或者数组只有一个元素并且没有提供初始值，则前一种情况下的初始值和后一种情况下的单个数组元素值将被简单地返回，而不会执行 reducer 函数。最后，如果没有提供初始值，数组为空，`reduce()`函数将抛出一个错误。出于这些原因，强烈建议始终提供初始值，以避免意外行为。

在使用`reduceRight()`功能时，所有这些事情都应该牢记在心。`reduceRight()`函数的工作方式与`reduce()`函数完全相同，但它从数组的末尾开始，并通过数组返回到开头。当您的数组以特定方式排序，但您不想在使用`reduce()`函数之前使用`reverse()`函数切换数组元素的顺序时，此函数特别有用。以下示例演示了`reduceRight()`函数的工作原理:

```
var letters = ['a', 'b', 'c', 'd', 'e'];
var word = array.reduceRight((word, letter) => word + letter, '')
// word: edcba
```

`reduceRight()`功能执行与`reduce()`功能完全相同的工作，但其执行方向与`reduce()`功能相反。就像本文中讨论的所有其他函数对一样，`reduce()`和`reduceRight()`函数是一对完美的相反的 Javascript `Array`函数。

# 结论

在本文中，我们介绍了几对 Javascript `Array`函数，它们提供了不同类型的功能。其中一些函数对提供了确定数组元素是否满足特定逻辑测试的能力，而其他函数对提供了操作数组本身包含的元素列表的能力。尽管它们在功能上有所不同，但每一对函数都为开发人员提供了实现特定功能的能力，也可以轻松地实现相反方向的功能。

这些相关函数对的知识是有用的，因为它使开发人员不必构思和实现他们自己的解决方案来逆转可能对阵列做出的任何改变或在阵列上运行相反的逻辑测试。将它们放在您的编码工具带中还可以节省大量的时间和精力，以解决在开发应用程序时不可避免地会出现的更重要的问题。我希望这篇文章能够帮助许多开发人员在涉及到所有 Javascript 数组相关的编码情况时拓宽他们的视野。

# 资源

[Javascript Array.indexOf()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf)

[Javascript Array.reverse()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse)

[Javascript Array.lastIndexOf()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/lastIndexOf)

[Javascript Array.includes()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)

[Javascript Array.pop()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/pop)

[Javascript Array.push()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/push)

[Javascript Array.shift()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/shift)

[Javascript Array.unshift()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift)

[Javascript Array.some()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)

[Javascript Array.every()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every)

[Javascript Array.reduce()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)

[Javascript Array.reduceRight()函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/ReduceRight)