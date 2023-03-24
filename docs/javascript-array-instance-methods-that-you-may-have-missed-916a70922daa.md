# 您可能错过的 JavaScript 数组实例方法

> 原文：<https://levelup.gitconnected.com/javascript-array-instance-methods-that-you-may-have-missed-916a70922daa>

![](img/6785233536947a63f06bda7d5860b605.png)

照片由 [Mehrpouya H](https://unsplash.com/@mehrpouya?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 在最近的版本中增加了许多新特性。其中之一是新的数组方法，它使得操作数组将其他对象转换为数组变得更加容易。

在本文中，我们将看看一些我们已经错过的 JavaScript 数组方法。

# 搜索数组元素

我们可以使用`find`和`findIndex`方法来搜索数组条目的第一个实例。

`find`返回数组中搜索结果的第一个实例。它接受一个回调函数，该函数返回搜索条件的布尔表达式。

它还需要第二个参数来设置回调中`this`的值。

例如，我们可以如下使用`find`:

```
const arr = [{
    name: 'Jane'
  },
  {
    name: 'Joe'
  },
];const jane = arr.find(n => n.name == 'Jane');
```

在上面的代码中，我们传入了一个带有要查找的条件的回调，它是值为`'Jane'`的`name`属性。

`jane`的值`{name: “Jane”}`与我们预期的一样。

我们可以设置`this`的值，并在回调中使用它，如下所示:

```
const arr = [{
    name: 'Jane'
  },
  {
    name: 'Joe'
  },
];const jane = arr.find(function(n) {
  return this.findJane(n);
}, {
  findJane: n => n.name == 'Jane'
});
```

在上面的代码中，我们用`findJane`方法作为第三个参数传入了一个对象。

然后我们调用`this.findJane`来调用函数并返回结果。

因此，我们应该得到与第一个例子中相同的`jane`值。

`findIndex`返回在数组中找到的第一个结果实例的索引。

它采用与`find`相同的参数。

例如，我们可以写:

```
const arr = [{
    name: 'Jane'
  },
  {
    name: 'Joe'
  },
];const jane = arr.findIndex(n => n.name == 'Jane');
```

然后我们得到`jane`的值`0`，因为具有`name`值`'Jane'`的条目在第一个条目中。

和`find`方法一样，我们也可以在回调中设置`this`的值。

例如，我们可以写:

```
const arr = [{
    name: 'Jane'
  },
  {
    name: 'Joe'
  },
];const jane = arr.findIndex(function(n) {
  return this.findJane(n);
}, {
  findJane: n => n.name == 'Jane'
});
```

然后我们可以做和`find`例子一样的事情。我们在回调中将`this`的值设置为第二个参数中的对象，并在回调中调用它。

我们得到了和上一个例子一样的结果。

![](img/b1e7517a29b26869df70e8116136ee2b.png)

照片由[卡里姆·曼吉拉](https://unsplash.com/@karim_manjra?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# `Array.prototype.copyWithin()`

`copyWithin`方法将数组的一部分复制到另一个数组，要复制的部分由起始索引到结束索引指示。

它有以下签名:

```
arr.copyWithin(target[, start[, end]])
```

其中`target`是序列要复制到的从零开始的索引。如果为负数，`target`将从末尾开始计数。

如果`target`等于或大于`arr.length`，则不会复制任何内容。如果`target`位于`start`之后，复制的序列将被修剪以适合`arr.length`。

如果省略`start`，则`copyWithin`将从索引`0`复制。

`start`是可选参数，它是从零开始复制元素的索引。如果是负数，`start`将从末尾开始计数。

`end`是可选参数，是从零开始的索引，在该处结束复制。

如果省略`end`，则`copyWithin`复制到最后一个索引。

方法返回修改后的数组，其中复制的项覆盖原始项。

例如，如果我们有`const arr = [1, 2, 3, 4, 5].copyWithin(0, 3)`，那么`arr`就是`[4, 5, 3, 4, 5]`，因为我们从条目索引 3 复制到数组的末尾，并且目标被设置为索引 0，这是第一个条目。

因此，由于复制的零件有 2 个条目，它会覆盖第一个和第二个条目。

另一个例子如下:

```
const arr = [1, 2, 3, 4, 5].copyWithin(-2, -3, -1)
```

在上例中，我们从倒数第三个条目复制到最后一个条目，目标设置为倒数第二个位置。

因此，`3, 4`会覆盖原数组中的`4, 5`。

所以`[1, 2, 3, 3, 4]`由`copyWithin`返回，我们得到它作为`arr`的值。

# 结论

我们可以使用`find`和`findIndex`来搜索数组元素。

`copyWithin`方法对于用数组的一部分覆盖数组的另一部分很方便。