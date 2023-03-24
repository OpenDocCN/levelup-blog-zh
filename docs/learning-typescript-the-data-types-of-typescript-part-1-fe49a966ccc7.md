# 学习 TypeScript:TypeScript 的数据类型第 1 部分

> 原文：<https://levelup.gitconnected.com/learning-typescript-the-data-types-of-typescript-part-1-fe49a966ccc7>

![](img/4b6673073b868e4f56d289110902f42e.png)

马修·施瓦茨在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

JavaScript 和 TypeScript 的主要区别之一是 TypeScript 有一个更健壮的类型系统。在本文中，我将介绍几种实际上不描述数据的 TypeScript 数据类型，并提供每种类型的示例。在我的下一篇文章中，我将完成对各种类型脚本类型的介绍，包括标准类型，如`number`、`string`、`object`等。

# 数据类型的简要定义

在开始讨论 TypeScript 中的数据类型和数据类型之前，让我花点时间来定义什么是数据类型。许多人认为数据类型只是描述了可以存储在变量中的数据的种类，但实际上，数据类型的定义不仅包括数据的种类，还包括可以对数据执行的操作。

例如，数字类型描述 100、3.14159 和 2，234，452 等值。但是数字类型也描述了可以对数字执行的操作，例如加、减、乘、除和取模。

字符串类型描述文本数据。它还描述了可以对字符串执行的操作，如连接和各种字符串函数，如小写和计算切片和子字符串。但是你不能对一个数进行算术运算，比如求模。

理解了数据类型描述了数据的种类和对数据的合法操作，就更容易理解如何使用数据类型，以及为什么数据类型对编程语言很重要。

# TypeScript 是一种强大的静态类型编程语言

计算机编程语言通常通过它们的类型系统来区分。如果您熟悉 JavaScript，您会知道它是一种弱动态类型的编程语言。让我们花几分钟来描述一下这意味着什么。

弱类型语言是一种可以为变量分配不同类型数据的语言。一个变量可以一开始保存一个数字，然后被赋予一个字符串，后来甚至被赋予其他类型。下面是一个例子(使用 Spidermonkey):

```
let x = 25;
print(x); // displays 25
x = "Hello, world!";
print(x); // displays Hello, world!
x = true;
print(x); // displays true
```

当我运行这段代码时没有错误。JavaScript 很容易将 x 从一种类型转换成另一种类型，只需要我给变量分配一个不同的类型。

弱类型语言的另一个特征是，变量可以在没有声明的情况下突然出现。这可能会导致令人尴尬的错别字，如下例所示:

```
let number = 20;
numbr = (number / 2) + 5;
print(number); // displays 20 not 15
```

这里我输入了错误的变量名，但是 JavaScript 没有抱怨。它只是创建了变量`numbr`，并根据算术表达式给它赋值。

另一方面，TypeScript 是一种静态类型的语言，也是强类型的。这意味着我不能随意地通过赋值来改变变量的类型。这段代码导致了 TypeScript 中的错误:

```
let x = 25
x = "Hello, world!"
```

一旦我将`x`定义为一个数字，我就不能返回给它一个字符串值。

我也不能魔术般地给一个最初没有声明的变量赋值。所以上面的代码在数字输入错误的地方`numbr`也会导致一个打字错误。

强类型、弱类型、动态类型和静态类型的定义归入*类型安全*的总框架下。如果不能将数据类型的赋值混合到变量中，并且不能使用没有首先声明的变量，那么编程语言就是类型安全的。

现在，我已经为您提供了不同类型系统和类型安全的概述，让我们看看 TypeScript 提供的各种数据类型，但首先我需要花一点时间讨论 TypeScript 如何将类型赋给变量。

# TypeScript 如何分配类型

TypeScript 中的数据类型可以显式声明，也可以推断出来。推断数据类型是所有 JavaScript 程序员都习惯的。这里有一个例子:

```
let x = 23; // the type is inferred to be number
let name = "Jane"; // the type is inferred to be string
let flag = false; // the type is inferred to be boolean
```

JavaScript 程序员不会显式声明变量的类型，但是系统会从分配给变量的数据中推断出一个类型，并在后台对变量进行类型化。您可以通过调用`typeof`操作符来观察变量的数据类型，如下例所示:

```
let number = 100;
print(typeof number); // displays number
let name = "Jane";
print(typeof name); // displays string
let flag = false;
print(typeof flag); // displays boolean
```

JavaScript 类型是从存储在变量中的数据推断出来的。

TypeScript 还可以推断类型，如下面的代码所示:

```
let number = 100;
console.log(typeof(number)); // displays number
let firstName = "Jane";
console.log(typeof(firstName)); // displays string
let flag = false;
console.log(typeof(flag)); // displays boolean
```

不同之处在于，这些变量现在不能像 JavaScript 中允许的那样存储不同类型的数据。

当您想要显式声明变量的数据类型时，可以使用类型注释来实现。以下是类型注释的一些示例:

```
let salary :number = 45000
let lastName :string = "Jones"
let flag :boolean = true;
```

函数参数和函数返回类型也可以被注释:

```
function ctof(temp :number) :number {
  return (9.0/5.0) * temp + 32.0;
}
```

完成概述后，让我们继续讨论各种类型脚本数据类型。

# 任何数据类型

TypeScript 中最通用的数据类型是`any`。`any`类型不会被推断出来，但是可以通过注释来声明。当用`any`键入程序的数据类型时，TypeScript 的行为就像 JavaScript 一样，这意味着当可能完成计算时，它会尝试将值转换成适当的类型。

为了演示，这里有一个 TypeScript 格式的程序，如果输入正确，它通常不会编译。相反，变量和数组被类型化为`any`,所以程序试图执行计算。代码如下:

```
let number :any = 1
let nums :any = [1,2,3]
console.log(number * nums)
```

输出是:

```
NaN
```

如果没有 any 声明，这个程序将无法编译，因为 TypeScript 知道不能将一个数乘以整个数组。

几乎在任何情况下都应该避免使用 any 类型。

# 未定义的类型

如果你声明了一个变量但是没有给它赋值，TypeScript 会推断出这个变量的类型是`undefined`。例如:

```
let salary
console.log(typeof(salary)) // displays undefined
```

为变量赋值后，TypeScript 将根据分配给变量的表达式“重新推断”适当的数据类型，如下例所示:

```
let salary
console.log(typeof(salary)) // displays undefined
salary = 30000
console.log(typeof(salary)) // displays number
```

如果你试图对一个没有声明类型或者没有推断类型的变量进行计算，计算将返回`NaN`或者`undefined`，这取决于计算中使用的数据类型。

# 未知类型

如果你需要声明一个变量，但是你不确定在声明的时候它的数据类型是什么，你应该使用`unknown`类型。当您将一个变量声明为`unknown`时，它将被 TypeScript 类型化为未定义的，直到您为它赋值，在这种情况下，TypeScript 将从该值推断出它的新类型。

一个变量永远不会被推断为一个`unknown`，所以当你创建一个新的变量时，你必须声明它。

下面是一个工作原理的例子:

```
let number :unknown
console.log("number type: ",typeof(number))
number = 1
console.log("number type: ",typeof(number))
```

这个程序的输出是:

```
number type:  undefined
number type:  number
```

您可以对`unknown`变量执行一些操作，包括比较操作和求反，但是您不能做其他任何事情。

如果你想让你的程序尽可能的类型安全，并且在声明的时候输入所有的变量，那么选择`unknown`而不是`any`。

这就结束了我的 TypeScript 数据类型概述的第 1 部分。在我的下一篇文章中，我将介绍 TypeScript 中的其余数据类型。

感谢您的阅读，请回复这篇文章或发邮件给我，告诉我您的意见和建议。