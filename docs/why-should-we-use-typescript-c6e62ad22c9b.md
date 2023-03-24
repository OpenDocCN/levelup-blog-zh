# 为什么要用 TypeScript？

> 原文：<https://levelup.gitconnected.com/why-should-we-use-typescript-c6e62ad22c9b>

![](img/efac84f8b7acc0bf4061ab6db18a5074.png)

[Zan](https://unsplash.com/@zanilic?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

JavaScript 在数据类型方面存在问题。人们对此感到困惑，并且由于缺乏固定的类型，自动完成功能也缺乏。

在本文中，我们将研究为什么 TypeScript 可以改善开发人员的体验。

# 与 JavaScript 兼容

TypeScript 是 JavaScript 的扩展。它不会破坏 JavaScript 中的任何东西。

它所做的只是扩展 JavaScript 语法以包含类型。类型可以是静态或动态的，即使使用 TypeScript 也是如此。

# 键入注释

TypeScript 为我们提供了接口，允许我们以灵活的方式键入对象。

我们可以创建具有固定和必需属性、可选属性或带有索引签名的动态属性的接口。

例如，我们可以编写如下的基本接口:

```
interface Person {
    firstName: string;
    lastName: string;
}
```

在上面的代码中，我们有两个固定的属性，`firstName`和`lastName`，它们是必需的，并且是字符串。

那么当我们用`Person`类型定义一个对象时如下:

```
const person: Person = { firstName: 'Jane', lastName: 'Smith' };
```

我们在键入代码时会自动完成，TypeScript 编译器也会检查对象的结构。

如果我们向上面的`person`对象添加额外的属性，我们会得到一个错误。

此外，我们可以定义包含方法的接口，如下所示:

```
interface Person {
    firstName: string;
    lastName: string;
    fullName(): string;
}
```

然后，TypeScript 编译器会要求我们添加一个名为`fullName`的方法，返回一个字符串。`person`看起来像这样:

```
const person: Person = {
    firstName: 'Jane',
    lastName: 'Smith',
    fullName() {
        return `${this.firstName} ${this.lastName}`;
    }
};
```

# 灵活性

接口还可以具有如下动态属性:

```
interface Person {
    firstName: string;
    lastName: string;
    [propName: string]: any;
}
```

在上面的代码中，`propName`是一个可以是任何类型的字符串键。

这保持了 TypeScript 的灵活性，因此我们不必花费太多时间来添加动态对象的类型，例如用作字典。

`propName`也可以是类型号的符号。

JavaScript 类和实现接口，所以它们需要有给定的字段和方法。

例如，如果我们有:

```
interface PersonInterface {
    firstName: string;
    lastName: string;
    fullName(): string;
}class Person implements PersonInterface {
    firstName: string;
    lastName: string;
    constructor(firstName: string, lastName: string) {
        this.firstName = firstName;
        this.lastName = lastName;
    } fullName() {
        return `${this.firstName} ${this.lastName}`;
    }
}
```

然后 TypeScript 编译器将编译代码，因为我们的`Person`类完全按照指定实现了`PersonInterface`的所有成员。

我们还可以在名称后加上问号，使成员可选，如下所示:

```
interface PersonInterface {
    firstName?: string;
    lastName?: string;
    fullName?(): string;
}
```

在上面的代码中，我们让`PersonInterface`中的所有成员都是可选的。

# 类型推理

我们可以使用`typeof`操作符来注释某种类型的东西，而不需要定义我们自己的类型。

例如，我们可以写:

```
const foo = {
    a: 1, b: 2
}const bar: typeof foo = {
    a: 1, b: 3
}
```

然后`bar`被要求具有与`foo`相同的属性和值类型，但是我们不需要明确地定义一个带有接口或类的类型。

# 自动完成

有了上面讨论的数据类型注释特性，文本编辑器和 ide 可以在我们输入代码时自动完成。

拥有自动完成功能很重要，因为我们不必查看每个成员是如何拼写的，以及函数接受什么参数。

这使得编写代码更快，更不容易出错，因为我们有自动完成功能来防止这些错误。

![](img/2479b545cd49994c4b1d56c1abc2f029.png)

蒂姆·范德奎普在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 无商标消费品

我们可以在代码中添加泛型类型，这样我们就可以编写可以插入任何数据类型的泛型代码。

例如，我们可以写:

```
function identity<T> (arg: T): T {
    return arg; 
}
```

它只返回传入的相同内容。

那么我们可以如下使用它:

```
let foo = identity<string>("foo");
```

我们在泛型函数中加入了`string`类型，这样我们就可以返回任何传入的字符串。

# 构建目标和已构建代码的向后兼容性

TypeScript 编译器支持将代码编译成 1999 年 12 月发布的 ES3 代码。所以编译出来的神器向后兼容性还是不错的。

编译成 ES3 的代码可以在任何情况下工作。然而，将代码编译成 ES3 应该没有太多的用例。最像 ES5 的将是目标。

# 结论

TypeScript 单独使用这些功能非常有用。

它让我们可以灵活地键入对象，并将代码编译成非常旧的 JavaScript 版本。

很多框架和库都支持它，比如 Angular 和 Vue。许多库都附带了 TypeScript 类型定义来让我们进行自动完成和类型检查。