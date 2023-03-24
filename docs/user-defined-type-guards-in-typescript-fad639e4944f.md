# Typescript 中用户定义的类型保护

> 原文：<https://levelup.gitconnected.com/user-defined-type-guards-in-typescript-fad639e4944f>

## 打字稿指南

## 如何使用强大的 Typescript 功能来改进应用程序中的类型处理

![](img/7179275897ad74f7b9988044549cfd51.png)

类型保护是类型安全代码的关键特性之一。它们允许您缩小条件块中对象的类型。[打字稿手册](https://www.typescriptlang.org/docs/handbook)将打字防护描述为:

> 执行运行时检查以保证某个范围内的类型的某个表达式

该描述最重要的部分是类型保护实际上是一种运行时检查的形式，这意味着变量在代码执行时是预期的类型。与其使用`any`关键字来强制 TypeScript 编译器停止抱怨未知属性，不如使用类型保护更具可读性。它们不会禁用应用程序代码中的类型检查，因此减少了出错的可能性。您的代码运行，您的测试通过。编译器开心，你也开心。

好哇🎉

TypeScript 带有内置的类型守卫，如`typeof`、`instanceof`、`in`和文字类型守卫。它们非常有用，但是在现代 web 开发中作用有限。

# 使用实例 of

`instanceof`可以用来检查一个变量是否是一个给定类的实例。`instanceof`操作符的右边需要是一个构造函数。

打印到控制台:

```
woof
meow
```

TypeScript 编译器以同样的方式处理`if-else`块。例如，如果不是`instanceof` `Dog`，它会将变量类型缩小到`Cat`:

# 使用类型 of

当需要区分`number`、`string`、`boolean`、`bigint`、`object`、`function`、`symbol`、`undefined`类型时，使用`typeof`。当试图使用其他字符串常量时，`typeof`操作符不会出错，但也不会按预期工作，这导致了难以跟踪的错误。这些类型的表达式不会被识别为类型保护。

与`instanceof`不同，`typeof`可以处理任何类型的变量。在下面的例子中，`foo`可以被键入为`number | string`而没有问题。

这将打印:

```
123
not a number: foo
```

棘手的部分是`typeof`只执行浅层类型检查。它可以确定一个变量是否是一个通用对象，但不能判断对象的形状。这是行不通的:

```
if (typeof foo === 'Dog')
```

# 使用于

`in`操作符对一个`union`类型的对象进行安全检查。在这种情况下，`if`分支缩小到将请求属性作为可选或必需属性的类型，例如:

# 使用文字类型保护

此外，您可以使用`===`、`==`、`!==`和`!=`来区分文字值，从而形成简单的类型保护，如下例所示:

这甚至适用于更复杂的例子，比如在联合中使用文字类型。您可以检查共享属性名的值来区分联合，如下所示:

TypeScript 编译器足够聪明，可以通过`== null`和`!= null`检查来排除`null`和`undefined`，例如:

# 用户定义的防护类型

在实际项目中，您可能希望用自定义逻辑声明自己的类型保护，以帮助 TypeScript 编译器确定类型。您将需要使用您喜欢的任何逻辑来声明一个用作类型保护的函数。此函数— *用户自定义类型守护函数—* 是一个以`event is MouseEvent`的形式返回一个*类型谓词*来代替返回类型的函数:

```
function handle(event: any): event is MouseEvent {
    // body that returns boolean
}
```

如果函数返回`true`，TypeScript 将在由函数调用保护的任何块中将类型缩小到`MouseEvent`。换句话说，类型会更具体。`event is MouseEvent`确保编译器传递给类型保护的`event`实际上是一个`MouseEvent`。例如:

这将打印:

```
woof
don’t know what this is! [[object Object]]
```

守卫的函数类型谓词(函数返回类型位置的`test is Dog`)在编译时用于缩小类型，而函数体在运行时使用。类型谓词和函数必须一致，代码才能工作。

类型守卫函数不一定要用`typeof`或者`instanceof`，可以用更复杂的逻辑。例如，这段代码检查对象属性以确定它是否是`Dog`。

# 摘要

作为 TypeScript 最被低估的特性之一，类型保护对于缩小类型和满足编译器的要求非常有用。它们有助于确保类型安全，并促进代码更易于推理和维护。

有内置类型的防护装置，如`typeof`和`instanceof`，但它们的用途有限。用户定义的类型保护可以用自定义类型谓词(`event is MouseEvent`)和所需类型的唯一属性来定义。

要创建自定义类型保护:

1.  编写一个用谓词类型代替返回类型的函数，例如`event is MouseEvent`
2.  使用`typeof`、`instanceof`或任何其他产生`boolean`值的函数运算符，实现可以确定作为参数传递的变量类型的逻辑

使用类型保护和 TypeScript 编译器的强大功能会使您的应用程序可读性更好，更不容易出错。

# 参考

*   官方[打字手册](https://www.typescriptlang.org/docs/handbook)在[typescriptlang.org](https://www.typescriptlang.org)