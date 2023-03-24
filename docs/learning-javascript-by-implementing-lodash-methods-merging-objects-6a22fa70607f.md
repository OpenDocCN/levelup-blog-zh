# 通过实现 Lodash 方法学习 JavaScript 合并对象

> 原文：<https://levelup.gitconnected.com/learning-javascript-by-implementing-lodash-methods-merging-objects-6a22fa70607f>

![](img/d70dd0ba473e0aa85e2ff25889ff48e8.png)

杰罗姆·霍伊泽在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Lodash 是一个非常有用的工具库，让我们可以轻松地处理对象和数组。

然而，现在 JavaScript 标准库正在赶上诸如 Lodash 之类的库，我们可以用简单的方法实现许多功能。

在本文中，我们将研究如何使用 spread 操作符和普通 JavaScript 对象操作符和方法来实现一些 Lodash 对象合并方法。

# `assign`

Lodash 的`assign`方法将源对象的所有自己的可枚举字符串关键字属性分配给目标对象。

源对象从左到右应用。普通 JavaScript 的标准库中已经有了`Object.assign`方法，所以我们可以用它来实现这个方法。

`assign`方法改变原始对象，将其他对象的键添加到其中。

例如，我们可以这样做:

```
const assign = (obj, ...sources) => {
  for (const s of sources) {
    obj = {
      ...obj,
      ...s
    };
  }
  return obj;
}
```

在上面的代码中，我们有自己的`assign`函数，它采用与 Lodash 的`assign`方法相同的参数。它取`obj`，是原物。`sources`是我们想要合并到`obj`中的对象。

然后我们有一个`for...of`循环，它循环通过`sources`，这是一个源自我们传入的参数的对象数组，因为我们对它应用了 rest 操作符。

在循环中，我们将 spread 操作符应用于`obj`和`sources`中的每个对象，将它们合并成一个。然后我们返回`obj`。

我们也可以使用普通 JavaScript 的`Object.assign`方法来做同样的事情，如下所示:

```
const assign = (obj, ...sources) => {
  return Object.assign(obj, ...sources);
}
```

我们只是以`obj`作为第一个参数调用`Object.assign`，而`sources`数组作为`assign`方法后续参数的参数展开。

最后，我们可以如下调用其中任何一个:

```
function Foo() {
  this.a = 1;
}function Bar() {
  this.c = 3;
}Foo.prototype.b = 2;
Bar.prototype.d = 4;console.log(assign({
  'a': 0
}, new Foo(), new Bar()));
```

然后我们得到控制台日志输出`{a: 1, c: 3}`,因为它只合并了源对象自己的可枚举属性，而不包括原型中的任何属性。

# 分配

Lodash `assignIn`方法类似于`assign`方法，但是它也将源对象的原型属性合并到对象中。

因此，我们可以用类似于`assign`函数的方式来实现它，除了我们需要调用`Object.getPrototypeOf`方法来获取我们在`sources`中的对象的原型，这样我们就可以将它们的原型属性合并到我们返回的对象中。

例如，我们可以通过以下方式实现`assignIn`:

```
const assignIn = (obj, ...sources) => {
  for (const s of sources) {
    let source = s;
    let prototype = Object.getPrototypeOf(s)
    while (prototype) {
      source = {
        ...source,
        ...prototype
      }
      prototype = Object.getPrototypeOf(prototype)
    }
    obj = {
      ...obj,
      ...source
    };
  }
  return obj;
}
```

在上面的代码中，我们有与`assignIn`相同的参数和相同的`for...of`循环。

然而，在循环内部，我们有`source`和`prototype`变量。`prototype`变量用于遍历对象的原型链，将所有原型的属性合并到原型链中。我们用`Object.getPrototypeOf`方法和`while`循环进行了遍历。

然后我们使用 spread 操作符合并到`source`对象中，将它的所有原型属性合并到`obj`对象中。

现在，当我们如下调用`assignIn`时:

```
function Foo() {
  this.a = 1;
}function Bar() {
  this.c = 3;
}Foo.prototype.b = 2;
Bar.prototype.d = 4;console.log(assignIn({
  'a': 0
}, new Foo(), new Bar()));
```

我们将`{a: 1, b: 2, c: 3, d: 4}`作为控制台日志中记录的值，因为所有的`Foo`和`Bar`实例变量都作为它们自己的属性添加到了`Foo`和`Bar`实例中。

![](img/fbd65b552e5e05de9795af907f9e7165.png)

[霍尔格连杆](https://unsplash.com/@photoholgic?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# 结论

要实现 Lodash `assign`方法，我们只需要使用 spread 操作符将对象自身的属性合并到我们想要合并属性的对象中。

`assignIn`方法更复杂，因为我们必须遍历原型链来合并所有这些属性。我们可以通过使用带有`Object.getPrototypeOf`方法的`while`循环来实现。