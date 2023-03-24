# 使用类型脚本—接口和抽象类

> 原文：<https://levelup.gitconnected.com/using-typescript-interfaces-and-abstract-classes-7e9c0ae381da>

![](img/8d19343a09cf3debcfa4e4665aa7faf9.png)

图为 [Duy Pham](https://unsplash.com/@miinyuii?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

TypeScript 是 JavaScript 的自然扩展，在许多项目中取代了 JavaScript。然而，并不是每个人都知道它实际上是如何工作的。

在本文中，我们将研究如何在 TypeScript 中使用接口和抽象类。

# 扩展接口

像类一样，接口也可以扩展。我们使用与扩展类相同的方法。

例如，我们可以写:

```
interface Person {
  name: string;
  getName(): string;
}interface Owner extends Person {
  item: string;
  getItemDetails(): string;
}
```

我们像对待类一样使用`extends`关键字。

意思是一样的。父接口的成员由子接口继承。

例如，我们可以写:

```
interface Person {
  name: string;
  getName(): string;
}interface Owner extends Person {
  item: string;
  getItemDetails(): string;
}class ThingOwner implements Owner {
  constructor(public name: string, public item: string) {} getName() {
    return this.name;
  } getItemDetails() {
    return this.item;
  }
}
```

我们实现了每个接口的所有成员。

关键字`implements`意味着我们必须实现子接口中的所有东西，包括继承的成员。

# 界面和形状类型

界面和形状类型是不同的，即使它们看起来很相似。

接口可以和`implements`关键字一起使用，让一个类实现接口中的所有东西。

形状类型只能直接分配给变量。但是，界面可以符合形状类型。

例如，我们可以对形状类型使用`extends`关键字:

```
type Person = {
  name: string;
  getName(): string;
};interface Owner extends Person {
  item: string;
  getItemDetails(): string;
}class ThingOwner implements Owner {
  constructor(public name: string, public item: string) {} getName() {
    return this.name;
  } getItemDetails() {
    return this.item;
  }
}
```

`extends`关键字表明我们的接口包含了 shape 类型的所有成员。

# 可选的接口属性和方法

接口属性和方法可以是可选的。`?`表示它是可选成员。

例如，我们可以写:

```
type Person = {
  name: string;
  getName?(): string;
};interface Owner extends Person {
  item: string;
  getItemDetails?(): string;
}class ThingOwner implements Owner {
  constructor(public name: string, public item: string) {} getName() {
    return this.name;
  } getItemDetails() {
    return this.item;
  }
}
```

我们把`getName`和`getItemDetails`做成可选的，带有`?`符号。

可以通过接口类型定义可选的接口功能，而不会导致编译器错误。但是我们必须确保我们没有收到`undefined`值，因为它们可能不存在于返回的对象中。

# 抽象接口实现

我们可以在代码中使用抽象接口。

我们可以对抽象类的成员使用`abstract`关键字，这样实现就在具体的类中，而不是抽象类本身。

例如，我们可以写:

```
interface Person {
  name: string;
  getName(): string;
}abstract class Owner implements Person {
  name: string;
  abstract getName(): string;
}class ThingOwner implements Owner {
  constructor(public name: string, public item: string) {} getName() {
    return this.name;
  }
}
```

我们有一个实现了`Person`接口的`Owner`抽象类。

`ThingOwner`类用`getName`的具体实现实现了抽象类。

![](img/321a81d9dd7cd6093a6707fa79d91b11.png)

由[布鲁克·拉克](https://unsplash.com/@brookelark?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 动态创建属性

JavaScript 允许通过给未使用的属性名赋值来在对象上创建新的属性。

使用 TypeScript，我们可以通过提供索引签名来允许接口上的动态属性。

例如，我们可以写:

```
interface Person {
  name: string;
  [prop: string]: any;
}
```

向`Person`接口添加索引签名。

该行:

```
[prop: string]: any;
```

是索引签名，它允许我们将任何字符串属性添加到类型为`Person`的任何内容中。

我们可以给动态属性赋值。

# 结论

我们可以像对待子类一样扩展接口。

此外，我们可以用接口实现形状类型。

接口可以加强类和对象的结构。

此外，抽象类可以加强对象的结构。