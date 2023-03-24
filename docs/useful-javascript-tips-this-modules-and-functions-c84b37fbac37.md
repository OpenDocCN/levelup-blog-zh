# 有用的 JavaScript 技巧——This、模块和函数

> 原文：<https://levelup.gitconnected.com/useful-javascript-tips-this-modules-and-functions-c84b37fbac37>

![](img/6850c6dd21f6476497be2923e5789a26.png)

照片由 [Kouji 鹤](https://unsplash.com/@pafuxu?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何类型的应用程序一样，JavaScript 应用程序也必须写得很好。否则，我们以后会遇到各种各样的问题。

在本文中，我们将了解一些应该遵循的技巧，以便更快更好地编写 JavaScript 代码。

# 返回对象以启用函数链

我们可以在方法中返回对象来链接函数。

例如，我们可以这样写:

```
const obj = {
  height: 0,
  width: 0,
  setHeight(height) {
    this.height = height;
    return this;
  },
  setWidth(width) {
    this.width = width;
    return this;
  }
}
```

然后我们可以通过运行来调用它:

```
obj.setHeight(100).setWidth(300);
```

我们得到`obj.height`是 100，`obj.width`是 300。

同样，我们也可以在课堂上这样做。

例如，我们可以写:

```
class Obj {
  setHeight(height) {
    this.height = height;
    return this;
  } setWidth(width) {
    this.width = width;
    return this;
  }
}
```

然后我们可以写:

```
new Obj().setHeight(100).setWidth(300)
```

# 安全字符串串联

我们可以使用`concat`方法来连接字符串。

例如，我们可以写:

```
const one = 1;
const two = 2;
const three = '3';
const result = ''.concat(one, two, three);
```

然后我们得到`result`是`'123'`。

# 运行不需要的模块

我们可以检查`module.parent`属性，看看一个 JavaScript 文件是否被另一个文件所需要。

例如，我们可以写:

```
if (!module.parent) {
  // run code ...
} else {
  module.exports = app;
}
```

如果`module.parent`是 falsy，那么我们运行`if`块中的代码。

否则，我们导出模块成员，以便它们可以被需要。

# 向回调函数传递参数

如果我们创建一个函数返回包含参数的回调，我们可以将参数传递给回调函数。

例如，我们可以写:

```
const callback = (a, b) => {
  return () => {
    console.log(a + b);
  }
}
```

然后我们可以写:

```
const x = 1, y = 2;
document.getElementById('foo').addEventListener('click', callback(x, y));
```

我们调用了`callback`来返回一个将两个参数相加的函数。

这意味着我们可以在回调中包含这些参数。

# 用 indexOf 来检查是否包含了什么

我们可以使用`indexOf`方法来检查一个字符串是否有子串。

例如，我们可以写:

```
'something'.indexOf('thing') !== -1
```

或者:

```
'something'.indexOf('thing') >= 0
```

`indexOf`如果没有找到子串，返回-1。

如果找到了，则返回子串的第一个索引。

同样，我们可以使用`includes`方法来做同样的事情:

```
'something'.includes('thing');
```

如果找到子串，则返回`true`。

所以，它会返回`true`。

同样的方法也在数组中。

所以我们可以写:

```
['foo', 'bar'].indexOf('foo') !== -1
```

或者:“

```
['foo', 'bar'].indexOf('foo') >= 0
```

或者:

```
['foo', 'bar'].includes('foo')
```

# 箭头功能

如果函数不需要自己的`this`，我们可以使用箭头函数来缩短代码。

例如，我们可以写:

```
const squared = arr.map((x) => x ** 2);
```

我们有一个单语句箭头函数的隐式返回。

用它们要短得多。

然而，`bind`、`call`或`apply`不会与它们一起工作，因为它们没有自己的`this`。

此外，我们不能在其中使用`arguments`对象，但这并没有多大损失。

# 衡量我们代码的性能

我们可以使用`console.time`和`console.timeEnd`来衡量我们代码的性能。

例如，我们可以写:

```
console.time("populate array");
let arr = Array(100),
    len = arr.length,
    i;

for (i = 0; i < len; i++) {
    arr[i] = new Object();
};
console.timeEnd("populate array");
```

我们从`console.time`开始测量。

然后我们运行想要测量性能的代码。

然后我们调用`console.timeEnd`停止测量。

# 使参数成为强制性的

如果我们在返回默认参数值的函数中抛出错误，我们可以将参数设置为强制参数。

例如，我们可以写:

```
const err = ( message ) => {
  throw new Error( message );
}

const getSum = (a = err('a is required'), b = err('b is not required')) => a + b
```

我们可以在分配默认参数值时调用函数。

因此，如果没有通过运行`err`函数设置错误，我们可以抛出错误。

`err`仅在未设置参数时运行。

![](img/88920a73c3b1f2cc51610fa02cba9a5d.png)

Vincent van Zalinge 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

如果我们在对象或类方法中返回`this`,我们可以创建可链接的函数。

此外，如果我们运行一个在赋值操作符右侧抛出错误的函数，我们可以确保参数被设置。