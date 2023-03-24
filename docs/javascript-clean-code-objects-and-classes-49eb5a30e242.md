# JavaScript 干净代码—对象和类

> 原文：<https://levelup.gitconnected.com/javascript-clean-code-objects-and-classes-49eb5a30e242>

![](img/41ac948195b9550f62cd92e63317866d.png)

由[在](https://unsplash.com/@creativeexchange?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上创意交流拍摄的照片

当编写干净的代码时，我们必须小心我们定义的对象和类或构造函数。我们不想公开我们不想让外界看到的数据，我们希望我们的数据结构以一种易于重用的方式定义。

在本文中，我们将看看如何以一种易于使用的方式组织我们的数据。

# 在构造函数上使用类

我们应该使用类而不是构造函数。它更清晰，继承也更容易。

他们本质上是一样的。类语法是构造函数语法的语法糖。

例如，我们可以如下定义一个`Person`类:

```
class Person{
  constructor(name, age) {
    this.name = name;
    this.age = age;    
  }   
}
```

我们可以看到，我们的构造函数将`name`和`age`作为参数，并将其设置为实例变量的值。

当我们需要扩展这个类的时候，它会大放异彩。我们可以创建一个拥有`Person`类的实例变量的`Employee`类，并如下扩展它:

```
class Employee extends Person {
  constructor(name, age, employeeCode) {
    super(name, age);
    this.employeeCode = employeeCode;
  }
}
```

我们所要做的就是用`super`调用超类的构造函数，然后我们可以在`constructor`中设置`Employee`类的实例变量。

# 使用 Getters 和 Setters

Getters 和 setters 有利于隐藏被访问的实际数据。我们可以有计算属性，这意味着我们可以抽象这些计算属性的实现，这样当我们改变实现时，就不必担心改变使用它们的代码。

此外，设置器让我们在设置数据之前进行验证，而不是直接设置它们。

同样，错误处理和日志也可以很容易地添加到其中。

我们也可以用 getters 延迟加载对象属性。

例如，我们可以如下使用 getters:

```
class Rectangle {
  constructor(length, width) {
    this._length = length;
    this._width = width;
  } get area() {
    return this._length * this._width;
  }
}
```

正如我们所看到的，我们可以用它来创建一个计算属性，以获得矩形的面积。

那么我们可以如下使用它:

```
let rectangle = new Rectangle(1, 2);
console.log(rectangle.area);
```

我们可以将验证添加到 setters 中，如下所示:

```
class Rectangle {
  constructor(length, width) {
    this._length = length;
    this._width = width;
  } get area() {
    return this._length * this._width;
  } get length() {
    return this._length;
  } set length(length) {
    if (length <= 0) {
      throw new Error('Length must be bigger than 0');
    }
    this._length = length;
  } get width() {
    return this._width;
  } set width(width) {
    if (width <= 0) {
      throw new Error('Width must be bigger than 0');
    }
    this._width = width;
  }
}
```

注意，定义 getters 也是一个好主意，这样我们就可以访问属性的值。

# 保持成员私密

JavaScript 类中没有私有变量，所以我们应该在块范围内定义私有变量，这样它们就不会用`let`和`const`暴露给公众。

![](img/ab68d0a58237d039f405b1df2a862bd4.png)

蒂娜·道森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 链接方法

链接方法使得调用一系列函数变得不那么冗长。我们可以通过返回`this`来定义一个可以被链接的函数。

例如，我们可以这样写:

```
class Person {
  setName(name) {
    this.name = name;
    return this;
  } setAge(age) {
    this.age = age;
    return this;
  }
}
```

那么我们可以如下使用它:

```
const person = new Person().setName('Joe').setAge(10);
```

然后当我们记录`person`时，我们得到:

```
{name: "Joe", age: 10}
```

这是在 jQuery 和 Lodash 等许多库中使用的常见模式。

# 更喜欢组合而不是继承

我们应该只为“是-a”关系留下类继承。关系是某个更大实体的子集。例如，雇员是一个人。

不然就用作文代替。例如，如果我们想保留一个`Person`的地址，我们可以创建一个名为`Address`的类，并在`Person`类的方法中实例化它，然后在那里使用它。

A `Person`与`Address`有‘has-a’关系，所以在这种情况下我们不应该使用继承。

例如，我们可以编写类似下面的代码:

```
class Address {
  constructor(streetName) {
    this.streetName = streetName;
  }
}class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  } setAddress() {
    const address = new Address('123 A St.');
  }
}
```

# 结论

类的语法比构造函数的语法更容易理解，尤其是当我们需要继承的时候。因此，是时候尽快远离构造函数语法了。

我们应该只在“是-a”关系中使用类继承。不然就用作文代替。

此外，我们可以使用 getters 和 setters 来保持私有成员的实现。在为成员设置值之前，我们可以将 getters 用于计算属性，将 setters 用于运行代码。例如，我们可以在用 setter 设置值之前运行一些验证代码。

链接方法也是一种普遍接受的方法，通过减少调用的冗长来清理代码。