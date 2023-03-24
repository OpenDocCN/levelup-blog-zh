# JavaScript 干净代码—类

> 原文：<https://levelup.gitconnected.com/javascript-clean-code-classes-82e8f42f6235>

![](img/749e3ed18410150490b32112ed8ada69.png)

[产品学院](https://unsplash.com/@productschool?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

JavaScript 中的类是语言原型继承特性之上的语法糖。然而，就编写干净的代码而言，这些原则仍然适用，因为它们与基于类的语言中的类具有相同的结构。

在本文中，我们将看看如何以一种简洁且可维护的方式编写 JavaScript 类。

# 班级组织

类应该从构造函数开始，构造函数内部有一个变量成员列表。

该类的方法可以跟在构造函数和变量列表之后。

# 包装

我们应该将私有变量保存在类内部的块中，而不是作为`this`的属性。

这样，类之外的代码就不能访问它们并意外地更改它们的值。

我们应该定义 getters 和 setters 来获取和设置不属于`this`的变量，或者将它们用于计算属性。

这也有助于隐藏实现，这样类就不会意外地使用它们，这就造成了代码的紧密耦合。

# 班级应该很小

班级应该很小。他们不应该有一个以上的责任。我们不希望有做多件事的类。神级是我们不想要的。

类的名字应该告诉我们它履行什么职责。如果一个方法没有类名中没有的东西，那么它就不应该在那里。

我们应该能够不用“如果”、“和”、“或”或“但是”这样的词来描述我们的类做了什么。

例如，下面是一个具有一项职责的类的示例:

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

我们的`Rectangle`类只有一个责任，那就是表示一个矩形。

另一方面，如果我们有如下的`createCircle`方法，那么没有这些连接词我们就无法描述我们的类，因为我们的`Rectangle`类有不止一个责任:

```
class Rectangle {
  constructor(length, width) {
    this.length = length;
    this.width = width;
  } get area() {
    return this.length * this.width;
  } createCircle(radius) { }
}
```

# 单一责任原则

班级应该有一个责任和一个改变的理由。这个原则给了我们一个很好的班级规模的指导方针。如果它有一个以上的责任，那它就太大了。

识别责任让我们在代码中创建更好的抽象。我们应该将`createCircle`方法移到它自己的类`Circle`中，因为圆形和矩形没有任何关系。

所以我们可以这样写:

```
class Circle {
  constructor(radius) {
    this.radius = radius;
  }
  get area() {
    return Math.PI * (this.radius ** 2);
  }
}
```

单一责任原则是面向对象设计的重要原则之一。也很好理解。

不幸的是，这也是更多被忽视的原则之一。人们只是让他们的程序工作，却忘记清理它们。

一旦他们的代码开始工作，他们就会转移到下一个问题上，只是懒得去清理它。

一些人还担心，建立更小的、单一责任的班级会增加理解全局的难度。他们认为从一个类到另一个类的导航会使他们更难了解系统的全貌。

然而，这是不正确的，因为他们有移动部件的数量。如果我们把所有的东西都放在一个类中，那么我们仍然必须在类中而不是在不同的文件中挖掘它们。没什么不同。

较小的类包含较少的代码，因此更容易阅读。

![](img/33db2d2ac3b58d5c14649af4d16c8b74.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [NeONBRAND](https://unsplash.com/@neonbrand?utm_source=medium&utm_medium=referral) 拍摄

# 内聚力

类应该有少量的实例变量。每个方法都应该操作一个或多个实例变量。每个方法都使用每个变量的类是最大内聚的。

我们喜欢内聚性高，这样方法和实例变量就相互依赖，并作为一个整体保持在一起。

高内聚使得阅读代码变得容易，因为它只围绕一个单一的概念。他们也不经常改变，因为每个类不做太多。

比如我们的`Circle`类:

```
class Circle {
  constructor(radius) {
    this.radius = radius;
  }
  get area() {
    return Math.PI * (this.radius ** 2);
  }
}
```

是内聚的，因为我们在`area` getter 方法中使用了`radius`实例变量，所以我们在方法中使用了每个实例变量。

# 保持凝聚力意味着许多小班

正如我们所看到的，创建小型的内聚类是很容易的。它们有较少的实例变量，所以很容易在方法中使用它们。

较大的类在保持内聚性方面有问题，因为我们不断添加只有少数方法使用的新实例变量。

这意味着当类失去凝聚力时，我们应该把它们分开。所以与其写:

```
class Shape {
  constructor(radius, length, width) {
    this.radius = radius;
    this.length = length;
    this.width = width;
  } get circleArea() {
    return Math.PI * (this.radius ** 2);
  } get rectangleArea() {
    return this.length * this.width;
  }
}
```

我们应该把它们分成`Rectangle`和`Circle`类，因为实例变量和方法放在一起没有意义。

所以最好这样写:

```
class Rectangle {
  constructor(length, width) {
    this.length = length;
    this.width = width;
  } get area() {
    return this.length * this.width;
  }
}class Circle {
  constructor(radius) {
    this.radius = radius;
  }
  get area() {
    return Math.PI * (this.radius ** 2);
  }
}
```

# 结论

JavaScript 类应该遵循干净代码原则，即使它只是其原型继承模型之上的语法糖。

我们应该有内聚类，其中方法使用所有定义的实例变量。

此外，每个类应该有一个单一的责任。这一点和内聚性使得阅读代码变得容易，因为它们只围绕一个单一的概念。他们也不经常改变，因为每个类不做太多。

小班比大班好，因为他们没有超过一个单一的责任，他们更有凝聚力。