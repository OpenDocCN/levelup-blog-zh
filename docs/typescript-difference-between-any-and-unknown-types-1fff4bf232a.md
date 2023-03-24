# TypeScript——“任何”和“未知”类型之间的区别

> 原文：<https://levelup.gitconnected.com/typescript-difference-between-any-and-unknown-types-1fff4bf232a>

![](img/1a688edc63405491744c700f8c6ff122.png)

[Brad Helmink](https://unsplash.com/@bradhelmink?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我猜你已经知道 TypeScript 是 [JavaScript](https://www.w3schools.com/js/default.asp) 的“风味”。

本文假设您对这两者或多或少都很熟悉。但是，我将首先简要介绍一下 TypeScript。

基本上，TypeScript 是添加了类型语法的 JavaScript。 [W3Schools](https://www.w3schools.com/) 将 TypeScript 定义为 JavaScript 的语法超集，增加了静态类型。这基本上意味着 TypeScript 在 JavaScript 之上添加了语法，允许开发人员添加类型。

# TypeScript 基本类型

下面是 TypeScript 中的主要基元类型:

**布尔值—** 真值或假值

**数字—** 整数和浮点值

**字符串—** 用引号括起来的文本值。例如`"TypeScript is Awesome"`

**any** —禁用类型检查，有效地允许使用所有类型

未知的——类似于任何一种选择，但更安全

**never** —有效地抛出一个定义的错误

你一定注意到了上面对`any`和`unknown`类型的描述之间的模糊性。这就把我们带到了今天讨论的核心。

# “任何”和“未知”类型之间的区别

我将介绍`any`和`unknown`的两个主要区别

1.  TypeScript 强迫我们在处理`unknown`值之前确定它的类型，但是它不处理`any`值。

***例如:***

```
let anyValue: any = 1;
let unknownValue: unknown = 1;anyValue.helloworld(); *// No error*
unknownValue.helloWorld();  *// we get a TypeScript error*
```

2.我们不能将`unknown`赋给除了它本身或`any`之外的任何东西，但是我们可以将`any`赋给任何东西。

***举例:***

```
let anyValue: any = 1;
let unknownValue: unknown = 1;let str: string;str = anyValue; *// No error*
str = unknownValue; *// We get a TypeScript error*
```

如您所见，这些约束确保我们在使用`unknown`值之前对其进行验证，这对于减少运行时错误的可能性非常重要。

让我们看几个使用案例:

# **用例 1-描述未知值:**

我们可以使用`unknown`类型来描述一个我们不知道的值。例如，从网络调用返回的值:

```
let data: unknown = getSomeValueFromSomeWhere();if (typeof data === 'string') {
   //data is a string.
} else if (Array.isArray(data)) {
   //data is an array.
} else if (typeof data === 'object') {
   //data is an object.
}
```

# 用例 2 —在类型断言中:

在 TypeScript 中，[类型断言](https://www.geeksforgeeks.org/explain-type-assertions-in-typescript/#:~:text=In%20Typescript%2C%20Type%20assertion%20is,compiler%20not%20to%20deduce%20it.)是一种通知编译器变量类型的技术。类型断言类似于类型转换，但它不重构代码。我们可以使用类型断言来指定一个值的类型，并告诉编译器不要推断它。

但是，我们不能简单地断言一个值可以有我们想要的任何类型。我们不能断言一个值的类型与该值的实际类型不重叠。

***举例:***

```
let value = '1' as number; /*/ Error, we can't say that a string is a number*interface Vehicle {
  name: string;
  model: string;
}function buyVehicle(vehicle: Vehicle){}buyVehicle({niceVehicle: ''} as Vehicle); *// Error, this object doesn't have the required properties of the Vehicle interface.*
```

断言一个值的类型不与该值的实际类型重叠的一种解决方法是首先断言该值是未知的:

```
buyVehicle({niceVehicle: ''} as unknown as Vehicle)
```

然而，我们应该避免这种变通方法，因为它只会使我们的代码不安全。

# **结论**

类型`any`和`unknown`是 TypeScript 中的两种基本类型，有时会派上用场。

`any`可能是一种有用的方法，因为它禁用了类型检查，但 TypeScript 将无法提供类型安全，并且依赖于类型数据的工具(如自动完成)将无法工作。

`unknown`最适合在我们不知道数据类型的时候使用。要在以后添加类型，我们需要转换它。

**NB:** 我们要尽量避免使用`any`。

— -

希望你对这篇文章感兴趣，如果你感兴趣，请留下一些掌声，如果你还不知道，请不要忘记关注我，这样你就可以在我每次发帖时获得更多精彩的内容。谢了。