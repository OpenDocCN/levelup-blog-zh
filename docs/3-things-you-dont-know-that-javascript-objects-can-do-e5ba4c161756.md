# JavaScript 对象能做的 3 件你不知道的事

> 原文：<https://levelup.gitconnected.com/3-things-you-dont-know-that-javascript-objects-can-do-e5ba4c161756>

![](img/6891ba56d572b8ec7a693737d318c588.png)

照片由[迈克·多纳](https://unsplash.com/@dorner?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

除了普通的对象属性赋值和遍历之外，我们还可以对 JavaScript 对象进行许多其他操作。

在本文中，我们将研究如何使用它们，包括访问内部属性、操作属性描述符和继承只读属性。

# 1.访问内部属性

JavaScript 对象具有不能以通常方式访问的内部插槽或属性。

它们的属性名用双方括号`[[]]`括起来，在创建对象时可用。

它们不能动态添加到现有对象中。

它在某些内置的 JavaScript 对象中可用，它们按照 ECMAScript 规范的规定存储内部状态。

有两种内部插槽，它们或者是操作对象的方法，或者是存储数据。

例如，以下属性是插槽:

*   `[[Prototype]]` —一个对象的原型，可以是`null`或一个对象
*   `[[Extensible]]` —布尔型，存储对象是否可以添加属性
*   `[[PrivateFieldValues]]` —用于管理私有类字段

# 2.属性描述符

JavaScript 对象中的每个属性都有一个属性描述符。它用于指定值，是可写的、可配置的还是可枚举的。

`value`描述符是属性值。例如，如果我们有以下对象:

```
let foo = {
  a: 1
}
```

那么`a`的`value` 属性描述符就是`1`。

`writable`表示属性值是否可以改变。默认值是`true`，这意味着属性是可写的。但是，我们可以用各种方法将其设置为不可写。

`configurable`表示对象的属性是否可以删除，或者其属性描述符是否可以改变。默认值是`true`，这意味着它是可配置的。

`enumerable`表示可以被`for...in`回路循环通过。它的默认值是`true`，这意味着当我们用`for...in`循环遍历它时，我们会看到它。

在将属性键添加到返回的数组之前，`Object.keys`方法还会检查`enumerable`描述符。然而，`Reflect.ownKeys`方法并不检查这个属性描述符，而是返回所有自己的属性键。

属性描述符有另外的方法，`get`和`set`分别用于获取和设置值。

我们可以用使用`Object.defineProperty`方法设置的描述符创建新的属性，如下所示:

```
let foo = {
  a: 1
}Object.defineProperty(foo, 'b', {
  value: 2,
  writable: true,
  enumerable: true,
  configurable: true,
});
```

然后我们得到`foo`的新值是`{a: 1, b: 2}`。

我们还可以使用`defineProperty`来改变现有属性的描述符。例如，我们可以写:

```
let foo = {
  a: 1
}Object.defineProperty(foo, 'a', {
  value: 2,
  writable: false,
  enumerable: true,
  configurable: true,
});
```

然后当我们试图给`foo.a`赋值时，比如:

```
foo.a = 2;
```

如果关闭了严格模式，浏览器将忽略，否则将抛出一个错误。

我们也可以使用`defineProperty`将一个属性转换成一个 getter，如下所示:

```
'use strict'
let foo = {
  a: 1
}Object.defineProperty(foo, 'b', {
  get() {
    return 1;
  },
});
```

然后当我们写下:

```
foo.b = 2;
```

当严格模式打开时，我们会得到一个错误，因为`b`属性是一个 getter 属性。不能将 Getter 属性重新分配给不同的值。

![](img/6893a4a1e0dd2326e901b7f4227430f7.png)

照片由 [Namroud Gorguis](https://unsplash.com/@namroud?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 3.不能将继承的只读属性分配给

不能为对象的继承只读属性赋值。这是有意义的，因为我们这样设置它，它是继承的，所以它应该传播到继承该属性的对象。

我们可以使用`Object.create`创建一个从原型对象继承属性的对象，如下所示:

```
const proto = Object.defineProperties({}, {
  a: {
    value: 1,
    writable: false,
  }
});const foo = Object.create(proto);
```

在上面的代码中，我们将`proto.a`的`writable`属性描述符设置为`false`，这样我们就不能再给它赋值了。

我们在最后一行用`Object.create`方法创建一个从`proto`继承的`foo`对象。

然后当我们写下:

```
foo.a = 2;
```

在严格模式下我们会得到一个错误，否则会被忽略。

# 结论

我们可以用 JavaScript 对象做很多我们可能不知道的事情。

首先，一些 JavaScript 对象，如内置的浏览器对象，有内部的槽或属性，用双方括号括起来，保存内部状态，以后不能动态添加。

JavaScript 对象属性也有属性描述符，这让我们可以控制它们的值，以及它们是否可以被设置，或者它们的属性描述符是否可以改变，等等。

我们可以用`defineProperty`改变一个属性的属性描述符。它还用于添加一个新属性及其属性描述符。

最后，继承的只读属性保持只读，这是有意义的，因为它是从父原型对象继承的。