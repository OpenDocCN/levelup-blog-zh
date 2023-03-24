# 通过实现 Lodash 方法学习 JavaScript 对象遍历

> 原文：<https://levelup.gitconnected.com/learning-javascript-by-implementing-lodash-methods-object-traversal-5d2a7aa7ad03>

![](img/a544edc8d20a69914e30c9ef322a9698.png)

照片由[加博尔·szűts](https://unsplash.com/@szutsi?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Lodash 是一个非常有用的工具库，让我们可以轻松地处理对象和数组。

然而，现在 JavaScript 标准库正在赶上诸如 Lodash 之类的库，我们可以用简单的方法实现许多功能。

在本文中，我们将研究如何实现一些遍历对象键的方法。

# `forOwn`

Lodash `forOwn`方法遍历对象自己的可枚举字符串键属性，并对每个属性运行`iteratee`函数。

`iteratee`带 3 个参数，分别是`value`、`key`和`object`。其中`value`是被循环的属性的值。`key`是属性键字符串，`object`是被遍历的对象。

我们可以用`Object.keys`方法将所有对象的键作为一个数组，因此我们可以如下循环遍历这些键:

```
const forOwn = (obj, iteratee) => {
  for (const key of Object.keys(obj)) {
    const result = iteratee(obj[key], key, obj);
    if (result === false) {
      break;
    }
  }
}
```

在上面的代码中，我们用`obj`调用`Object.keys`来获得`obj`自己的密钥。然后我们通过引用`obj[key]`作为第一个参数、`key`作为第二个参数、`obj`作为第三个参数来调用`iteratee`的值。

在每次迭代中调用`iteratee`，其返回值被设置为`result`的值。

那么当我们这样称呼它的时候:

```
function Foo() {
  this.a = 1;
  this.b = 2;
}Foo.prototype.c = 3;forOwn(new Foo(), (value, key) => console.log(key));
```

我们从控制台日志输出中得到`a`和`b`，因为`a`和`b`是`Foo`的实例自身的属性，但是`c`是从`Foo`的原型继承的属性。

# `forOwnRight`

Lodash 的`forOwnRight`方法与`forOwn`相似，只是它以相反的顺序遍历对象的键。

我们可以通过在由`Object.keys`返回的数组上调用`reverse`来反向循环按键，从而稍微改变`forOwn`的实现。

例如，我们可以这样实现:

```
const forOwnRight = (obj, iteratee) => {
  for (const key of Object.keys(obj).reverse()) {
    const result = iteratee(obj[key], key, obj);
    if (result === false) {
      break;
    }
  }
}
```

在上面的代码中，除了我们在`Object.keys`之后调用了`reverse`之外，我们的实现与`forOwn`相同。

那么当我们这样称呼它的时候:

```
function Foo() {
  this.a = 1;
  this.b = 2;
}Foo.prototype.c = 3;forOwnRight(new Foo(), (value, key) => console.log(key));
```

我们看到`b`然后`a`被记录在控制台日志输出中。

![](img/9c1dc5b82248a1bd9b5de6a0226fc1b0.png)

[大卫·克洛德](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# `functions`

`functions`方法返回一个数组，该数组包含函数值属性的属性名。

我们可以通过检查给定的属性是否具有 function 类型并将其放入返回的数组中来自己实现它。

例如，我们可以这样做:

```
const functions = (obj) => Object.entries(obj).filter(([key, value]) => typeof value === 'function').map(([key]) => key);
```

在上面的代码中，我们使用`Object.entries`获取`obj`的每个属性的键和值，作为一个嵌套数组，每个条目作为一个键和值的数组。

然后，我们通过在每个条目上析构给定的`key`和`value`来对`Object.entries`返回的内容调用`filter`，然后通过用`typeof`操作符检查`value`来返回任何具有`'function'`类型的值。

接下来，我们调用数组中的`key`构造的`map` on，只返回`keys`，并将它们放入返回的数组中。

然后当我们有了一个`Foo`构造函数并用`Foo`实例调用它，如下所示:

```
function Foo() {
  this.a = () => 'a';
  this.b = () => 'b';
}Foo.prototype.c = () => 'c';const result = functions(new Foo());
```

我们得到`result`是`[“a”, “b”]`，因为在`Foo`实例中，只有`'a'`和`'b'`是函数值自身的属性。

# 结论

为了实现`forOwn`和`forOwnRight` Lodash 方法，我们可以使用`Object.keys`方法获取所有的键，然后使用`for...of`循环遍历给定对象的所有键。

通过调用`Object.entries`可以实现`functions`方法，以数组的形式获取对象的键和值。

然后我们可以用`typeof`操作符调用`filter`方法来检查值类型为`'function'`的方法。然后我们可以得到函数值属性的键，并用`map`返回一个数组。