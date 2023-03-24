# 编写更好的 JavaScript 的方法—使用 TypeScript

> 原文：<https://levelup.gitconnected.com/ways-to-write-better-javascript-use-typescript-40f89193771a>

![](img/014718655f560bd6ade35f2f2e1c712e.png)

Erik Lucatero 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们编写 JavaScript 的方式总是可以改进的。随着语言的发展和更方便的特性的增加，我们也可以通过使用有用的新特性来改进。

在本文中，我们将研究一些使用 TypeScript 编写更好的 JavaScript 的方法。

# 使用类型脚本

TypeScript 是 JavaScript 的自然扩展。它让我们编写类型安全的 JavaScript 代码。因此，我们可以使用它来防止大量的数据类型错误，如果我们不使用 TypeScript，这些错误就会发生。

此外，它还提供了自动完成功能，否则就不像许多库那样具有自动完成功能。他们使用 TypeScript 类型定义为文本编辑器和 ide 提供自动完成功能，使我们的生活变得更加轻松。

TypeScript 并没有把 JavaScript 变成一种不同的语言。它所做的就是通过各种类型检查特性给 JavaScript 添加类型检查。

因此，所有用于 JavaScript 的知识都适用于 TypeScript。

例如，我们可以创建一个带有类型注释的函数，如下所示:

```
const foo = (num: number): number => {
  return num + 1;
}
```

在上面的代码中，我们有一个带有被设置为类型`number`的`num`参数的`foo`函数。我们还通过在`:`后指定类型，将返回类型设置为`number`。

然后如果我们用数字调用函数，TypeScript 编译器会接受代码。

否则，它会拒绝代码，不会构建代码。这很好，因为 JavaScript 不会阻止这种情况发生。

# 接口

TypeScript 为我们提供了接口，这样我们就可以知道对象的结构，而无需记录对象或检查值。

例如，我们可以创建一个，如下所示:

```
interface Person {
    name: string;
    age: number;
}
```

那么我们可以如下使用它:

```
const person: Person = { name: 'jane', age: 10 }
```

如果我们错过了这些属性中的任何一个，那么当 TypeScript 编译器正在寻找它们时，我们将得到一个错误。

我们还可以使用它来强制实现一个类，如下所示:

```
interface PersonInterface {
    name: string;
    age: number;
}class Person implements PersonInterface {
    name: string;
    age: number;
    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
    }
}
```

在上面的代码中，我们有`name`和`age`字段。如果我们跳过其中的任何一个，那么我们将从 TypeScript 编译器得到一个错误。

如果我们想接受 JavaScript 的动态类型特性，我们可以在 JavaScript 中添加动态索引签名。此外，还有联合和交集类型，可以将不同的类型组合成一个类型。

例如，我们可以如下使用它:

```
interface PersonInterface {
    name: string;
    age: number;
    [key: string]: any;
}
```

在上面的代码中，我们有:

```
[key: string]: any;
```

允许在任何实现有值的`PersonInterface`的东西中使用动态键。

然后我们可以在任何实现了`PersonInterface`的类中拥有除了`name`和`age`之外的任何属性，或者拥有一个被转换为`PersonInterface`类型的对象。

联合类型让我们将不同的类型连接在一起。例如，我们可以如下使用它:

```
interface Person {
    name: string;
    age: number;    
}interface Employee {
    employeeId: string;
}const staff: Person | Employee = {
    name: 'jane',
    age: 10,
    employeeId: 'abc'
}
```

在上面的代码中，`|`是联合类型的操作符。它允许我们将两个接口中的两个键合并成一个，而无需创建新的类型。

TypeScript 的另一个优点是可空属性。我们可以用`?`操作符使属性可选。

例如，我们可以使用下面的代码:

```
interface Person {
    name: string;
    age?: number;    
}
```

使用`?`操作符，我们将`age`设置为可选属性。

![](img/f865cfb11142e1c557e6b8fd1f61425a.png)

照片由 [Pereanu Sebastian](https://unsplash.com/@sebastian123?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 运算符的类型

TypeScript 的另一个很棒的特性是`typeof`操作符，它让我们可以指定某个东西与另一个东西具有相同的类型。

例如，我们可以如下使用它:

```
const person = {
    name: 'jane',
    age: 10,
}const person2: typeof person = {
    name: 'john',
    age: 11,
}
```

在上面的代码中，我们有`person2`对象，它与`person`具有相同的类型，因为我们用`typeof person`指定了它。那么`person2`必须有`name` 和`age` 属性，否则我们会得到一个错误。

正如我们所看到的，我们不需要明确地指定任何接口或类来指定类型。这对于获取没有类型定义的导入库的类型很方便。

# 结论

有了 TypeScript，我们使重构变得容易，因为用它提供的类型和结构检查来破坏现有代码变得更加困难。

这也使得交流更加容易，因为我们知道我们的对象、类和函数返回值的类型和结构。