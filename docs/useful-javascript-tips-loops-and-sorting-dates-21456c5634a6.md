# 有用的 JavaScript 技巧——循环和日期排序

> 原文：<https://levelup.gitconnected.com/useful-javascript-tips-loops-and-sorting-dates-21456c5634a6>

![](img/37cbdbd2305e99f5ca75e86e66f67441.png)

照片由[斯雷科·斯克罗比奇](https://unsplash.com/@sreckoskrobic?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何类型的应用程序一样，JavaScript 应用程序也必须写得很好。否则，我们以后会遇到各种各样的问题。

在本文中，我们将了解一些应该遵循的技巧，以便更快更好地编写 JavaScript 代码。

# 中断 for 循环

如果我们想早点结束一个`for`循环，我们可以使用`break`关键字。

例如，我们可以写:

```
for (const val of list) {
  console.log(val);
  if (val === 'b') {
    break;
  }
}
```

如果`val`是`'b'`，那么我们用`break`结束循环。

我们也可以用旧的`for`循环做同样的事情:

```
for (let i = 0; i < list.length; i++) {
  console.log(list[i]);
  if (list[i] === 'b') {
    break;
  }
}
```

我们做同样的事情，除了因为索引而变得更长。

# 检查对象是否为空

我们可以用`Object.entries`方法检查一个对象是否为空。它返回对象中非继承的键值对。

因此，我们可以这样写:

```
Object.entries(obj).length === 0
```

通过检查由`Object.entries`的长度返回的数组来检查`obj`是否为空。

最好通过添加以下内容来检查`obj`实际上是:

```
obj.constructor === Object
```

Lodash 也有`isEmpty`函数来做同样的事情。

我们可以写:

```
_.isEmpty(obj)
```

去做检查。

# 返回异步函数的结果

异步函数返回一个承诺，其中包含该承诺的解析值。

例如，如果我们写:

```
const getJSON = async () => {
  const response = await fetch('./file.json')
  return response
}
```

然后我们返回一个解析到`response`对象的承诺。然后，我们可以将它与其他异步函数一起使用，或者对它调用`then`。

# 检查 JavaScript 数组是否有给定值

我们可以使用`includes`方法来检查一个数组是否有给定值。

例如，我们可以写:

```
['red', 'green'].includes('red');
```

然后我们可以检查`'red'`是否在它被调用的数组中。它应该返回`true`，因为它包含在数组中。如果参数中的值不包含在内，那么它返回`false`。

# 对象析构时重命名字段

我们可以在析构对象时重命名字段。为此，我们可以使用`:`符号。

例如，我们可以写:

```
const person = {
  firstName: 'james',
  lastName: 'smith'
}const { firstName: name, lastName } = person;
```

然后我们把`firstName`改名为`name`。所以`name`就是`'james'`。

# 使用符号

我们可以使用符号来创建一个唯一的标识符。即使内容相同，创建的每个符号也不会相同。

例如:

```
Symbol() === Symbol()
```

返回`false`。

```
Symbol('foo') === Symbol('foo')
```

也是`false`。

我们可以将它们用作属性标识符。

例如，我们可以写:

```
const foo = Symbol('foo');
const obj = {
  [foo](){
    //...
  }
}
```

我们也可以写:

```
const foo = Symbol('foo');
class Bar {
  [foo](){
    //...
  }
}
```

我们可以在对象和类中使用符号作为标识符。

# 公共类字段

我们可以通过将公共类字段放在构造函数或方法中来添加它们。

例如，我们可以写:

```
class Foo {
  constructor() {
    this.count = 0
  }
}
```

# 按日期对数组排序

要按日期对数组进行排序，我们可以使用`sort`方法，计算两个日期条目之间的差值并返回它。

例如，给定以下数组:

```
const tasks = [{
    title: 'eat',
    date: new Date('2020-06-23')
  },
  {
    title: 'drink',
    date: new Date('2020-06-10')
  },
  {
    title: 'sleep',
    date: new Date('2020-06-22')
  }
]
```

我们可以按日期降序对其进行排序，如下所示:

```
tasks.sort((a, b) => +b.date - +a.date);
```

`sort`对数组进行排序。

`+`操作符将日期转换成 UNIX 时间戳，这样我们就可以计算它们之间的差异。

如果差值大于 1，则`sort`颠倒 2 个条目的顺序。

所以我们得到:

```
[
  {
    "title": "eat",
    "date": "2020-06-23T00:00:00.000Z"
  },
  {
    "title": "sleep",
    "date": "2020-06-22T00:00:00.000Z"
  },
  {
    "title": "drink",
    "date": "2020-06-10T00:00:00.000Z"
  }
]
```

作为`tasks`的值。

我们可以翻转`a`和`b`来按照日期升序对`tasks`进行排序。

要在排序前复制`tasks`，我们可以使用扩展运算符或`slice`，如下所示:

```
const sortedTasks = [...tasks];
sortedTasks.sort((a, b) => +b.date - +a.date);
```

或者:

```
const sortedTasks = tasks.slice();
sortedTasks.sort((a, b) => +b.date - +a.date);
```

![](img/ae196e3a74b2a9878d57fdbc79ada0e9.png)

[百合班克斯](https://unsplash.com/@lvnatikk?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们可以使用`break`提前结束一个循环。

为了用日期对对象进行排序，我们可以返回时间戳之间的差异。

此外，我们可以使用`Object.entries`返回一个键值对数组并使用`length`属性来检查对象是否为空。