# 为什么是时候抛弃 JavaScript 中的 for…in 循环了——这是在 JS 中迭代对象的最佳方式

> 原文：<https://levelup.gitconnected.com/why-its-time-to-ditch-the-for-in-loop-in-javascript-75327f74004d>

![](img/f560e87efb2e3b0aeb6b1e4c2c29154a.png)

照片由[理查德·普莱斯](https://unsplash.com/@juanpoe?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

从 ES5.1 开始，我们有了遍历对象属性的新特性。在过去，我们只有`for...in`循环来完成这个任务。

在本文中，我们将看看为什么遍历对象属性的新方法比`for...in`循环好得多。这包括从 ES5.1 开始提供的`Object.keys`方法，但在 ES6 中更新以支持符号，以及 ES2017 新增的`Object.entries`和`Object.values`方法。要循环符号键，我们有`Reflect.ownKeys`方法。

# for…in 循环的特征

`for...in`循环让我们遍历对象的非符号、可枚举属性。

它将以任意顺序迭代对象的属性。因此，我们不能依赖于对象的键被迭代的顺序。

用`delete`关键字删除的属性不会被迭代。如果在迭代过程中修改了一个属性，那么以后访问它时的值将是修改完成后的值。

在迭代过程中修改一个对象的属性是一个坏主意，因为上面提到的特征会造成混乱。

有些人也用它来遍历数组，因为`for...in`会像对象的键一样获取数组的索引。然而，这又是一个坏主意，因为订单没有保证。

此外，它迭代自己的属性及其原型的属性。例如，如果我们有:

```
const person = {
  name: 'Joe'
};
const employee = {
  title: 'waiter'
};employee.__proto__ = person;
for (let prop in employee) {
  console.log(prop);}
```

然后我们看到`for...in`循环中的`console.log`记录了`name`和`title`。

我们可能不希望这样，所以我们必须从一个对象的原型使用`hasOwnProperty`方法来检查属性是否实际上是一个对象自己的属性，如下所示:

```
const person = {
  name: 'Joe'
};
const employee = {
  title: 'waiter'
};employee.__proto__ = person;
for (let prop in employee) {
  if (employee.hasOwnProperty(prop)) {
    console.log(prop);
  }
}
```

正如我们所看到的，没有`hasOwnProperty`,`for...in`循环遍历一个对象的所有自己的和继承的属性。

这不太方便，因为这通常不是我们想要的。

此外，由于`for...in`循环是在 ES6 之前引入的，并且从未更新过，所以它不知道符号属性键，所以如果我们有以下对象:

```
const foo = {
  [Symbol('foo')]: 'Joe'
};
```

当我们试图如下循环遍历`foo`对象时:

```
for (let prop in foo) {
  console.log(prop);
}
```

我们什么也得不到。

# 对于…在回路中的性能

`for...in`环路也很慢。这是因为循环必须检查每个属性在原型链上是否都是可枚举的，然后返回每个可枚举属性的名称。

让它与任意对象一起工作是困难的，因为结构不是恒定的。它必须检查每个键及其原型的键，以便对每个属性进行检查。因此，这比一个简单的`for`循环包含了更多的计算。

当然，数组或对象越大，这个问题就越严重，但是在这些情况下，还是要记住这一点。

# 遍历对象属性的新方法

## 对象.键

从 ES5.1 开始，我们有了`Object.keys`方法来获取对象的键并将它们作为数组返回。

密钥按以下顺序返回。首先是按数字升序排列的整数索引键。然后是所有其他的字符串键，按照它们被添加到对象的顺序。

与`for...in`循环不同，返回键的顺序有一些推理。由于`Object.keys`返回一个数组，我们可以按照自己喜欢的方式对它进行排序。

我们也可以使用`for...of`循环或`forEach`方法来编写一个更干净的循环。

此外，我们不必再使用`hasOwnProperty`来检查一个对象的属性是否是它自己的属性。

由于`Object.keys`随 ES6 一起发布，所以支持符号。

例如，假设我们拥有与上述示例相同的继承对象:

```
const person = {
  name: 'Joe'
};
const employee = {
  title: 'waiter'
};employee.__proto__ = person;
```

我们可以如下循环:

```
for (let prop of Object.keys(employee)) {
  console.log(prop);
}
```

我们只从上面的循环中返回`'title'`。

排序如下所示。如果我们有以下对象:

```
const foo = {
  a: 1,
  b: 2,
  1: 'a',
  2: 'b',
  3: 'c',
};
```

当我们用以下代码循环遍历这些键时:

```
for (let prop of Object.keys(foo)) {
  console.log(prop);
}
```

然后我们回来了:

```
1
2
3
a
b
```

![](img/e86792427fc5021ca3f9792342a9204e.png)

照片由 [Amit Jain](https://unsplash.com/@amitjain0106?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 对象.值

要从没有继承属性的对象中获取值，我们可以使用`Object.values()`方法。

迭代的顺序与`Object.keys`相同，但是我们得到的是值而不是键。

例如，给定以下对象:

```
const person = {
  name: 'Joe'
};
const employee = {
  title: 'waiter'
};
employee.__proto__ = person;
```

我们可以在没有`person`属性值的情况下遍历`employee`自己的值，如下所示:

```
for (let val of Object.values(employee)) {
  console.log(val);
}
```

我们应该只从`console.log`那里得到`'waiter'`。

`Object.values`方法是 ES2017 的新功能。

## 对象.条目

为了在一个循环中获得键值对，我们可以使用`Object.entries`方法。像上面提到的其他方法一样，它接受一个对象作为参数。

方法返回一个数组，该数组在数组中具有其自身属性的键值对。

例如，假设我们有以下对象:

```
const person = {
  name: 'Joe'
};
const employee = {
  title: 'waiter'
};
employee.__proto__ = person;
```

我们可以如下使用`Object.entries`方法:

```
for (let entry of Object.entries(employee)) {
  console.log(entry);
}
```

然后我们回来了:

```
["title", "waiter"]
```

那就是键和属性的值分别在`employee`中。

## Reflect.ownKeys

没有一个`Object`方法支持符号。要循环遍历带有符号键的对象，我们可以使用`Reflect.ownKeys`方法。像`Object.keys`一样，它获取对象自身属性的键，但也支持符号键。它还返回一个键数组。像`Object.keys.`一样也支持字符串键

例如，假设我们有以下对象:

```
const foo = {
  [Symbol('foo')]: 'Joe'
};
```

我们可以使用`Reflect.ownKeys`:

```
for (let prop of Reflect.ownKeys(foo)) {
  console.log(prop);
}
```

然后我们再回来`Symbol(foo)`。

如果我们编写以下循环，从符号键获取值也是可行的:

```
for (let prop of Reflect.ownKeys(foo)) {
  console.log(foo[prop]);
}
```

然后我们再回来`'Joe’`。

这是 ES6 的新方法。

正如我们所看到的，从 ES5.1 或更高版本开始，我们有很多替代方法来使用`for...in`循环遍历对象的属性。在`for...in`循环中唯一有用的特性是遍历对象的继承属性。

否则，我们应该使用`Object.keys`、`Object.entries`、`Object.values`，因为它们都返回键或值的数组，或者两者都返回。此外，顺序是可预测的，不像`for...in`循环。因为数组是返回的，所以我们可以对它们进行排序，并以我们喜欢的方式遍历属性或值。

如果我们需要循环符号键，我们可以使用`Reflect.ownKeys`方法。