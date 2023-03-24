# JavaScript 干净代码——关于类的更多信息

> 原文：<https://levelup.gitconnected.com/javascript-clean-code-more-about-classes-7553ae9db3f2>

![](img/476240751d17e3e79f66583014fbc8b8.png)

Jonas Svidras 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 中的类是语言原型继承特性之上的语法糖。然而，就编写干净的代码而言，这些原则仍然适用，因为它们与基于类的语言中的类具有相同的结构。

在本文中，我们将看看如何以一种简洁且可维护的方式编写 JavaScript 类。

我们将看看如何组织变化和使用类语法，而不是使用构造函数。

# 组织变革

我们在组织课程时，必须为课程的变更做好准备。这意味着我们应该使它们可扩展，而不是不断修改代码来获得我们想要的功能。

我们的方法应该简单。简单的方法更容易测试，我们不必对它们做太多的修改。

我们应该遵循开放/封闭原则，即一段代码应该对扩展开放，但对修改关闭。

这适用于类，就像另一段代码一样。

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

然后，我们可以很容易地添加一个 getter 方法来计算矩形的周长，如下所示:

```
class Rectangle {
  constructor(length, width) {
    this.length = length;
    this.width = width;
  } get area() {
    return this.length * this.width;
  } get perimeter() {
    return 2 * (this.length + this.width);
  }
}
```

如我们所见，我们不必修改现有代码来添加计算周长的方法。我们只需添加`perimeter` getter 方法就可以了。

![](img/b5fe8bf70665a020bc96607889259032.png)

[Yancy Min](https://unsplash.com/@yancymin?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# 使用类语法而不是构造函数

是时候转到类语法而不是使用构造函数了。

我们可以用下面的继承例子来看为什么:

```
function Person(name, age) {
  this.name = name;
  this.age = age;
}function Employee(name, age, employeeCode) {
  Person.call(this, name, age);
  Employee.prototype.constructor = Person;
  this.employeeCode = employeeCode;
}
```

首先，我们必须创建`Person`构造函数，然后制作`Employee`的原型`Person`并设置所有继承的属性，我们必须首先编写:

```
Person.call(this, name, age);
```

设置所有实例变量，并且:

```
Employee.prototype.constructor = Person;
```

将`Employee`的原型构造器设置为`Person`。我们很容易漏掉这两行中的任何一行，并且`Employee`构造函数不会继承`Person`构造函数。

如果我们如下创建一个`Employee`实例:

```
const employee = new Employee('Joe', 20, 'waiter');
```

然后，我们应该在`__proto__`属性下看到类似下面的内容:

```
constructor: *ƒ Person(name, age)*
```

这意味着我们正确地将`Employee`实例的原型设置为`Person`构造函数。

有了类语法，我们只需要使用`extends`关键字来继承一个类。我们可以将上面的代码重写如下:

```
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
}class Employee extends Person{
  constructor(name, age, employeeCode) {
    super(name, age);
    this.employeeCode = employeeCode;
  }
}
```

那么当我们创建如下相同的`Employee`实例时:

```
const employee = new Employee('Joe', 20, 'waiter');
```

然后，我们应该在`__proto__`属性下看到如下内容:

```
constructor: *class Employee*
```

正如我们所见，除了`function`和`class`的不同之外，两个`console.log`输出是相同的，但是它们是相同的，因为类与构造函数是相同的。

但是，我们不必使用`call`或`this`，手动设置超类的变量。

JavaScript 解释器会告诉我们是否忘记调用`super`或使用`extends`关键字。

现在没有回到旧的构造函数语法，因为它很不方便。

# 结论

当我们设计班级时，我们应该为改变而组织。这意味着我们应该拥有对扩展开放但对修改关闭的代码。

这降低了弄乱现有代码的风险，这也是为什么允许我们通过添加新代码来不断进行更改的原因。

此外，是时候讨论创建构造函数的类语法了。用旧的构造函数很难继承，而类语法使一切变得容易得多。