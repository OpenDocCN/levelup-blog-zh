# JavaScript 面试问题—数组

> 原文：<https://levelup.gitconnected.com/javascript-interview-questions-arrays-246a1a125752>

![](img/7ab1521084cbd75d82f7bab424af6b2c.png)

照片由 [Vivint Solar](https://unsplash.com/@vivintsolar?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

为了得到一份前端开发人员的工作，我们需要搞定编码面试。

在本文中，我们将研究一些 JavaScript 数组问题。

# 手动实现`Array.prototype.map`方法。

`Array.prototype.map`方法通过调用回调函数将每个数组条目映射到新条目。

它返回一个包含映射值的新数组。

我们可以如下实现它:

```
const map = (arr, callback) => {
  if (!Array.isArray(arr)) {
    return [];
  }
  const newArr = [];
  for (let i = 0, len = arr.length; i < len; i++) {
    newArr.push(callback(arr[i], i, arr));
  }
  return newArr;
}
```

首先，我们检查`arr`是否是一个数组，如果不是，我们返回一个空数组。

在上面的代码中，我们使用了`map`方法，该方法遍历每个数组条目，并使用传入的数组条目、索引和原始数组调用一个`callback`。每个条目都被推送到`newArr`数组。

然后它返回`newArr`数组，其中包含映射的条目。

# 手动执行`Array.prototype.filter`方法。

`Array.prototype.filter`方法返回一个新数组，该数组从满足回调谓词的原始数组中提取元素，将这些条目推入一个新数组，然后返回它。

我们可以按如下方式实现:

```
const filter = (arr, callback) => {
  if (!Array.isArray(arr)) {
    return [];
  }
  const newArr = [];
  for (let i = 0, len = arr.length; i < len; i++) {
    if (callback(arr[i], i, arr)) {
      newArr.push(arr[i]);
    }
  }
  return newArr;
}
```

在上面的代码中，我们首先检查`arr`是否是一个数组。如果不是，我们返回一个空数组。

然后我们循环遍历`arr`，然后使用`if`块检查`callback`调用是否返回`true`，然后我们将这些条目推送到`newArr`并返回。

# 手动执行`Array.prototype.reduce`方法。

`Array.prototype.reduce`方法通过反复调用回调将数组条目合并成一个值，从而将数组条目合并成一个。

例如，我们可以这样写:

```
const reduce = (arr, reduceCallback, initialValue) => {
  if (!Array.isArray(arr)) {
    return;
  }  
  let val = initialValue || 0;
  for (let i = 0, len = arr.length; i < len; i++) {    
     val = reduceCallback(val, arr[i]);    
  }
  return val;
}
```

我们首先检查`arr`是否是一个数组，如果不是，我们返回。

然后我们设置`val`为`initialValue`如果存在或者为 0。

接下来，我们遍历数组，然后使用`reduceCallback`来组合`val`和`arr[i]`，返回新值并将其分配给`val`。

一旦循环完成，我们返回`val`。

![](img/dfb2b44253803811f07849737df1f5e5.png)

詹·西奥多在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# arguments 对象是什么？

`arguments`对象是一个类似数组的对象，它返回传递给传统函数的参数。

它不适用于箭头函数。

它是一个类似数组的对象，因为它的条目可以被索引访问，并且它有一个`length`属性。`arguments`没有数组方法。

此外，它可以被`for...of`循环遍历，并通过 spread 操作符转换成一个数组。

例如，如果我们写:

```
function logArgs() {
  console.log(arguments)
}logArgs(1, 2, 3, 4, 5);
```

然后我们会看到这样的情况:

```
Arguments(5) [1, 2, 3, 4, 5, callee: ƒ, Symbol(Symbol.iterator): ƒ]
```

从`console.log`开始。

我们可以看到条目和`Symbol.iterator`方法，所以我们知道它是一个可迭代的对象。

我们可以使用 spread 运算符将其转换为数组，如下所示:

```
function logArgs() {
  console.log([...arguments])
}logArgs(1, 2, 3, 4, 5);
```

然后`console.log`给出`[1, 2, 3, 4, 5]`，是一个规则数组。

对于箭头函数，我们使用 rest 运算符来获取所有参数，如下所示:

```
const logArgs = (...args) => {
  console.log(args)
}logArgs(1, 2, 3, 4, 5);
```

我们看到`[1, 2, 3, 4, 5]`被记录。

# 如何在数组的开头添加一个元素？

我们可以使用`Array.prototype.unshift`方法在数组的开头添加一个元素。

例如，我们可以写:

```
const arr = [2, 3, 4];
arr.unshift(1);
```

那么`arr`就是`[1, 2, 3, 4]`。

扩展运算符也适用于此:

```
let arr = [2, 3, 4];
arr = [1, ...arr];
```

请注意，我们将`const`更改为`let`，因为我们分配了一个新值。

# 如何在数组末尾添加一个元素？

我们可以使用`push`方法在数组末尾添加一个条目。

例如，我们可以写:

```
const arr = [2, 3, 4];
arr.push(1);
```

那么`arr`就是`[2, 3, 4, 1]`。

扩展运算符也适用于此:

```
let arr = [2, 3, 4];
arr = [...arr, 1];
```

请注意，我们将`const`更改为`let`，因为我们分配了一个新值。

# 结论

我们通过从头开始实现数组方法来学习更多关于 JavaScript 数组的知识，并练习使用它们进行常见的操作。