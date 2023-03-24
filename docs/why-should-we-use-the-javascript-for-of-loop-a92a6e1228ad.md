# 我们为什么要使用 JavaScript For-Of 循环？

> 原文：<https://levelup.gitconnected.com/why-should-we-use-the-javascript-for-of-loop-a92a6e1228ad>

![](img/93ff05d79f16925b219496cfc67d269f.png)

布莱恩·霍尔兹沃思在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

从 ES6 开始，我们有了`for...of`循环。这是一个非常有用的循环，它可以做比常规 for 循环更多的事情。在本文中，我们将看看为什么`for...of`循环对开发人员有用，以及其性能是否足以取代`for`循环。

`for...of`循环让我们遍历数组、类似数组的对象和用户定义的可重复项。它让我们不用使用不同种类的方法和循环就能遍历所有这些。

例如，我们可以创建一个`for...of`循环来遍历数组，如下所示:

```
const arr = [1, 2, 3];for (const a of arr) {
  console.log(a);
}
```

该循环将产生以下输出:

```
1
2
3
```

`for...of`循环的亮点在于能够遍历类似数组的对象，如节点列表和我们自己的可迭代对象。例如，如果我们有以下 HTML:

```
<p>
  foo
</p>
<p>
  bar
</p>
<p>
  baz
</p>
```

然后我们可以使用下面的 JavaScript

```
const nodeList = document.querySelectorAll('p');for (const element of nodeList) {
  console.log(element.textContent.trim());
}
```

然后，我们记录了以下内容:

```
foo
bar
baz
```

使用`for...of`循环，我们不必像常规的`for`循环那样写出索引来遍历 NodeList，这是唯一的选择，因为它不是一个数组，而是一个类似数组的对象。

此外，我们可以迭代用户定义的可迭代对象，如生成器和可迭代对象。例如，给定以下生成器:

```
function* generator() {
  yield 1;
  yield 2;
  yield 3;
}
```

我们可以使用`for...of`循环来遍历它，如下所示:

```
for (let a of generator()) {
  console.log(a);
}
```

然后我们得到:

```
1
2
3
```

此外，我们可以定义自己的 iterable 对象。只要它有`Symbol.iterator`方法，我们就可以用`for...of`循环遍历它。鉴于我们有:

```
const iterableObj = {
  *[Symbol.iterator]() {
    yield 1;
    yield 2;
    yield 3;
  }
}
```

我们可以用以下语句循环遍历它:

```
for (let a of iterableObj) {
  console.log(a);
}
```

`for...of`也可以循环遍历新对象，如`Set`和`Map` s:

```
const set = new Set([1, 1, 2, 2, 2, 3, 5, 4, 3]);for (const value of set) {
  console.log(value);
}
```

然后我们得到:

```
1
2
3
5
4
```

同样，我们可以循环浏览地图:

```
const map = new Map([
  ['a', 1],
  ['b', 2],
  ['c', 3]
]);for (const value of map) {
  console.log(value);
}
```

同样，我们可以用它来遍历字符串。例如，假设我们有以下字符串:

```
const str = 'foo';
```

我们可以循环遍历每个字符，如下所示:

```
for (let char of str) {
  console.log(char);
}
```

然后我们得到:

```
f
o
o
```

正如我们所看到的，它对于循环任何可以循环的东西非常有用。

![](img/a447b84ea8228fed8fefc93fcc197f97.png)

照片由[Ayo ogunsende](https://unsplash.com/@armedshutter?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 表演

我们可以通过遍历一个包含 10000 个条目的数字数组来测试这一点。该数组的构造如下:

```
let arr = [];
for (let i = 1; i <= 10000; i++) {
  arr.push(i);
}
```

为了测试每个循环的性能，我们使用大多数现代浏览器中可用的`console.time`和`console.timeEnd`方法。

使用数组的`forEach`方法，我们得到使用数组的`forEach`方法遍历每个条目需要 840.8779296875 毫秒。

使用`for...of`循环，遍历同一个数组的每个条目需要 843.900390625 毫秒。

最后，使用普通的`for`循环，遍历 10000 个数字需要 841.210205078125 毫秒。

使用长度缓存，如下所示:

```
const len = arr.length;
for (let i = 0; i < len; i++) {
  console.log(arr[i]);
}
```

更是快到了 593.16796875ms。

正如我们所见，普通的`for`循环要快得多。然而，对于少量数据的循环，我们可以忍受稍慢的性能。此外，我们不必编写任何额外的代码来遍历其他类型的可迭代对象，如`Map` s 和`Set` s。

这意味着对于非数组可迭代对象来说，`for...of`循环仍然更适合遍历这类对象。更少的代码意味着需要更少的处理时间。

然而，对于数组来说，如果我们遍历一个有很多条目的数组，那么普通的老式`for`循环可能是更好的选择。

# 我们应该使用 for…of 循环吗？

从我们所看到的来看，`for...of`循环适用于除大型数组之外的任何东西。与其他循环或现成的数组的`forEach`方法相比，它可以循环更多种类的对象。

它的设计比其他循环更加灵活和干净。

此外，只要它们是可迭代的，我们就不必担心我们所循环的内容，这与其他循环不同。

它唯一不擅长的是遍历大型数组。在这种情况下，使用缓存了数组长度的`for`循环。