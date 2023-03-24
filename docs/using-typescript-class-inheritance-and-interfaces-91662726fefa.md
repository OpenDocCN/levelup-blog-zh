# 使用类型脚本—类继承和接口

> 原文：<https://levelup.gitconnected.com/using-typescript-class-inheritance-and-interfaces-91662726fefa>

![](img/c11e6411709123ddc7e8ee8f36c6e4df.png)

照片由[zdenk Macháek](https://unsplash.com/@zmachacek?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

TypeScript 是 JavaScript 的自然扩展，在许多项目中取代了 JavaScript。

然而，并不是每个人都知道它实际上是如何工作的。

在本文中，我们将研究如何在 TypeScript 中使用类和接口。

# 类继承

TypeScript 建立在标准的 clas 继承特性之上，使使用其他编程语言的开发人员更熟悉这些特性。

它像 JavaScript 一样保留了`extends`关键字来创建子类。

例如，我们可以写:

```
class Person {
  constructor(public name: string) {}
}class Student extends Person {
  constructor(readonly id: number, public name: string) {
    super(name);
  } read() {
    //...
  }
}
```

我们有`Student`子类，它在构造函数中有自己的参数。

我们使用`super`关键字来调用父构造函数。

另外，我们有一个`read`方法，它是`Student`类独有的。

# 子类的类型推断

TypeScript 编译器可能无法从类中推断类型。

假设它对所创建对象的结构有所了解，很容易产生意想不到的结果。

例如，如果我们有:

```
class Person {
  constructor(public name: string) {}
}class Student extends Person {
  constructor(readonly id: number, public name: string) {
    super(name);
  } read() {
    //...
  }
}const person = new Person("james");
const student = new Student(1, "james");const people: (Person | Student)[] = [person, student];
people.forEach(p => {
  if (p instanceof Person) {
    console.log("Person");
  } else {
    console.log("Student");
  }
});
```

我们将看到这两个对象都被认为是`Person`的一个实例。

所以我们为两个对象记录了`'Person'`。

# 抽象类

抽象类是不能直接实例化的类，用于描述必须由子类实现的公共功能。

它强制子类遵守特定的结构，但允许特定方法的特定类实现。

要定义一个，我们可以使用`abstract`关键字。

例如，我们可以写:

```
abstract class Person {
  constructor(public name: string) {}
  getName(): string {
    return this.name;
  }
  abstract getSpecificDetails(): string;
}class Student extends Person {
  constructor(public id: string, public name: string) {
    super(name);
  } getSpecificDetails() {
    return `${this.id} - ${this.name}`;
  }
}
```

上面的代码有一个抽象的`Person`类，带有一个`getName`方法和一个构造函数。

然后我们有实现`Person`类的`Student`类。

这用关键字`extends`表示。

我们实现了`getSpecificDetails`并返回一个字符串。

# 保护抽象类的类型

抽象类在由 TypeScript 编译器生成的 JavaScript 中实现为常规类。

TypeScript 编译器阻止我们用抽象类创建对象。

然而，这并没有延续到 JavaScript 代码中，这意味着我们可以用在我们的类型脚本代码中声明为抽象的类来创建对象。

# 使用接口

界面类似于对象形状类型。

一个类可以实现一个接口，这意味着它必须符合接口。

例如，我们可以写:

```
interface Person {
  name: string;
  getName(): string;
}class Employee implements Person {
  constructor(public name: string) {}
  getName() {
    return this.name;
  }
}
```

我们有一个包含`name`和`getName`成员的`Person`接口。

然后由于`Employee`实现了由`implements`关键字指示的`Person`接口，我们必须包含这两个成员。

`interface`关键字表明它是一个接口。

# 合并接口声明

我们可以用`|`合并多个接口，形成一个并集，或者用`&`形成一个交集。

这就像对象形状类型的联合或交集。交集意味着两个接口的所有成员都必须包含在我们的对象中。

联合意味着可以包含接口的任何成员。

![](img/d517f021d1805861c43defedaca5d5c1.png)

照片由 [Rowan Heuvel](https://unsplash.com/@insolitus?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 实现多个接口

我们可以在一个类中实现多个接口。然后我们需要实现两个接口的所有成员。

例如，我们可以写:

```
interface Person {
  name: string;
  getName(): string;
}interface Owner {
  ownedItems(): string[];
}class Employee implements Person, Owner {
  constructor(public name: string) {}
  getName() {
    return this.name;
  } ownedItems() {
    return ["food"];
  }
}
```

我们有一个实现了`Person`和`Owner`接口的`Employee`类。

它拥有两者的所有成员。

# 结论

我们可以用 TypeScript 实现继承。它改进了类语法的继承语法。

TypeScript 具有抽象类和接口来强制实现类。