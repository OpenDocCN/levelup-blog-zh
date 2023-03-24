# 在 TypeScript 中实施类实现—TypeScript 接口简介

> 原文：<https://levelup.gitconnected.com/introduction-to-typescript-interfaces-enforcing-class-implementation-b41f9e290bf9>

![](img/e5515e781819d88b274ab2bf31fdadd9.png)

[Jason Hafso](https://unsplash.com/@jasonhafso?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

与普通 JavaScript 相比，TypeScript 的最大优势在于，它通过为我们的程序对象添加类型安全来扩展 JavaScript 的特性。它通过检查对象所呈现的值的形状来做到这一点。检查形状被称为鸭分型或结构分型。

接口是在 TypeScript 中扮演命名数据类型角色的一种方式。这对于在 TypeScript 程序的代码中定义契约非常有用。在本文中，我们将看看如何使用接口来加强类的实现。

# 类别类型

我们可以使用 TypeScript 接口来确保类满足接口指定的特定约定，就像在其他编程语言中一样，比如 C#和 Java。例如，我们可以用关键字`implements`定义一个类来实现接口指定的项目，就像我们在下面的代码中所做的那样:

```
interface PersonInterface {
  name: string;
}class Person implements PersonInterface {
  name: string = 'Mary';    
}
```

在上面的代码中，我们实现了类`Person`，它实现了`PersonInterface`中列出的项目。因为`PersonInterface`有一个字符串字段`name`，所以我们遵循在它下面实现的`Person`类中的契约。上面的代码实现了接口中概述的所有项目，因此 TypeScript 编译器会将上面的代码视为有效。如果我们没有实现接口中的项目，而是使用了`implements`关键字，那么我们将得到一个错误:

```
interface PersonInterface {
  name: string;  
  age: number;  
}class Person implements PersonInterface {    
  name: string = 'Mary';  
  foo: any = 'abc';
}
```

例如，如果我们有上面的代码，那么 TypeScript 编译器将错误地拒绝它，因为我们在`Person`中只有`name`字段，但是没有`age`字段，而是有`foo`字段。因此，如果我们试图编译上面的代码，我们会得到一个错误消息“类‘Person’错误地实现了接口‘Person interface’。类型“Person”中缺少属性“age”，但类型“PersonInterface”中需要该属性。

然而，在我们实现了接口中的所有项之后，我们可以添加接口中未指定的额外项，并且 TypeScript 编译器将接受代码:

```
interface PersonInterface {
  name: string;    
}class Person implements PersonInterface {    
  name: string = 'Mary';  
  age: number = 20;
}
```

上面的代码会被 TypeScript 编译器接受，因为我们在接口中有所有的东西，并且我们向类中添加了接口中没有的东西，这在 TypeScript 中是可以接受的。

我们也可以用一个接口描述我们将在类中实现的方法。为此，我们可以在接口中添加方法签名及其返回类型，如下面的代码所示:

```
interface PersonInterface {
  name: string;
  age: number;
  setName(name: string): string;
  setAge(age: number): number;
}class Person implements PersonInterface {    
  name: string = 'Mary';  
  age: number = 20;
  setName(name: string) {
    this.name = name;
    return name;
  } setAge(age: number) {
    this.age = age;
    return age;
  }
}
```

在上面的代码中，我们将在实现接口的类中实现的 2 个方法签名放在`PersonInterface`中。我们有一个用于`setName`方法的方法签名，还有一个用于`setAge`方法的方法签名。在每个方法的括号内，我们指定了每个方法所需的参数。在`setName`方法中，我们指定我们需要`name`参数，并且是字符串类型。在`setAge`方法中，我们指定我们有一个`age`参数，并且是 number 类型。那是结肠左边的部分。在冒号的右边，我们有每个方法的返回类型。对于`setName`方法，我们指定它返回一个字符串。在`setAge`方法的签名中，我们指定它返回一个数字。

然后在`Person`类中，我们只是按照接口中的描述执行。所以我们添加了`name`和`age`字段，我们分别将它们指定为类型字符串和数字。然后我们添加`setName`和`setAge`方法，就像我们在`PersonInterface`中概述的那样。请注意，方法参数中的类型指定是必需的，但返回类型不是必需的，因为它可以由 TypeScript 推断，但必须显式检查参数类型。这意味着，如果我们有类似下面这样省略了参数类型的代码:

```
interface PersonInterface {
  name: string;
  age: number;
  setName(name: string): string;
  setAge(age: number): number;
}class Person implements PersonInterface {    
  name: string = 'Mary';  
  age: number = 20;
  setName(name) {
    this.name = name;
    return name;
  } setAge(age) {
    this.age = age;
    return age;
  }
}
```

则 TypeScript 编译器将拒绝代码，并显示错误消息“Parameter 'name '隐式具有' any '类型。(7006)`setName`方法的“和”参数“年龄”隐含有“任何”类型。(7006)".正如我们从错误中看到的，没有附加类型指定的参数将被隐式指定为`any`类型，这不是`PersonInterface`中指定的类型。

接口只描述了类的公共端，而不是公共端和私有端。这意味着我们不能用它来检查私有字段和方法。

![](img/c54ae269032f8d73f4923cabf2e18881.png)

由 [Patrick Tomasso](https://unsplash.com/@impatrickt?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 类的静态端与实例端

在 TypeScript 中，接口只检查类的实例端，所以不检查任何与类的静态端相关的东西。例如，如果我们有以下代码:

```
interface PersonInterface {
  new (name: string, age: number);
}class Person implements PersonInterface {    
  constructor(name: string, age: number) {}
}
```

我们将得到错误“类‘Person’不正确地实现了接口‘Person interface’。类型“Person”不提供签名“new (name: string，age: number)”的匹配，因为类的静态端不能被接口检查，而`constructor`在静态端。相反，我们必须为静态端的每个部分添加不同的接口。我们可以这样做，如下面的代码所示:

```
interface PersonConstructor {
  new (name: string, age: number): PersonInterface;
}interface PersonInterface {
  name: string;
  age: number;
  greet(name: string): void;
}const createPerson = (ctor: PersonConstructor, name: string, age: number): PersonInterface =>{
  return new ctor(name, age);    
}class Person implements PersonInterface {    
  name: string;
  age: number;
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
  greet(name: string) {
    console.log(`Hello ${this.name}. You're ${this.age} years old.`)
  }
}let person = createPerson(Person, 'Jane', 20);
console.log(person);
```

在上面的代码中，`PersonInterface`只有公共成员，分别是`name`、`age`和`greet`方法。这迫使我们在我们的`Person`类中实现这些成员。因为`constructor`是一个静态成员，所以它不会被`PersonInterface`检查。我们将检查留在`createPerson`函数中。在`createPerson`中，我们检查了`ctor`参数是否正确实现了`PersonConstructor`，因为我们在签名中使用了带有`name`和`age`参数的`new`方法，并且我们检查了它是否返回了一个`PersonInterface`，这意味着构造函数正在返回实现`PersonInterface`的某个类的实例。上面的代码将被 TypeScript 编译器接受，因为我们检查静态端与实例端是分开的，因为我们创建了`createPerson`来让我们检查静态端，也就是`constructor`。

TypeScript 的另一个强大特性是，我们可以使用接口来检查我们通过使用接口在类中实现了什么。这对于检查类的非静态成员很有用。然而，对于像`constructor`这样的静态成员，我们必须在我们用来实现类的接口之外检查它们，因为它们不能像`constructor`方法那样检查静态成员。