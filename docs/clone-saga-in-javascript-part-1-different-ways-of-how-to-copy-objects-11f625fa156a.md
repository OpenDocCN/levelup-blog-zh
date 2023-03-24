# JavaScript 中的克隆传奇第 1 部分:复制对象的不同方式

> 原文：<https://levelup.gitconnected.com/clone-saga-in-javascript-part-1-different-ways-of-how-to-copy-objects-11f625fa156a>

![T](img/97e826c971e9cf5664ff0bfa07877590.png)  T 他的是 JavaScript 中克隆人战争传奇的开始。我们将探讨 JavaScript 中复制对象的主题，这是一个由三部分组成的系列。在本文中，我们将讨论 JavaScript 中存在哪种复制。在下一篇文章[中，我们将深入浅出的复制](https://pandaquests.medium.com/clone-saga-in-javascript-part-2-shallow-copying-in-javascript-9dc340b92634)。在本系列的最后一篇文章中，我们将研究深度复制。

复制对象是一项常见的任务，在使用语言的各种情况下都可能需要。我们将涉及各种方法以及复制对象背后的动机。为了在给定的情况下选择最合适的方法，了解复制对象的不同选项是很重要的。

这只是我们写的许多关于 JavaScript 或软件开发的文章中的一篇。我们试图每天发表一篇文章——即使是在节假日。关注或订阅我们，这样你就不会错过任何一个。

![](img/2ea46315d4eca827aae22efd995c14c8.png)

照片由 [Kadyn Pierce](https://unsplash.com/@illiminate86?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 JavaScript 中，有浅层复制和深层复制。对象的浅表副本是包含对原始对象属性的引用的对象副本，而不是创建属性的新副本。另一方面，深层副本创建原始对象属性的新副本，而不是包含对原始属性的引用。

下面是一个使用 assign()方法创建对象的浅层副本的示例:

```
const original = { a: 1, b: { c: 3 } };
const copy = Object.assign({}, original);
console.log(copy.b); // { c: 3 }
console.log(copy.b === original.b); // true
original.b.c = 123; // change the original and see that copy also changes
console.log(copy.b); // { c: 123 }
```

在此示例中，Object.assign()方法用于创建原始对象的浅表副本。复制对象包括对与原始对象相同的 b 属性的引用，而不是创建 b 属性的新副本。你也可以看到这只是一个浅层拷贝，因为一旦你改变了原始对象中的 b，这种改变也会反映在拷贝对象中。

以下是创建对象深层副本的示例:

```
function deepCopy(obj) {
 if (typeof obj !== 'object' || obj === null) {
   return obj;
 }
 const copy = Array.isArray(obj) ? [] : {};
 for (const key in obj) {
   if (obj.hasOwnProperty(key)) {
     copy[key] = deepCopy(obj[key]);
   }
 }
 return copy;
}

const original = { a: 1, b: { c: 3 } };
const copy = deepCopy(original);
console.log(copy.b); // { c: 3 }
console.log(copy.b === original.b); // false
original.b.c = 123; // change the original property
console.log(copy.b); // { c: 3 } property in the copy object stays the same
```

如您所见，即使您更改了原始对象中的属性，复制对象中的属性仍保持不变。这是因为创建了一个不受原始对象更改影响的新对象，而不是对原始对象的引用。

对于某些用例，浅层拷贝就足够了。当您只需要复制对象的顶级属性，而不需要复制任何嵌套对象的属性时，在 JavaScript 中浅层复制就足够了。浅表副本创建一个新对象，该对象具有与原始对象相同的顶级属性，但嵌套对象仍是对原始对象的引用。

下面是一些使用案例，其中浅拷贝可能就足够了:

*   当您希望创建一个与原始对象具有相同顶级属性的新对象，但不需要修改嵌套对象时。
*   当原始对象没有嵌套对象，或者嵌套对象是不可变的(即它们不能被修改)时。
*   当您希望创建一个与原始对象具有相同顶级属性的新对象，但您计划以不影响原始对象的方式修改嵌套对象时。

值得注意的是，浅拷贝在所有情况下都是不够的。如果需要创建包含所有嵌套对象的对象副本，则需要使用深层副本。深层副本创建一个新对象，该对象具有与原始对象相同的所有属性和值，包括任何嵌套对象的属性。

当您需要创建一个包含所有嵌套对象的对象副本，而不仅仅是对象的顶级属性时，深度副本在 JavaScript 中是必要的。深层副本创建一个新对象，该对象具有与原始对象相同的所有属性和值，包括任何嵌套对象的属性。

以下是一些可能需要深层拷贝的使用案例:

*   当您想要创建与原始对象具有相同属性和值的新对象时，包括任何嵌套对象的属性。
*   当需要在不影响原对象的情况下修改原对象的嵌套对象时。
*   当原始对象具有可变的嵌套对象(即，它们可以被修改)并且您想要用这些嵌套对象的副本而不是对原始对象的引用来创建新对象时。

![](img/e9440f2c5a242b2f777abc96595ecffb.png)

这只是我们克隆传奇的第一部分。我们还没开始呢。在下一部分中，我们将更深入地探讨 JavaScript 中的复制。从浅抄开始。敬请关注。

如果你喜欢这个系列，请关注或订阅我们，这样你就不会错过 JavaScript 克隆传奇的下一部分。愿原力与你同在。