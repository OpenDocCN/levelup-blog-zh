# JavaScript 干净代码——可靠

> 原文：<https://levelup.gitconnected.com/javascript-clean-code-solid-9d135f824180>

![](img/966c5e3290e6a4f9a82cb9168ed57243.png)

阿什温·瓦斯瓦尼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

像任何其他编程语言一样，JavaScript 也遵循 SOLID 中概述的原则。

SOLID 由 5 个概念组成，我们可以用它们来改进我们的程序。它们是:

*   单一责任原则
*   开放/封闭原则
*   利斯科夫替代原理
*   界面分离原理
*   从属倒置原则

在本文中，我们将逐一查看，并了解如何将它们应用到 JavaScript 程序中。

# 单一责任原则

单一责任原则说我们的每个类只能用于一个目的。

我们需要这样做，这样当有变化时，我们就不必频繁地修改代码。如果这个类在做很多事情，也很难理解它在做什么。

同一类中不相关的概念也使得理解代码的目的更加困难。

例如，我们可以编写如下内容来遵循单一责任原则:

```
class Rectangle {
  constructor(length, width) {
    this.length = length;
    this.width = width;
  } get area() {
    return this.length * this.width;
  }
}
```

上面的`Rectangle`类只有矩形的`length`和`width`作为成员，并让我们从中获取面积。

它不做其他事情，所以它遵循单一责任原则。

一个不好的例子是:

```
class Rectangle {
  constructor(length, width) {
    this.length = length;
    this.width = width;
  } get area() {
    return this.length * this.width;
  } createCircle() { }
}
```

我们应该在一个`Rectangle`类中有一个`createCircle`方法，因为它们是不相关的概念。

# 开/关原则

开放/封闭原则表明，一个软件可以扩展，但不能修改。

这意味着我们应该能够在不改变现有代码的情况下添加更多的功能。

例如，如果我们有下面的`Rectangle`类:

```
class Rectangle {
  constructor(length, width) {
    this.length = length;
    this.width = width;
  } get area() {
    return this.length * this.width;
  }
}
```

然后，如果我们想添加一个函数来计算它的周长，我们可以通过添加一个方法来完成，如下所示:

```
class Rectangle {
  constructor(length, width) {
    this.length = length;
    this.width = width;
  } get area() {
    return this.length * this.width;
  } get perimteter() {
    return 2 * (this.length + this.width);
  }
}
```

正如我们所看到的，我们不必改变现有的代码来添加它，这满足了开放/封闭原则。

# 利斯科夫替代原理

这个原则表明，如果我们有一个父类和一个子类，那么我们可以交换父类和子类，而不会得到不正确的结果。

这意味着子类必须实现父类中的所有东西。父类服务于具有基类成员的类，子类从基类扩展。

例如，如果我们想为一堆形状实现类，我们可以有一个父类`Shape`，它通过实现`Shape`类中的所有东西被所有类扩展。

我们可以编写以下代码来实现一些 shape 类，并获得每个实例的面积:

```
class Shape {
  get area() {
    return 0;
  }
}class Rectangle extends Shape {
  constructor(length, width) {
    super();
    this.length = length;
    this.width = width;
  } get area() {
    return this.length * this.width;
  }
}class Square extends Shape {
  constructor(length) {
    super();
    this.length = length;
  } get area() {
    return this.length ** 2;
  }
}class Circle extends Shape {
  constructor(radius) {
    super();
    this.radius = radius;
  } get area() {
    return Math.PI * (this.radius ** 2);
  }
}const shapes = [
  new Rectangle(1, 2),
  new Square(1, 2),
  new Circle(2),
]for (let s of shapes) {
  console.log(s.area);
}
```

因为我们在每个扩展了`Shape`的类中覆盖了`area` getter，所以我们为每个形状获得了正确的面积，因为为每个形状运行了正确的代码来获得面积。

![](img/dd54e6db62cadf5dc153e0eac0159fe8.png)

照片由 [Allef Vinicius](https://unsplash.com/@seteales?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 界面分离原理

接口分离原则声明“客户不应该被迫依赖他们不使用的接口。”

这意味着如果不需要的话，我们不应该强制实现。

JavaScript 没有接口，所以这个原则并不直接适用，因为它没有通过接口强制实现任何东西。

# 从属倒置原则

这个原则声明高级模块不应该依赖低级模块，它们都应该依赖抽象，而抽象不应该依赖细节。细节应该依赖于抽象。

这意味着我们不需要知道依赖关系的任何实现细节。如果我们这样做了，那么我们就违反了这个原则。

我们需要这个原则，因为如果我们确实需要引用代码来获得依赖关系的实现细节，那么当依赖关系改变时，我们自己的代码将会有很多重大改变。

随着软件变得越来越复杂，如果我们不遵循这个原则，那么我们的代码将会崩溃很多。

对我们实现的代码隐藏实现细节的一个例子是 facade 模式。该模式将一个 facade 类放在底层复杂实现的前面，因此我们只需依赖 facade 来使用底层的特性。

如果底层类改变了，那么只有外观需要改变，我们不必担心修改我们自己的代码，除非外观有重大改变。

例如，下面是外观模式的一个简单实现:

```
class ClassA {}class ClassB {}class ClassC {}class Facade {
  constructor() {
    this.a = new ClassA();
    this.b = new ClassB();
    this.c = new ClassC();
  }
}class Foo {
  constructor() {
    this.facade = new Facade();
  }}
```

我们不必担心`ClassA`、`ClassB`和`ClassC`来实现`Foo`类。只要`Facade`类不变，我们就不用自己改代码。

# 结论

我们应该遵循坚实的原则来编写易于维护的代码。

为了遵循 SOLID，我们必须编写只做一件事的类。

我们的代码必须对扩展开放，但对修改关闭。这减少了弄乱现有代码的机会。

当我们切换父类和子类时，它们必须是可互换的。当我们转换它们时，结果仍然是正确的。

最后，我们不应该依赖我们引用的任何一段代码的实现细节，这样我们就不会在某些事情发生变化的时候出现很多破坏性的变化。

这让我们减少了模块之间的耦合。