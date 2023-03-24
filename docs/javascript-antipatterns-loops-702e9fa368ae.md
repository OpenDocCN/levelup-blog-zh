# JavaScript 反模式—循环

> 原文：<https://levelup.gitconnected.com/javascript-antipatterns-loops-702e9fa368ae>

![](img/c6802c08d0118d07202f0544ba7138f3.png)

rafael Souza 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 让我们可以做很多事情。它的语法有时过于宽容。

在本文中，我们将研究一些在编写 JavaScript 循环时应该避免的反模式。

# 对于循环

for 循环对于迭代数组和类似数组的对象非常方便。

例如，我们可以写:

```
const arr = [1, 2, 3];
for (let i = 0; i < arr.length; i++) {
  // do something with arr[i]
}
```

这将循环`arr`数组的索引，并让我们对每个条目做一些事情。

它也适用于类似数组的对象。节点列表是类似数组的对象。

我们可以调用以下方法来获得一个包含多个 DOM 元素的 Nodelist:

*   `document.querySelectorAll()`
*   `document.getElementsByName()`
*   `document.getElementsByClassName()`
*   `document.getElementsByTagName()`

`document.querySelectorAll()`是最通用的，因为它接受任何 CSS 选择器。

`document.getElementsByName`仅返回具有给定名称属性的项目。

`document.getElementsByClassName`只返回给定类名的项目。

`document.getElementsByTagName`返回具有给定标记名的项目。

此外，文档的以下属性具有特定的项目

*   `document.images` —页面上的所有`img` 元素
*   `document.links` —所有`a`元素
*   `document.forms` —所有表格
*   `document.forms[0].elements` —表单中的所有字段

我们可以如下循环遍历它们:

```
for (let i = 0; i < document.links.length; i++) {
  // do something with document.links[i]
}
```

使用`for`循环，我们可以在第一个表达式中定义所有的初始条件，因此我们可以写:

```
for (let i = 0, max = arr.length; i < max; i++) {
  // do something with arr[i]
}
```

这与以下内容相同:

```
for (let i = 0; i < arr.length; i++) {
  // do something with arr[i]
}
```

`i++`与`i = i + 1`或`i += 1`相同。

如果我们将它分配给某样东西，可能会很棘手。

`i++`返回`i`的原始值，而不是更新值。

所以如果我们有:

```
let i = 1;
const a = i++;
```

我们得到`a`是 1。

# for-in 循环

for-in 循环对于遍历对象及其原型的关键点非常有用。

因此，如果我们只想遍历对象的非继承键，我们必须使用`hasOwnProperty`方法。

例如，我们可以这样使用它:

```
const obj = {
  a: 1,
  b: 2
}for (const key in obj) {
  if (obj.hasOwnProperty(key)) {
    //...
  }
}
```

这将只遍历非继承的键，因为`hasOwnProperty`会检查。

我们也可以如下调用`hasOwnProperty`:

```
const obj = {
  a: 1,
  b: 2
}for (const key in obj) {
  if (Object.prototype.hasOwnProperty.call(obj, key)) {
    //...
  }
}
```

这将避免当`obj`对象重新定义`hasOwnProperty`为其他对象时出现的问题。

我们也可以写:

```
const obj = {
  a: 1,
  b: 2
}const hasOwn = Object.prototype.hasOwnProperty;
for (const key in obj) {
  if (hasOwn.call(obj, key)) {
    //...
  }
}
```

来缓存`hasOwnProperty` 方法。

![](img/2b15d414becb504ec8fc6a6925c5937b.png)

照片由[莎拉·奥利芙](https://unsplash.com/@taffy_apple?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# for-of 循环

for-of 循环比其他循环更加通用。这对于遍历数组和类似数组的对象很有用。

因此，对于遍历这些对象，这是 for 循环的一个很好的替代方法。

它还支持上面列出的所有`document`属性，以及像集合和映射这样的新数据结构。

我们可以如下使用它:

```
for (const link of document.links) {
  // do something with link
}
```

对于数组，我们可以写:

```
for (const a of arr) {
  // do something with a
}
```

它还使用析构赋值语法:

```
for (const { foo, bar } of arr) {
  // do something with foo and bar
}
```

它还适用于集合和地图:

```
for (const s of set) {
  // do something with a
}
```

对于地图，我们可以写:

```
const map = new Map([
  ['a', 1],
  ['b', 2]
])for (const [key, value] of map) {
  // do something with key and value
}
```

# 结论

for 循环非常适合在数组和类似数组的对象中循环。

然而，for 循环通过让我们循环遍历任何可迭代的对象而击败了它。

for-in 循环在遍历对象中的键时用途有限。如果我们也想遍历他们原型的键，那么我们可以使用它。