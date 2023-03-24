# 编写更健壮代码的 JavaScript 最佳实践——删除重复和合并数组

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-for-writing-more-robust-code-removing-duplicates-and-merging-arrays-67d0dfce9592>

![](img/7ccdea8a26b3baa9b2fa147ac5d1e812.png)

照片由 [Leon Hinz](https://unsplash.com/@llon?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是一种简单易学的编程语言。编写运行并执行某些操作的程序很容易。然而，很难考虑所有的用例并编写健壮的 JavaScript 代码。

在本文中，我们将研究如何以一种可靠的方式从数组中移除重复项。

# 设置

我们可以使用 JavaScript `Set`构造函数来创建集合，这些集合是不能包含重复项的对象。

重复项的确定方式类似于`===`操作符，但是 we -0 和+0 被认为是不同的值。

为了确定`Set` s 的重复项目，`NaN`也被认为与自身相同

我们可以从数组创建一个集合，如下所示:

```
const set = new Set([1, 2, 3, 3]);
```

然后我们有一个`Set`实例，它的值是 1、2 和 3。

由于`Set`是一个可迭代对象，我们可以使用 spread 操作符将其转换回数组，如下所示:

```
const noDup = [...set];
```

正如我们所见，将`Set`转换回数组非常容易。

因为确定重复的算法是以类似于`===`操作符的方式确定的，所以它对于删除重复的原始值很有效。

但是，对于对象来说，除非它们引用内存中的同一个项，否则效果不好。

如果我们有对象，那么消除重复的最可靠的方法是将它们转换成字符串，然后解析回对象。

例如，如果我们有以下数组:

```
const arr = [{
  a: 1
}, {
  a: 1
}];
```

然后，我们可以编写以下代码，将数组映射到一个字符串，将其转换为一个`Set`，然后我们可以将剩余的项解析回对象，如下所示:

```
const set = new Set(arr.map(a => JSON.stringify(a)));
const noDup = [...set].map(a => JSON.parse(a));
```

在上面的代码中，我们有一个`Set`，它是从用`JSON.stringify`字符串化的数组条目中创建的。

然后我们使用 spread 操作符将`set`扩展回一个数组，然后将字符串化的条目映射回带有`JSON.parse`的对象。

这对于没有方法的普通对象来说很有效。

如果我们的对象有方法，那么我们应该确保每个条目引用同一个对象。

也有一些方法使遍历它们变得更容易。有一个`entries`方法将所有条目作为迭代器获取，迭代器将每个条目作为具有`[key, value]`结构的数组返回。

`forEach`使用回调来遍历它们。`keys`和`values`方法让我们分别得到键和值。

方法从集合中移除所有的项目。

它还具有`size`属性来获取`Set`的大小。

# 使用扩展运算符

spread 操作符是 JavaScript 最近增加的最有用的特性之一。

当它和数组一起使用时，它可以让我们复制数组或者合并数组，而不需要调用任何方法。这使得我们的代码简短易读。

我们只是用 spread 操作符把所有东西放在一个数组中，然后我们得到一个包含新条目的新数组。

此外，它允许我们将不同类型的 iterable 对象中的项组合到一个数组中。

例如，我们可以对多个数组使用 spread 运算符，如下所示:

```
const arr1 = [1, 2, 3];
const arr2 = [4, 5];
const arr = [...arr1, ...arr2];
```

然后我们得到`arr`的值是`[1, 2, 3, 4, 5]`。

正如我们所看到的，条目是用 spread 操作符按顺序添加到新数组中的。

由于 spread 操作符处理不同种类的 iterable 对象，我们也可以将`Map`和`Set`扩展到数组中:

```
const arr1 = [1, 2, 3];
const set = new Set([4, 5]);
const map = new Map();
map.set('a', 1);
map.set('b', 2);
const arr = [...arr1, ...set, ...map];
```

那么我们得到`arr`是:

```
[
  1,
  2,
  3,
  4,
  5,
  [
    "a",
    1
  ],
  [
    "b",
    2
  ]
]
```

`Map`被转换成一个数组，数组中的条目是`key`和`value`的数组。

![](img/0fca7e321adb259bba5d6e70a2a36fb0.png)

胡安·Á·阿尔瓦雷斯·阿贾米尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 结论

`Set`对于从数组中删除重复项很有用。它们也可以被转换或合并成数组。

我们还可以用 spread 操作符将多种可迭代对象合并到一个数组中。