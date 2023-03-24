# JavaScript 对象的可枚举性

> 原文：<https://levelup.gitconnected.com/enumerability-of-javascript-objects-4388993f03d7>

![](img/92af51f8213482cd3d72e8695d5f071a.png)

照片由[雅各布·欧文](https://unsplash.com/@jakobowens1?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 对象是动态的。可以动态地添加和删除这些属性，并且可以用描述它们如何工作的各种属性描述符来设置它们。

在本文中，我们将研究 JavaScript 对象属性的可枚举性，这是属性的描述符之一。

# ES6 中的可枚举性

可枚举性是对象属性的描述符。每个对象有零个或多个属性。每个属性都有一个键和另外 3 个属性。

描述符存储关于属性的信息。所有属性都具有以下属性:

*   `enumerable` —一个布尔属性，如果它被设置为`false`，则在某些操作中隐藏该属性
*   `configurabvle` —将此项设置为`false`可防止更改属性描述符。除了`value`之外的描述符不能更改，属性不能删除。

正常属性有以下描述符:

*   `value` —房产的价值
*   `writable` —控制该值是否可以更改。

访问者具有以下属性:

*   `get` —保存一个 getter，这是一个函数
*   `set` —保存一个 setter，这也是一个函数

# 受可枚举性影响的构造

下面的代码受属性的可枚举性影响。`for-in`循环只遍历可枚举的键。

`Object.keys`返回可枚举且不从其“原型”继承的字符串键。

`JSON.stringify` only stringifies 仅可枚举带有字符串键的属性。

`Object.assign`只复制可枚举的自身属性。字符串和符号键都被复制。

# 可枚举性的用例

可枚举性不是一个非常有用的特性。它不会对所有操作隐藏属性。

然而，它对于隐藏像`for...in`循环这样的遗留代码仍然很有用。

此外，它还用于防止属性被`jQuery`的`extend`方法复制。`extend`将继承的属性作为非继承的属性复制到返回的对象中，这很可能不是我们想要的。

`Object.assign`在合并或复制对象时也会忽略不可枚举的属性。这更有用，因为它不会混淆非继承属性和继承属性。

# 将属性标记为私有

使用可枚举性描述符将属性标记为私有并不是很好。这是因为我们仍然可以以某种方式检索它们。像`for...in`这样的操作会跳过不可枚举的属性，但是`Object.getOwnPropertyNames`除了返回可枚举的键之外，还会返回不可枚举的键。

从外部代码中获取不可枚举的属性非常容易，所以不可枚举不会使属性私有。

# 对`JSON.stringify()`隐藏自己的属性

`JSON.stringify`跳过不可枚举的属性不被字符串化。然而，我们也可以给我们的对象添加一个`toJSON`方法来指定我们应该如何字符串化我们的对象。

例如，我们可以写:

```
const obj = {
  foo: 1,
  toJSON() {
    return {
      bar: 2
    };
  },
};
```

然后当我们写下:

```
JSON.stringify(obj)
```

它将返回`{“bar”:2}`，这是我们在`toJSON`中返回的。

![](img/198d1f323f00ce7b44da0754e5ef7302.png)

照片由 [Garrett Karoski](https://unsplash.com/@garrettk9?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 命名不一致

有些方法返回不可枚举的属性，有些不返回。例如，`Object.keys`忽略不可枚举的属性。然而，`Object.getOwnPropertyNames`列出了所有的属性名。

`Reflect.ownKeys`忽略可枚举性，返回所有属性的键。

# 结论

可枚举性是一个 d 属性描述符，它指定一个属性如果是`false`的话，可能会对某些操作隐藏。

它不适合隐藏属性，因为它很容易被一些方法访问。

因此，最好不要处理属性的可枚举性，除非我们正在处理非常旧的代码。