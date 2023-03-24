# 带类的类型脚本泛型简介

> 原文：<https://levelup.gitconnected.com/introduction-to-typescript-generics-classes-54c30244ae99>

![](img/fd840d4f57f459d5f24f526f7c9ace5f.png)

[乔伊斯·罗梅罗](https://unsplash.com/@joyceromero?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

创建可重用代码的一种方法是创建这样的代码，它允许我们根据自己的需要使用不同的数据类型。TypeScript 提供了创建可重用组件的泛型构造，我们可以用一段代码处理各种类型。这允许开发者通过放入他们自己的类型来使用这些组件。在本文中，我们将研究定义泛型类的许多方法，并通过定义接口和`extends`关键字来验证属性，并通过使用`keyof`关键字来强制代码只接受对象中的属性名。

# 定义泛型类

为了在 TypeScript 中定义泛型类，我们将泛型类型标记放在类名后面的`<>`之间。我们可以用逗号分隔多个类型标记。此外，我们可以使用相同的类型标记来标记函数的类型，以便在我们的类中更改参数和返回方法的类型。例如，我们可以编写以下代码来定义具有单个泛型类型标记的泛型类:

```
class GenericPerson<T> {  
  name: T;
  getName: (name: T) => T;
}let person = new GenericPerson<string>();
person.name = 'Jane';
person.getName = function(x) { return x; };
```

在上面的代码中，我们在`<>`中传递了字符串类型。我们也可以改变成我们想要的任何其他类型，比如`number`、`boolean`，或者任何类似下面代码的接口:

```
class GenericPerson<T> {  
  name: T;
  getName: (name: T) => T;
}let person = new GenericPerson<number>();
person.name = 2;
person.getName = function(x) { return x; };
```

我们还可以在同一个类中插入多种类型，如下面的代码所示:

```
class GenericPerson<T, U> {  
  name: T;
  getName: (name: U) => U;
}let person = new GenericPerson<string, number>();
person.name = 'Jane';
person.getName = function (x) { return x; };
person.getName(2);
```

在上面的代码中，`T`和`U`可能是也可能不是不同的类型。只要我们将它们都放入，并且我们传入并分配具有我们在类定义中指定的类型的数据，那么 TypeScript 编译器将接受它。上面的例子只在有限的情况下有用，因为我们不能在类定义中引用任何属性，因为 TypeScript 编译器不知道`T`或`U`包含什么属性。

为了让 TypeScript 编译器知道可能在我们的类定义中使用的属性，我们可以使用一个接口来列出所有可以与该类一起使用的属性，并在泛型类型标记之后使用`extends`关键字来表示该类型可能具有在接口中列出的属性列表。例如，如果我们希望在类方法中有不同的复杂类型，我们可以编写类似下面的代码:

```
interface PersonInterface {
  name: string;
  age: number;
}interface GreetingInterface {
  toString(): string;
}class GenericPerson<T extends PersonInterface, U extends GreetingInterface> {  
  person: T;
  greet(greeting: U): string {
    return `${greeting.toString()} ${this.person.name}. You're ${this.person.age} years old`;
  }
}let jane = new GenericPerson();
jane.person = {
  name: 'Jane',
  age: 20
};
console.log(jane.greet('Hi'));
```

在上面的代码中，我们定义了两个接口,`PersonInterface`和`GreetingInterface`,分别表示可以被`T`和`U`引用的属性。在`PersonInterface`中，我们列出了`name`和`age`属性，因此我们可以为类型为`T`的数据引用这些属性。对于类型为`U`的数据，我们可以对其调用`toString`方法。因此，在我们的`greet`方法中，我们可以在`greeting`上调用`toString`，因为它具有`U`类型，并且因为`this.person`具有`T`类型，我们可以从中获得`name`和`age`属性。

然后在类定义之后，我们可以实例化该类，然后为我们创建的`jane`对象上的`name`和`age`属性设置值。然后，当我们在`jane.greet(‘Hi’)`上运行`console.log`时，我们应该会看到‘嗨，简。自从我们设定`jane.person`的值后，你已经 20 岁了。

我们也可以在实例化对象时显式地放入类型，以使类型更加清晰。我们可以稍微改变一下上面的内容，编写下面的代码:

```
interface PersonInterface {
  name: string;
  age: number;
}interface GreetingInterface {
  toString(): string;
}class GenericPerson<T extends PersonInterface, U extends GreetingInterface> {  
  person: T;
  greet(greeting: U): string {
    return `${greeting.toString()} ${this.person.name}. You're ${this.person.age} years old`;
  }
}let jane = new GenericPerson<PersonInterface, string>();
jane.person = {
  name: 'Jane',
  age: 20
};
console.log(jane.greet('Hi'));
```

唯一的区别是我们在`new GenericPerson`后面加上了`<PersonInterface, string>`。注意，我们可以在括号之间添加接口或基本类型。TypeScript 只关心类型是否具有我们定义的接口中列出的方法。既然这些类型受到这些泛型类型的约束，我们就不必担心引用任何意外的属性。例如，如果我们在下面的代码中引用了不存在的东西:

```
interface PersonInterface {
  name: string;
  age: number;
}interface GreetingInterface {
  toString(): string;
}class GenericPerson<T extends PersonInterface, U extends GreetingInterface> {  
  person: T;
  greet(greeting: U): string {
    return `${greeting.foo()} ${this.person.name}. You're ${this.person.age} years old`;
  }
}let jane = new GenericPerson<PersonInterface, string>();
jane.person = {
  name: 'Jane',
  age: 20
};
console.log(jane.greet('Hi'));
```

我们会得到“属性‘foo’在类型‘U’上不存在。(2339)“既然我们的`GreetingInterface`里没有列出`foo`。

![](img/c6c1e69943830bc681a0840a4e854475.png)

照片由 [sarandy westfall](https://unsplash.com/@sarandywestfall_photo?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 约束对象属性的检索

TypeScript 还提供了一种方法，让我们可以用泛型安全地获取对象的属性。我们可以使用`keyof`关键字来约束一个对象可以接受的另一个对象的键名的值。例如，我们可以在下面的代码中使用`keyof`关键字:

```
function getProperty<T, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}let x = { foo: 1, bar: 2, baz: 3 };console.log(getProperty(x, "foo"));
```

在上面的代码中，我们约束通用标记`K`只接受传递给`obj`的任何对象的键作为`key`的有效值，因为`K`在`K`之后有`extends keyof T`标记。这意味着无论`T`类型有什么键，这些键都是`K`的有效值。有了上面这样的代码，我们不必担心获取不存在的属性值。因此，如果我们传入一个在`obj`中不存在的键名，如下面的代码所示:

```
getProperty(x, "a");
```

则 TypeScript 编译器将拒绝该代码，并给出错误消息“a 类型的参数不可赋给 foo 类型的参数”| "bar" | "baz "”。(2345)".这意味着只有`'foo'`、`'bar'`和`'baz'`是`key`的有效值，因为它具有类型`K`，类型`K`后面有一个`extends keyof T`标记，用于将有效用法约束为`obj`的键名。

我们可以用 TypeScript 轻松定义一个泛型类。为了在 TypeScript 中定义泛型类，我们将泛型类型标记放在类名后面的`<>`之间。我们可以用逗号分隔多个类型标记。此外，我们可以使用相同的类型标记来标记函数的类型，以便在我们的类中更改参数和返回方法的类型。此外，我们可以使用`extends`关键字来定义属性，这些属性可以被泛型类型标记的数据引用。我们可以使用`keyof`关键字来约束一个对象可以接受的另一个对象的键名的值。