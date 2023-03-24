# 通过实现 Lodash 方法学习 JavaScript 对象和函数

> 原文：<https://levelup.gitconnected.com/learning-javascript-by-implementing-lodash-methods-objects-and-functions-b2b200996ae>

![](img/23d3d362a95afcd685dbf8e9d5eaec43.png)

[大卫·克洛德](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Lodash 是一个非常有用的工具库，让我们可以轻松地处理对象和数组。

然而，现在 JavaScript 标准库正在赶上诸如 Lodash 之类的库，我们可以用简单的方法实现许多功能。

在本文中，我们将研究一些 Lodash 对象和函数方法，我们可以用自己的普通 JavaScript 实现来替换它们。

# `unary`

Lodash `unary`方法返回一个只用一个参数调用原始函数的函数。

我们可以创建自己的函数，该函数返回一个调用原始函数的函数，如下所示:

```
const unary = (fn) => (val) => fn(val);
```

在上面的代码中，我们有自己的`unary`函数，它接受一个返回`(val) => fn(val)`的函数，这个函数只接受一个参数，然后用那个参数调用`fn`。

那么我们可以这样称呼它:

```
const result = ['6', '8', '10'].map(unary(parseInt));
```

我们得到了:

```
[
  6,
  8,
  10
]
```

作为`result`的值。

# 包

Lodash `wrap`方法采用一个`wrapper`函数和另一个函数`fn`，我们希望使用`wrapper`作为第一个参数来调用这个函数。

它返回一个函数，将值`val`作为参数，并调用第二个参数中的函数，将包装器作为第一个参数，将`val`作为第二个参数。

例如，我们可以实现自己的`wrap`函数，如下所示:

```
const wrap = (wrapper, fn) => (val) => fn(wrapper, val)
```

在上面的代码中，`wrap`函数取`wrapper`和`fn`，这两个是函数。然后我们返回一个函数，它接受一个参数，这个参数是`val`，然后用`wrapper`和`val`调用`fn`。

然后，我们可以如下调用它，将 Lodash `_.escape`方法作为第一个参数，并使用我们自己的函数将文本包装在 p 标签周围，如下所示:

```
const p = wrap(_.escape, (func, text) => `<p>${func(text)}</p>`);
```

在上面的代码中，`escape`方法对文本进行了转义，我们自己的函数包装了`func(text)`，其中`func`是`escape`方法，因为它是第一个参数，`text`是我们将传递给返回的`p`函数的文本。

那么我们可以如下调用`p`:

```
const result = p('foo bar');
```

我们得到`result`是`<p>foo bar</p>`。

![](img/eb483ba5b25dfb3fb3655bc2e45a855b.png)

照片由[雷切尔·希斯科](https://unsplash.com/@rachelhisko?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# `forIn`

Lodash `forIn`方法遍历对象的自有和继承的可枚举字符串键控属性，并对每个属性运行一个`iteratee`函数。

`iteratee`是一个带`value`、`key`和`object`参数的函数，我们可以通过显式返回`false`来停止循环

我们可以如下实现自己的`forIn`功能:

```
const forIn = (object, iteratee) => {
  for (const key in object) {
    const result = iteratee(object[key], key, object);
    if (result === false) {
      break;
    }
  }
}
```

在上面的代码中，我们有接受一个`object`和一个`iteratee`函数的`forIn`函数。

然后在函数内部，我们使用了`for...in`循环来遍历`object`的所有可枚举 own 和可枚举 string 键。

在循环内部，我们用值调用`iteratee`函数，这个值是通过使用`object[key]`、`key`本身和`object`得到的。我们将`iteratee`的返回值赋给`result`。

那么如果`result`是`false`，那么我们就打破循环。否则，它将继续运行。

然后我们可以如下调用我们的`forIn`函数:

```
function Foo() {
  this.a = 1;
  this.b = 2;
}Foo.prototype.c = 3;forIn(new Foo(), (value, key) => console.log(key));
```

在上面的代码中，我们有一个`Foo`构造函数，它有两个自己的属性`a`和`b`。它还有一个额外的继承属性`c`。

然后我们用一个`Foo`实例调用`forIn`，并传入一个记录了`Foo`实例的键的回调函数。

最后，我们应该看到控制台日志输出中记录的`a`、`b`和`c`。

# 结论

`unary`和`wrap` Lodash 方法都返回一个接受一些参数的函数，并调用作为参数从返回的函数传入的原始函数。

Lodash 的`forIn`方法类似于`for...in`循环，只是我们可以用我们的`iteratee`函数返回`false`来中断循环。在每次迭代中运行`iteratee`，直到它返回`false`。