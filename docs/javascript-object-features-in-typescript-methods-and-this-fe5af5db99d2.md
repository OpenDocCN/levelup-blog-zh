# TypeScript 中的 JavaScript 对象特性—方法和 this

> 原文：<https://levelup.gitconnected.com/javascript-object-features-in-typescript-methods-and-this-fe5af5db99d2>

![](img/5eac57c7608a42c3345eca16840014d0.png)

照片由[克里斯汀·休姆](https://unsplash.com/@christinhumephoto?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

TypeScript 是 JavaScript 的自然扩展，在许多项目中取代了 JavaScript。

然而，并不是每个人都知道它实际上是如何工作的。

在本文中，我们将看看应该在 TypeScript 项目中使用的 JavaScript 的一些特性，包括方法和`this.`

# 定义方法

方法是函数值对象的属性。

它们也是类的一部分，我们可以在实例化类后调用它们。

例如，我们可以通过编写以下代码来定义对象中的方法:

```
let obj = {
  greet() {
    console.log("hello");
  }
};
```

在上面的代码中，我们定义了一个具有`foo`方法的`obj`对象。

然后我们可以通过运行来调用它:

```
obj.greet();
```

现在我们得到控制台日志上显示的`'hello'`。

# this 关键字

`this`对于许多 JavaScript 开发者来说是一个令人困惑的关键词。

这是因为`this`可以指代任何东西，这取决于它的位置。

如果它在一个对象中，那么`this`就是指这个对象。

如果它在一个类中，`this`指的是它所在的类。

如果是在传统函数中，那么`this`指的是函数。

例如，如果我们有:

```
let obj = {
  name: "joe",
  greet() {
    console.log(`hello ${this.name}`);
  }
};obj.greet();
```

因为`this`是指`greet`方法中的`obj`。

那么`this.name`就是指`'joe'`。

因此，我们在控制台日志中看到“hello joe”。

如果在类中，那么`this`就是类。例如，如果我们有:

```
class Foo {
  constructor() {
    this.name = "joe";
  } greet() {
    console.log(`hello ${this.name}`);
  }
}new Foo().greet();
```

然后我们将`this.name`再次设置为`'joe'`，因为我们在构造函数中已经这样做了。

所以，我们运行时得到相同的结果:

```
new Foo().greet();
```

# 独立函数中的该关键字

我们可以在传统函数中使用`this`关键字。

例如，如果我们写:

```
function greet(msg) {
  console.log(`${this.greeting}, ${msg}`);
}
```

然后我们可以使用`call`方法设置`this`的值，并调用函数:

```
greet.call({ greeting: "hello" }, "joe");
```

我们通过将`this`的值传递给`call`方法的第一个参数，将它更改为`{ greeting: “hello” }`。

一个没有使用`let`、`const`或`var`关键字的值被分配给全局对象。

例如，我们可以写:

```
function greet(msg) {
  console.log(`${this.greeting}, ${msg}`);
}greeting = "hello";
greet("joe");
```

然后我们看到`this.greeting`是`'hello'`，因为`greeting`是全局对象的属性。

`this`是我们看到的全局对象。

# 改变它的行为

`call`方法可以改变`this`的行为。

同样，我们可以使用`bind`方法返回一个具有不同值`this`的函数。

例如，我们可以写:

```
function greet(msg) {
  console.log(`${this.greeting}, ${msg}`);
}const helloGreet = greet.bind({ greeting: "hello" });
helloGreet("joe");
```

通过调用`bind`函数，我们将`this`改为一个对象，我们将它作为一个参数传入。

然后我们可以调用返回的函数，并在控制台输出中看到`'hello, joe’`。

`bind`和`call`都只能用于传统功能。

![](img/f32aeb827b941e1a60f418cbfab3476b.png)

由[弗雷德·穆恩](https://unsplash.com/@fwed?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 箭头功能

箭头函数的工作方式与常规函数不同。

它们没有自己的`this`值，继承它们运行时能找到的最接近的`this`值。

例如，如果我们有:

```
let obj = {
  greeting: "Hi",
  getGreeter() {
    return msg => console.log(`${this.greeting}, ${msg}`);
  }
};const greeting = obj.getGreeter()("joe");
```

然后当我们调用`getGreeter`时，我们得到返回的箭头函数。

`this`将是对象，因为`getGreeter`中的`this`的值将是`obj`。

第二个括号用`'joe'`调用返回的函数。

箭头函数没有自己的值`this`，所以它从`getGreeter`中取`this`的值。

# 结论

我们可以向对象和类添加方法，并在我们的 TypeScript 代码中使用它们。

`this`的值因位置而异。为了确保我们的代码引用了正确的值`this`，我们必须知道`this`在它所在的位置发生了什么。

可以用传统函数可用的`bind`和`call`覆盖`this`的值。

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)