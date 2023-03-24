# TypeScript 必备基础知识—类型别名和接口

> 原文：<https://levelup.gitconnected.com/typescript-must-know-fundamentals-for-your-next-tech-interview-or-project-255ae70df0a3>

TypeScript 提供了两种方法来为我们的数据创建定制类型，它们包括**类型别名**和**接口**。本文将讨论它们之间的相似之处和不同之处，以及各自的最佳用例。

# TL；博士:

*   类型别名和接口都可以用来描述对象的形状或函数签名。但是语法不同。
*   声明合并只对接口有效，对类型别名无效。
*   不能用关键字`interface`声明联合、交集或元组。但是，您可以在接口中使用它们。
*   类可以实现类型别名和接口，但不能实现表示联合类型的类型别名。

![](img/6b98af17a830b8657d96f822769dd01d.png)

让我们开始吧！

# 键入别名

一个[类型别名](https://devdocs.io/typescript/2/everyday-types#type-aliases)基本上是任何类型的名称。类型别名不仅可以用来表示原语，还可以表示对象类型、联合类型、元组和交集。让我们来看一些例子:

```
type personName = string;
type personId = string | number; // Union type
type numArray = number[];// Object type
type Person = {
    id: personId; // We're making use of another type alias here
    name: personName;
};const user1: Person = {
    id: 1, // ok, because it's number
    name: "Bob"
};
const user2: Person = {
    id: "2",  // ok, because it's string
    name: "Alice"
};
const user3: Person = {
    id: false,  // Not ok: 'false' is neither string or number
    name: "Alice"
}const numbers: numArray = [1, 8, 9];
```

类型别名是用前面的`type`关键字声明的。可以把它们想象成用来表示特定值的常规 JavaScript 变量。只要使用变量名，它就会被计算为它所代表的值。类型别名以类似的方式工作。无论您在何处通过别名对类型进行注释，别名都将评估为它所代表的类型。像变量一样，不能多次声明同一个类型别名。TypeScript 也从不推断别名，您必须显式地注释它们。

# 接口

[接口](https://devdocs.io/typescript/2/everyday-types#interfaces)是命名数据结构的另一种方式，如对象。接口的声明语法不同于类型别名的声明语法。让我们将上面的类型别名`Person`重写为一个接口声明:

```
interface Person {
    id: userId;
    name: userName;
}
```

# 联合和交集

联合类型由两个或多个其他类型组成，表示可以是其中任何一个类型的值。交集允许我们将多个现有类型组合成一个具有这些类型所有特征的单一类型。举以下例子:

```
type Human = {
    name: string;
    speaks: boolean;
};interface Dog {
    name: string;
    barks: boolean;
}type HumanAndDog = Human & Dog; // Intersection
type HumanOrDogOrBoth = Human | Dog; // Unionlet humanAndDog: HumanAndDog = {
    // must have all the properties of Human and Dog
    name: "Sparky",
    speaks: false,
    barks: true,
};let humanOrDog: HumanOrDogOrBoth = {
    // can have the properties of Human or Dog, or both
    name: "Tolu",
    speaks: true,
};
```

类型别名和接口可以使用联合或交集组合成一个`type`，但是不能组合成一个`interface`。

# 元组

元组是一种输入固定长度数组的方式，考虑到了所述数组中的每一项。

```
type Mix = [number, string, boolean];const mix: Mix = [1, "banana", true];
```

在 TypeScript 中，元组可以用类型声明，但不能用接口声明。但是，元组可以在接口内部使用，如下所示:

```
interface Cat {
    ageName: [number, string];
}
```

# 声明合并

如果你不止一次地用相同的名字声明一个`interface`，TypeScript 将它们合并成一个声明，并将它们视为一个接口。这叫做**申报合并**。

```
interface Person {
    name: string;
}interface Person {
    age: number;
}type Num = number;
type Num = string; // Duplicate identifier Numlet person: Person = {
    // has the properties of both instances of Person
    name: "Bob",
    age: 0,
};
```

声明合并只对接口有效。如果多次声明同一个类型别名，TypeScript 将给出错误。

# 延伸

类型别名和接口都可以扩展。但是，语法不同。派生类型别名或接口具有其基类型别名或接口的所有属性和方法，还可以定义其他成员。

一个类型别名可以使用&符号扩展另一个类型别名:

```
type Human = { age : number };
type Driver = Human & { hasCar: boolean };
```

类型别名也可以扩展接口:

```
interface Human {
    age: number;
}type Driver = Human & { hasCar: boolean };
```

接口可以用关键字`extends`扩展类型别名:

```
type Human = {
    age: number;
};interface Driver extends Human {
    hasCar: boolean;
}
```

接口可以像扩展类型别名一样扩展其他接口。接口也可以扩展多个用逗号分隔的接口。

```
interface Human {
    age: number;
}interface Animal {
    alive: boolean;
}interface Driver extends Human, Animal {
    hasCar: boolean
}let driver: Driver = {
    name: "Bob",
    speaks: true,
    alive: true,
    hasCar: true
}
```

# 接口扩展类

TypeScript 允许一个接口扩展一个[类](https://devdocs.io/typescript/2/classes)。发生这种情况时，接口继承基类的成员，包括私有和公共成员，但不继承它们的实现。这意味着，当您创建一个用私有成员扩展类的接口时，只有该类或其子类可以实现该接口。这允许您将接口的使用限制在它所扩展的类的一个或多个子类上。

```
class Driver {
    greet() {
        console.log(`Hello!`);
    }
}// Interface extending the Driver class
interface UberDriver extends Driver {
    greetGuest(): void;
}// New class that extends Base class and implements the Derived interface
class AustralianUberDriver extends Driver implements UberDriver {
    greetGuest() {
        console.log("G'day Mate!");
    }
}const driver = new AustralianUberDriver();
driver.greet(); // Hello!
driver.greetGuest(); // I saw this the other day...
```

# 工具

TypeScript 支持基于类的面向对象编程。因此，它允许类使用`implements`关键字实现类型别名和接口。如果一个类未能正确实现它，将会抛出一个错误。

```
// Interface being implemented by a class
interface PositionInterface {
    x: number;
    y: number;
}class Circle implements PositionInterface {
    x = 1;
    y = 1;
}// Type alias being implemented by a class
type PositionType = {
    x: number;
    y: number;
};class Oval implements PositionType {
    x = 1;
    y = 2;
}
```

一个类也可以实现多个用逗号分隔的接口，例如`class A implements B, C {}`。请注意，类不能实现或扩展表示联合类型的类型别名:

```
type PointOnAxis = { x: number } | { y: number };// ERROR: A class can only implement an object type or intersection of object types with statically known members.
class NewPoint implements PointOnAxis {
    x = 1;
    y = 2;
}
```

> 重要的是要理解 implements 子句只是检查该类是否可以被视为接口类型。它根本不会改变类的类型或它的方法。一个常见的错误来源是假设 implements 子句会改变类类型——它不会！ *—* [*打印公文*](https://www.typescriptlang.org/docs/handbook/2/classes.html#class-heritage)

# 你应该使用哪一个？

类型别名和接口非常相似，您可以在它们之间自由选择。就个人而言，我认为类型别名更适合定义原语、联合、交集、函数或元组类型。然而，接口更适合于定义对象类型或利用声明合并。

## 延伸阅读:

*   像专家一样使用打字键
*   [打字班——从零到英雄](/typescript-classes-from-zero-to-hero-a429a3c96189)
*   [使用类和装饰器的下一级 Typescript 运行时类型验证](/next-level-your-typescript-runtime-type-validation-using-class-and-decorators-ddd2ce3c86f3)
*   掌握类型脚本泛型:终极指南
*   [打字技巧和提示:立即成为专业人士](https://bootcamp.uxdesign.cc/typescript-tricks-and-tips-become-a-pro-in-no-time-5390aba151be)
*   [TypeScript 中的泛型——愚蠢简化的基础知识](/generics-in-typescript-must-know-fundamentals-stupidly-simplified-e7b4d7ffc0e3)
*   [Typescript 遗漏了这一点，但你不应该—运行时类型验证](/typescript-missed-this-but-you-shouldnt-runtime-type-validation-aa8a81ce4289)
*   [Typescript 枚举陷阱和解决方案必须知道](/typescript-enum-pitfalls-and-solutions-must-know-bb971cb0f7d2)
*   [掌握类型脚本泛型—终极指南—基本接口技术](https://bootcamp.uxdesign.cc/mastering-typescript-generics-the-ultimate-guide-essential-interface-techniques-86e793cf1fc)
*   【Javascript 开发者经常忽略的 Typescript 特性
*   掌握打字稿中的交集和并集类型:终极指南和基本技巧

如果你觉得这个指南有帮助，请鼓掌并跟我来。通过[链接](https://medium.com/@caopengau/membership)加入 medium，获取我和所有其他优秀作家在 medium 上发表的所有优质文章。