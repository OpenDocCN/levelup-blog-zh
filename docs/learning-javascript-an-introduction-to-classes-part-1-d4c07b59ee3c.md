# 学习 JavaScript:类的介绍第 1 部分

> 原文：<https://levelup.gitconnected.com/learning-javascript-an-introduction-to-classes-part-1-d4c07b59ee3c>

![](img/ddf75a89f76be276ecca67e510948fa9.png)

[摄影师杨](https://unsplash.com/@rugeli?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

JavaScript 并不总是能够像在 C++和 Java 等语言中那样创建经典对象，但是自从 EcmaScript 6 的开发以来，您现在可以了。在本文中，我将介绍如何在 JavaScript 中创建类，包括如何实现构造函数和访问方法(getters 和 setters ),我将在以后的文章中介绍 JavaScript 类的其他方面。

# EcmaScript 6 之前的类

在以前版本的 JavaScript (EcmaScript)中，不能像在 C++和 Java 等语言中那样创建类。相反，您必须创建一个构造函数，然后将方法分配给构造函数的原型。以下示例演示了这种技术:

```
function Student(name, id, grades) {
  this.name = name;
  this.id = id;
  this.grades = grades;
}Student.prototype.calcAverage = function() {
  let sum = 0;
  for (grade of this.grades) {
    sum += grade;
  }
  return sum / this.grades.length;
}let stu1 = new Student("Jane Smith", 1234, [81, 77, 92]);
print("Grade average: " + stu1.calcAverage());
// displays 83.33333333333
```

这不是经典的面向对象编程(OOP)语言定义类对象和方法的方式，但在以前的 JavaScript 版本中，这是最接近的方式。

# 现在用 JavaScript 创建类

当前版本的 JavaScript 现在允许您创建与经典 OOP 语言创建类的方式更密切相关的类。让我们先看看如何将上面的学生示例定义为一个类:

```
class Student { constructor(name, id, grades) {
    this.name = name;
    this.id = id;
    this.grades = grades;
  }

  calcAverage() {
    let sum = 0;
    for (let grade of this.grades) {
      sum += grade;
    }
    return sum / this.grades.length;
  }
}let stu1 = new Student("Jane Smith", 1234, [81, 77, 92]);
print("Grade average: " + stu1.calcAverage());
```

这个程序的输出是:

```
Grade average: 83.33333333333
```

类定义看起来更像是在其他 OOP 语言中完成的方式。关键字`class`表示一个类定义即将出现。创建一个构造函数，不是用类名，而是用关键字`constructor`。尽管如此，正如您在示例程序中看到的，仍然使用类名调用构造函数。

# 类定义是语法糖

类定义只是隐藏了 JavaScript 中创建对象原型的标准方式的语法糖。这意味着我可以使用旧的 JavaScript 对象语法来定义上面的`Student`类，就像这样(这个例子是根据尼古拉斯·c·扎卡斯的《理解 EcmaScript 6 一书中的一个例子修改的，第 168 页):

```
let Student = function(name, id, grades) {
  "use strict";
  const Student = function(name, id, grades) {
    if (typeof new.target === "undefined") {
      throw new Error("Constructor must be called with new.");
    }
    this.name = name;
    this.id = id;
    this.grades = grades;
  } Object.defineProperty(Student.prototype, "calcAverage", {
    value: function() {
      if (typeof new target !== "undefined") {
        throw new Error("Method cannot be called with new.");
      }
      let sum = 0;
      for (let grade of this.grades) {
        sum += grade;
      }
      return sum / this.grades.length;
  },
  enumerable: false,
  writable: true,
  configurable: true
  });
  return Student;
}());
```

显然，类语法使得创建类比用老方法容易得多。

不过，这里有几件事需要注意。首先，默认情况下，类定义中的所有代码都以严格模式运行。此外，默认情况下，所有方法都被定义为不可数的。不能用`new`关键字调用类方法，但必须用 new 关键字调用类构造函数。

一般来说，这种新的用于创建类的 JavaScript 语法为您提供了许多功能，而不必像以前的 JavaScript 版本那样编写更复杂的基于对象的语法。

# 类构造函数

如上面的例子所示，类需要用构造函数实例化。下面是另一个定义了构造函数的类定义:

```
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  } show() {
    return this.name + ", " + this.age;
  }
}
```

如果你没有定义构造函数，系统会自动为你提供一个默认的构造函数。这里有一个例子来说明这一点:

```
class Person {
  show() {
    return this.name + ", " + this.age;
  }
}let you = new Person();
print(you.show());
```

这个程序的输出是:

```
undefined, undefined
```

即使我没有定义构造函数方法，程序也没有崩溃，因为创建了一个默认的构造函数并将`undefined`分配给`name` 和`age`。

如果已经定义了另一个构造函数，则不能定义默认构造函数。类只允许一个构造函数定义，或者，换句话说，它们只允许使用一次`constructor`关键字。当没有定义构造函数或者已经定义了另一个构造函数时，系统提供默认的构造函数。

# 访问者属性

完整的类定义提供了用于检索和设置属性值的访问器方法(或者 JavaScript 中的属性)。在 OOP 语言中，这些通常被称为 getters 和 setters。

让我们从获取当前属性开始。定义中使用的关键词是`get`。此属性只返回存储在属性中的值。下面是带有 getter 属性的`Person`类定义:

```
class Person {
  constructor(name, age) {
    this.name = name;
    this.pAge = age;
  } get age() {
    return this.pAge;
  } show() {
    return this.name + ", " + this.age;
  }
}let me = new Person("John Green", 24);
print("Age: " + me.age); // displays Age: 24
```

这里我将存储一个人的年龄的属性的名称改为`pAge`，这样我就可以使用`age`作为 getter 属性。

现在让我们给类定义添加一个 setter。拥有 setter 允许您在设置属性值时执行一些数据验证。例如，`age`属性可以设置为任何有效的数字，因此某人的年龄可以是-2000 或 123，456。让我们为`age`定义 setter，以便它只接受从 0 到 120 的值。

下面是这个 setter 的新类定义:

```
class Person {
  constructor(name, age) {
    this.name = name;
    if (age >= 0 && age <= 120) {
      this.pAge = age;
    }
    else {
      this.pAge = 0;
    }
  } get age() {
    return this.pAge;
  } set age(value) {
    if (value >= 0 && value <= 120) {
      this.pAge = value;
    }
    else {
      this.pAge = 0;
    }
  } show() {
    return this.name + ", " + this.age;
  }
}let me = new Person("John Green", 24);
print("Age: " + me.age); // displays Age: 24
me.age = 121;
print("Age: " + me.age); // displays 0
me.age = 25;
print("Age: " + me.age); // displays Age: 25
```

查看我的类定义，我还需要将这个数据验证代码添加到我的构造函数中，就像这样:

```
constructor(name, age) {
  this.name = name;
  if (age >= 0 && age <= 120) {
    this.pAge = age;
  }
  else {
    this.pAge = 0;
  }
}
```

另一种解决方案是编写一个函数来验证年龄，并从构造函数和设置函数中调用该函数。我将把它留给读者作为练习。

# 在第 2 部分中

我只是触及了用 JavaScript 创建类的表面。在我的下一篇文章中，我将讨论更多的类特性，包括如何将类视为一级对象、计算成员名和静态类成员。在那篇文章之后，我将转向 JavaScript 中的类继承。

感谢您的阅读，请将您的意见和建议通过电子邮件发送至[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)。如果你对我的在线编程课程感兴趣，请访问 https://learningcpp.teachable.com。