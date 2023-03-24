# JavaScript 中遍历对象的 6 种方法

> 原文：<https://levelup.gitconnected.com/6-ways-to-loop-over-an-object-in-javascript-1e747d2ede70>

![L](img/157f0aef48e7f04a194c370c5325324a.png)  L   oops 是基本的编程构造，允许你重复执行一段代码。在 JavaScript 中有几种方法可以遍历对象，这对于访问和操作对象的键和值很有用。

在 JavaScript 中，对象是表示键值对集合的基本数据类型。它们经常用于存储和组织结构化数据，可以使用点符号或方括号符号来访问。

在对象上循环时，可以根据具体需要迭代对象的键、值或键值对。有几种方法可以实现这一点，包括使用 for…in 循环、带有 Object.keys()的 for 循环、带有 Object.entries()的 for…of 循环等等。

在本文中，我们将研究 JavaScript 中遍历对象的不同方法，并讨论每种方法的优缺点。我们还将看一些如何在实践中使用这些方法的例子。无论您是初学者还是有经验的开发人员，学习如何循环对象是一项重要的技能，在各种情况下都会派上用场。

这只是众多关于 JavaScript 和软件开发的文章中的一篇。欢迎关注我们，获取更多关于 JavaScript 和软件开发的精彩内容。我们尝试一周发布多次。请确保不要错过我们的任何精彩内容。

![](img/da9376c76babb462e8aca0566b99db6a.png)

照片由[诺亚·博耶](https://unsplash.com/@emerald_?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 中有几种方法可以循环对象。以下是循环遍历对象的可能方法及其优缺点:

## 使用 for…in 循环

```
for (const key in object) {
 const value = object[key];
 // do something with the key and value
}
```

该方法将迭代对象的键，并允许您访问相应的值。请注意，迭代的顺序没有保证，继承的属性将包含在迭代中。

好处:

*   简单易用
*   允许您访问对象的键和值

陷阱:

*   不保证迭代的顺序
*   继承的属性将包含在迭代中

## 使用 Object.keys()和 for 循环

```
const keys = Object.keys(object);
for (let i = 0; i < keys.length; i++) {
 const key = keys[i];
 const value = object[key];
 // do something with the key and value
}
```

此方法将检索对象的键数组，并允许您在标准 for 循环中对它们进行循环。迭代的顺序将与数组中键的顺序相同。

好处:

*   允许您在标准 for 循环中循环对象的键
*   迭代的顺序保证与数组中键的顺序相同

陷阱:

*   不允许您直接访问对象的值
*   在迭代中不包括继承的属性

## 使用 Object.entries()和 for…of 循环

```
for (const [key, value] of Object.entries(object)) {
 // do something with the key and value
}
```

该方法将检索对象的键值对数组，并允许您使用 for…of 循环遍历它们。迭代的顺序将与数组中键值对的顺序相同。

好处:

*   允许您以简洁的方式遍历对象的键值对
*   迭代的顺序保证与数组中键值对的顺序相同

陷阱:

*   在迭代中不包括继承的属性

## 对 Object.values()使用 for…of 循环

```
for (const value of Object.values(object)) {
 // do something with the value
}
```

该方法将检索对象值的 iterable，并允许您使用 for…of 循环遍历它们。迭代的顺序没有保证，继承的属性不会包含在迭代中。

好处:

*   允许您以简洁的方式遍历对象的值

陷阱:

*   不保证迭代的顺序
*   在迭代中不包括继承的属性

## 使用 forEach()循环

```
Object.keys(object).forEach(function(key) {
 const value = object[key];
 // do something with the key and value
});
```

该方法将检索对象的键数组，并使用 forEach()方法对它们进行迭代。forEach()方法是一个高阶函数，它允许您传递一个要为数组中的每个元素执行的回调函数。迭代的顺序将与数组中键的顺序相同。

好处:

*   允许您使用高阶函数来迭代对象的键
*   迭代的顺序保证与数组中键的顺序相同

陷阱:

*   不允许您直接访问对象的值
*   在迭代中不包括继承的属性

## 对对象的 Symbol.iterator 属性的 entries()方法使用 for…of 循环

```
const iterator = object[Symbol.iterator]();
for (const [key, value] of iterator) {
 // do something with the key and value
}
```

该方法将检索对象的迭代器，并允许您使用 for…of 循环遍历其键值对。迭代的顺序没有保证，继承的属性不会包含在迭代中。

好处:

*   允许您使用 for…of 循环在迭代器的帮助下迭代对象的键值对

陷阱:

*   不保证迭代的顺序
*   在迭代中不包括继承的属性

这就是了。我们向您展示了 JavaScript 中循环对象的不同方法。您选择哪种方法将取决于您的具体需求和偏好。有些方法在某些情况下可能更合适，而有些方法在其他情况下可能更合适。为了选择最适合您需求的方法，仔细考虑每种方法的优点和缺点是很重要的。

![](img/5c7fdb823e2c7f4190f716ff6bed224c.png)

你最后一次迭代一个对象是什么时候？用例是什么？你用的是哪种方法？这是这里介绍的方法之一吗？还是用了另外一个？你知道另一种迭代对象的方法吗？在这里分享一下你的经验。我们很想知道。

也别忘了跟着我们。我们每周发表多篇文章。所以，不要错过任何一个。再见