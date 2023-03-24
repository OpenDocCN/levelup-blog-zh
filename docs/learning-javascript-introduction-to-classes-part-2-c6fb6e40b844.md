# 学习 JavaScript:类简介第 2 部分

> 原文：<https://levelup.gitconnected.com/learning-javascript-introduction-to-classes-part-2-c6fb6e40b844>

![](img/0652f146459db1a2b7cd22dee598b044.png)

照片由 [LUM3N](https://unsplash.com/@lum3n?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在我的上一篇文章中，我讨论了如何用 JavaScript 创建经典类对象。在本文中，我将通过查看一些高级概念来继续讨论——作为一级对象的类、计算成员名和静态成员变量。

# 类是一级对象

一级对象可以是函数参数、函数的返回值，也可以像数字和字符串这样的原始数据那样分配给变量。

让我们从类对象被赋给变量开始。下面的示例演示了如何将匿名类表达式赋给变量，从而创建一个单独的类。代码如下:

```
let MyMath = new class {
  get PI() {
    return 3.14159;
  }
}let radius = 5;
let area = MyMath.PI * (radius * radius);
print(`The area of the circle with radius 5 is ${area}.`);
```

这个程序创建了一个类，可以用来存储数学常数和数学函数。通过使用类表达式创建具有一个访问器属性的类来创建该类，该属性是一个返回 pi 值的 getter。

下一个例子演示了如何将一个类对象作为函数参数传递，并让一个类作为函数返回值返回。代码如下:

```
function createObject(classDef) {
  return new classDef();
}let newObject = createObject(class {
  greet() {
    print("Hello, world!");
  }
});newObject.greet(); // displays Hello, world!
```

`createObject`函数将一个类定义作为参数，并使用该类定义返回一个新的实例对象。通过用只有一个方法`greet`的匿名类定义调用`createObject`来创建`newObject`。通过从`newObject`调用 greet 方法显示问候语。

当像类对象和函数这样的特性可以用作一级对象时，可以看出一个强大的编程语言的标准之一。基于如何使用类对象，JavaScript 显然是一种强大的编程语言。我还应该提到，JavaScript 函数也是一流的，这更加证明了 JavaScript 的强大。

# 计算成员名称

通常，类方法是通过点运算符访问的，如下例所示:

```
Point p1(1,2);
print(pt.getX());
```

但是，您可以使用方括号来定义将被计算的类方法。当访问该方法时，括号中的名称将被替换为一个值。

要使用计算成员名，请将成员名放在定义的括号中，如下所示:

*【方法名】(){
//方法定义
}*

下面是一个简化的`Point`类的例子:

```
let methodName = "getPoint";class Point {
  constructor(x,y) {
    this.x = x;
    this.y = y;
    Point.count++;
  } [methodName]() {
    print(`x: ${this.x}, y: ${this.y}`);
  }
}
```

下面是一个简短的测试程序:

```
let p1 = new Point(1,2);
p1.getPoint(); // displays x: 1, y: 2
```

您也可以将计算成员名称与 getters 和 setters 一起使用:

```
let propName = "getX";class Point {
  constructor(x,y) {
    this.x = x;
    this.y = y;
  } get [propName]() {
    return this.x;
  } get [propName]() {
    return this.y;
  } set [propName](value) {
    this.x = value;
  } set [propName](value) {
    this.y = value;
  }
}
```

# 静态成员方法

有时你想定义一个必须从类本身调用的方法，而不是通过类构造函数。这就是所谓的静态方法。您可能还希望定义一个与类的所有实例相关的属性，而不仅仅是一个实例，例如在程序运行期间创建的所有对象的计数。这就是所谓的静态属性。在 JavaScript 中我们可以做到这两个。

静态方法是在类定义中通过用关键字 static 开始声明来定义的。给定前面对一个`Point`对象的定义，下面的代码向该类添加了一个静态的`create`方法，这样我们就可以在不调用构造函数的情况下创建新的`Point`对象:

```
static create(x,y) {
  return new Point(x,y);
}
```

必须使用`Point`类而不是`Point`的实例来调用该方法，如下行所示:

```
let p1 = Point.create(2,3);
```

如果我们通过取出构造函数并强迫类的用户调用 create 方法来修改`Point`类定义，我们就实现了 OOP 工厂方法模式。请到[这里](https://en.wikipedia.org/wiki/Factory_method_pattern)了解为什么您可能选择使用工厂方法而不是构造函数来创建对象。

# 静态成员变量(属性)

静态属性是为程序中创建的每个类对象保留其值的类属性。静态属性的一个经典例子是跟踪创建的对象的数量。让我们看看如何为`Poin` t 类创建一个静态属性。

“纯”静态属性是 ES2019 的一部分，还没有在 JavaScript 中广泛使用，所以下面的`Point`类静态属性的实现有点麻烦。首先，修改`Point` 的构造函数，如下所示:

```
class Point {
  constructor(x,y) {
    this.x = x;
    this.y = y;
    Point.count++;
  }
  … // more class definition code
}
```

接下来，将静态 getter 方法`COUNT`添加到类定义中:

```
static get COUNT() {
  return Point.count;
}
```

您必须在主程序中设置计数:

```
Point.count = 0;
```

下面是测试这个静态属性的测试程序:

```
Point.count = 0;
let p1 = Point.create(1,2);
print(`Number of Point objects: ${Point.COUNT}`);
let p2 = Point.create(2,3);
print(`Number of Point objects: ${Point.COUNT}`);
let p3 = Point.create(-1,-1);
print(`Number of Point objects: ${Point.COUNT}`);
```

该程序的输出是:

```
Number of Point objects: 1
Number of Point objects: 2
Number of Point objects: 3
```

正如我所说的，这有点像黑客，当 ES2019 变得更广泛可用时，你将能够像其他 OOP 语言一样创建一个使用静态成员变量。

# JavaScript 有类

本文展示了一些使用 JavaScript 类可以执行的高级技术。在我的下一篇文章中，我将向您介绍用 JavaScript 类执行继承。

感谢您阅读这篇文章，请给我发电子邮件，地址是[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)，并提出意见和建议。如果你对我的在线编程课程感兴趣，请访问 https://learningcpp.teachable.com。