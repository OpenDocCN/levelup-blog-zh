# 为什么 Lodash 还有用？

> 原文：<https://levelup.gitconnected.com/why-is-lodash-still-useful-1503a4215762>

![](img/2f5df2b43b541cdd5907de92bcdb9f87.png)

卡尔·安德森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

随着 ES6 和更高版本 JavaScript 的发布，有许多方法随之发布，扩展了语言的功能。例如，有新的数组和字符串方法，以及有用的操作符，如 spread 和 rest 操作符。

然而，JavaScript 仍然没有让 Lodash 这样的实用程序库过时，因为有许多有用的方法在 JavaScript 中仍然不可用。

在本文中，我们将研究 Lodash 的一些方法，这些方法仍然比普通的 JavaScript 更容易开发，包括`times`、`get`、`set`、`debounce`、`deburr`和`keyBy`方法。

# **_。次数**

`times`方法让我们多次调用一个函数，将每个函数调用的所有返回结果放入一个数组，然后返回它。

它有两个参数，一个是函数被调用的次数，另一个是要调用的函数。

例如，我们可以如下使用它:

```
import * as _ from "lodash";const getRandomInteger = () => Math.round(Math.random() * 100);
let result = _.times(5, getRandomInteger);
console.log(result);
```

那么我们可能会得到以下结果:

```
[16, 83, 35, 87, 41]
```

# **_。去抖**

我们可以使用`debounce`方法将一个函数的调用延迟一段指定的时间。用 JavaScript 实现这一点并不容易。

这对于事件处理非常有用，在这种情况下，我们希望等待事情完成，然后调用一个函数。例如，使用预输入搜索，反跳会等到用户完成输入后再进行 API 调用，从而消除服务器上不必要的点击。

例如，我们可以如下使用它。给定以下数字输入:

```
<input type="number" />
```

我们可以编写以下 JavaScript 代码:

```
import * as _ from "lodash";const checkPositiveNumber = e => {
  console.log(+e.target.value > 0);
};const numInput = document.querySelector("input[type=number]");
numInput.addEventListener("input", _.debounce(checkPositiveNumber, 600));
```

上面的代码有`checkPositiveNumber`函数，它检查输入的值是否为正。然后我们使用`debounce`方法，该方法在调用函数之前以毫秒为单位获取函数和延迟。

由`debouce`返回的函数具有与原始函数相同的参数和内容，除了在调用它之前延迟了给定的毫秒数。

# **_。获取**

`get`方法让我们以安全的方式访问对象的属性。也就是说，即使属性的路径不存在，它也将返回`undefined`或默认值，而不是使程序崩溃。

例如，给定以下对象:

```
const obj = {
  foo: {
    bar: { baz: { a: 3 } },
    foo: { b: 2 },
    baz: [1, 2, 3]
  }
};
```

我们可以通过以下方式访问`obj`的属性:

```
const result = _.get(obj, "foo.bar.baz.a", 1);
```

第一个参数是我们想要访问属性值的对象。第二个是属性的路径。最后一个参数是默认值。

对于`result`我们应该得到 3。

另一方面，如果路径不存在或者它是`undefined`，那么我们得到`undefined`或者返回一个默认值。

例如，如果我们有:

```
const result = _.get(obj, "foo.bar.a", 1);
```

然后我们为`result`得到 1。

如果我们不指定默认值，如下所示:

```
const result = _.get(obj, "foo.bar.a");
```

然后我们得到`undefined`。

在可选的链接操作符成为主流之前，没有办法安全地获取深度嵌套属性的值。

![](img/30c031d23b266cc483a43caace7a93b4.png)

照片由[豪尔赫·萨帕塔](https://unsplash.com/@jorgezapatag?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# _.设置

还有一个`set`方法可以给对象的属性赋值。例如，给定我们之前拥有的同一个对象:

```
const obj = {
  foo: {
    bar: { baz: { a: 3 } },
    foo: { b: 2 },
    baz: [1, 2, 3]
  }
};
```

我们可以通过编写以下代码来设置属性的值:

```
_.set(obj, "foo.bar.a", 1);
```

`obj`对象被就地改变。正如我们所见，它可以为尚不存在的属性设置值。原始对象没有`foo.bar.a`，它添加后自动将值设置为 1。

所以我们得到:

```
{
  "foo": {
    "bar": {
      "baz": {
        "a": 3
      },
      "a": 1
    },
    "foo": {
      "b": 2
    },
    "baz": [
      1,
      2,
      3
    ]
  }
}
```

即使嵌套对象不存在，它也能工作，所以如果我们写:

```
_.set(obj, "foo.foofoo.bar.a.b", 1);
```

我们得到:

```
{
  "foo": {
    "bar": {
      "baz": {
        "a": 3
      }
    },
    "foo": {
      "b": 2
    },
    "baz": [
      1,
      2,
      3
    ],
    "foofoo": {
      "bar": {
        "a": {
          "b": 1
        }
      }
    }
  }
}
```

# **_。去毛刺**

要从字符串字符中删除重音符号，我们可以使用`deburr`方法。它接受一个字符串并返回一个新字符串，其中有重音的字符被没有重音的字符替换。

例如，如果我们有`“S’il vous plaît”`:

```
const result = _.deburr("S'il vous plaît");
```

然后我们得到`result`就是`"S’il vous plait"`。“我”不再有重音。

# **_。按键操作**

`keyBy`方法接受一个数组和属性名，并返回一个对象，将属性值作为对象的键。

例如，如果我们有以下数组:

```
const people = [
  { name: "Joe", age: 20 },
  { name: "Jane", age: 18 },
  { name: "Mary", age: 20 }
];
```

那么我们可以如下使用`keyBy`:

```
const results = _.keyBy(people, "name");
```

然后`results`会有以下内容:

```
{
  "Joe": {
    "name": "Joe",
    "age": 20
  },
  "Jane": {
    "name": "Jane",
    "age": 18
  },
  "Mary": {
    "name": "Mary",
    "age": 20
  }
}
```

然后，我们通过编写以下内容获得名为`'Joe'`的对象:

```
results['Joe']
```

如果不编写多行代码，用普通的 JavaScript 无法轻松做到这一点。

# 结论

Lodash 有许多有用的函数，它们没有像这些方法一样容易使用的等价函数。

有一种在一行中多次调用一个函数的`times`方法。`debounce`函数返回一个新函数，它具有与原始函数相同的签名和代码，但是它在被调用之前被延迟了给定的毫秒数。

为了安全地访问和设置对象属性和值，有`get`和`set`方法，当我们访问不存在或有值`undefined`的属性路径时，它们不会崩溃。

然后是用这些字符的非重音版本替换重音字符的`deburr`方法。

最后，有一个`keyBy`方法将一个数组传递到一个对象中，该对象将每个条目的给定属性值作为键，并将具有给定属性值的条目作为这些键的值。