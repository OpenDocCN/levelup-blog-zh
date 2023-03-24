# 学习 TypeScript:TypeScript 数据类型(第 2 部分)

> 原文：<https://levelup.gitconnected.com/learning-typescript-the-typescript-data-types-part-2-4f38b7dfd5bc>

![](img/7463b2f3826218cc63a9e93be96550a3.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

本文是讨论 TypeScript 程序中可用的各种数据类型的系列文章的第二部分。第一条是[此处](/learning-typescript-the-data-types-of-typescript-part-1-fe49a966ccc7)。本文主要关注更传统的数据类型，比如数字、字符串、布尔值，以及产生对象的类型，比如普通对象、数组和元组，后者是 JavaScript 和 TypeScript 中较新的数据类型。

# 布尔类型

`boolean`类型用于存储真值和假值。TypeScript 可以从赋值中推断出`boolean`类型，并且您可以明确地将一个变量声明为`boolean`类型。你也可以声明一个变量是一个特定的`boolean` 类型(或者是`true`或者是`false`)并且这个变量不能被赋予另一个`boolean`值。

以下是一些例子:

```
let flag = true // boolean type inferred
let negative = false // boolean type inferred
let flag :boolean = true // boolean type annotated
const t = true // an inferred type literal
let alwaysTrue :true = true // an annotated type literal
let alwaysFalse :false = false // an annotated type literal
```

您熟悉其中的许多赋值，但可能不熟悉那些引用类型文字的赋值。一个*类型的文字*使用一个特定的值作为数据类型，你可以在 TypScript 中用`true`和`false`来实现。一旦用类型文本声明了变量，该变量就不能保存任何其他值。这里有一个例子:

```
let t :true = true
t = false
```

当我试图编译这段代码时，我得到以下错误消息:

```
test.ts:2:1 - error TS2322: Type 'false' is not assignable to type 'true'.2 t = false
  ~Found 1 error.
```

变量`t`现在是一个真正的类型文字，它不能保存其他值。

在某些情况下，类型文字是您可以在程序中提供的额外的类型安全层。

# 数字类型

`number`型是用来存储数字的。与其他强类型语言不同，TypeScript 只为数字数据提供了`number`类型。不能为整数、浮点数、长整型等指定。

`number`类型允许对其进行常见的数字运算，例如加、减、乘、除和取模，并且`Math`库中有许多数学函数也可以处理数字数据。

与其他数据类型一样，`number`类型可以通过类型注释来推断或指定，如下例所示:

```
let salary = 20000 // number type is inferred
const PI = 3.14159 // number type is inferred
let hours :number = 40 // number type is annotated
const e :number = 2.71828 // number type is annotated
```

您也可以对数值数据使用类型文本:

```
let pi :3.14159 = 3.14159
let standardHours :40 = 40
```

`pi`和`standardHours`的值现在是类型文字，不能更改。这很像创建常量，您可能应该避免以这种方式使用类型文字，而应该使用常量。

# bigint 类型

数字数据类型的大小限制是 253。如果你需要存储一个大于这个数的数，你必须使用`BigInt`类型，它在 TypeScript 中表示为`bigint`。

用`bigint`类型初始化变量有几种方法。以下是一些例子:

```
let n = 4567n 
// an integer with n after it is a BigInt and the type is inferred
const x = 230000n // bigint type is inferred
let z :bigint = 100000n
let s :100n = 100n // a bigint type literal
```

`bigint`类型可以与标准算术运算符(+、-、*、/)一起使用，但模数除外，BigInts 可以与关系运算符一起使用。例如:

```
let x :bigint = 100n
let y :bigint = 200n
let c :bigint = x * y
```

# 字符串类型

`string`类型用于文本数据，可以包含数字。字符串是通过在要作为字符串键入的文本周围加上单引号或双引号而形成的。唯一对字符串起作用的操作符是串联(+)操作符，还有许多函数可以调用来对字符串进行操作，比如`substr`、`slice`、`length`等等。

以下是一些如何创建新字符串变量的示例:

```
let name = "Billy"
let greeting = 'Hello'
let id = '28348'
let last :string = "Pyle"
let firstFiveLetters = "a"+"b"+"c"+"d"+'e'
let fn :mike = "Mike" // a type literal
```

与往常一样，您应该让 TypeScript 从分配的数据中推断字符串类型，而不是在程序中对该类型进行批注。

# 符号类型

`symbol`类型是 JavaScript 中相对较新的数据类型。该类型的主要目的是将对象和映射属性定义为唯一的实体，以便错误地使用一些其他字符串值，这导致 JavaScript 创建一个具有该名称的新属性。

使用`Symbol`功能创建一个`symbol`类型的变量。你不用这个函数调用关键字`new`，只用带值的函数名。以下是一些用`symbol`类型声明变量的例子:

```
let first_name = Symbol("first_name")
let last_name :symbol = Symbol("last_name")
```

您可以让 TypeScript 推断出`symbol`类型，也可以用您的声明对其进行注释。

您还可以将变量声明为唯一的`symbol`类型，如下例所示:

```
const sym :unique symbol = Symbol("unique")
```

唯一的`symbol`是完全唯一的，除了它自己之外，不能与任何其他`symbol`相等。

# 对象类型

`object`类型用于描述对象的形状，我的意思是 TypeScript 关心对象的属性，而不是对象的名称。例如，下面的代码创建两个不同的对象:

```
let student = {
  name : "John Doe",
  major : "Art"
}let dog = {
  name : "Fido",
  speak : "Woof!"
}console.log(typeof(student)) // displays object
console.log(typeof(dog)) // displays object
```

但两个对象的类型都只是 object，而不是每个对象的具体“种类”。这种分型叫做*结构分型*。

您可以使用`objec` t 类型注释，但是这会导致奇怪的行为:

```
let person :object = {
  name: 'Terri'
}
console.log(person.name) // error message: Property name does
                         // not exist on type object
```

但是，如果让 TypeScript 推断数据类型:

```
let person = {
  name: 'Terri'
}
console.log(person.name) // displays Terri
```

TypeScript 将 person 识别为对象，将 name 识别为对象的属性。

# 数组类型

数组实际上不是数据类型，而是一种特殊类型的`object`。在 JavaScript 中，数组可以包含任何类型的值，给数组一种狂野西部的感觉，因为在大多数编程语言中，数组是同构的，这意味着只能在其中存储一种类型的数据。然而，TypeScript 为我们提供了一些加强同质性的选项。

首先，让我们看看如果我们像在 JavaScript 中那样在 TypeScript 中创建数组会发生什么。以下是一些示例代码:

```
let values = []
values.push(1)
values.push("hello")
values.push(true)
console.log(values) // displays [1, "hello", true]
```

我们可以把任何我们想要的数据值放入数组。

让我们在 TypeScript 中使用类型注释来在数组上强制使用单一数据类型。我们可以使用以下通用语法指定数组的数据类型:

*let array-name:data-type[]=[将可选初始值放在此处或留空]*

下面是这种类型注释的一个工作示例:

```
let numbers :number[] = []
numbers.push(100)
numbers.push(200)
numbers.push("hello")// error: Argument of type 'string' is not
                     // assignable to parameter of type 'number'
```

还可以通过用至少一个值初始化数组，让 TypeScript 推断数组的数据类型，如下例所示:

```
let numbers = ["one"]
numbers.push("two")
numbers.push(3) // error: Argument of type 'number' is not
                //assignable to parameter of type 'string'
```

但是，如果您想要(但愿不会这样)一个混合数据类型的数组，该怎么办呢？您可以用`any`类型注释数组:

```
let values :any[] = []
values.push(1)
values.push("two")
value.push(false)
```

这段代码运行时没有任何错误消息。然而，请记住，当你以这种方式声明数组时，你实际上冒着在你的程序中出现奇怪结果的风险。

您还可以通过用您希望允许的数据类型的值初始化数组来限制数组可以容纳的类型。例如，如果我想要一个只保存数字和字符串的数组，我可以这样初始化这个数组:

```
let values = [1, "two"]
```

现在如果我试着写下以下内容:

```
values.push(false)
```

我收到以下错误消息:

```
Argument of type 'boolean' is not assignable to parameter of type 'string | number'.
```

我所做的基本上是创建一个类型为`number`或`string`的数组。这两种类型的值都可以放入数组，但不允许放入其他数据类型。

# 元组

元组是一种数组，其中元素的长度和元素的数据类型是固定的。以下是元组声明示例:

```
let dept :[string, string, string, string] =
          ['Raymond', 'Mike', 'Mayo', 'Danny']
```

大小固定为四个元素，元组的类型为`[string, string, string, string]`。

如果我尝试向元组中添加一个元素:

```
dept = ['Raymond', 'Mike', 'Mayo', 'Danny', 'Cynthia']
```

我得到一个错误消息，指出类型`[string, string, string, string, string]`不可分配给类型`[string, string, string, string]`。

由于 JavaScript 数组的灵活性，元组提供了数组无法提供的大量类型安全性。在许多应用程序中，应该优先使用元组，而不是使用数组。

您可以在元组中提供可选元素，以便元组的元素可以包含可选数量或更少，但不能更多。例如:

```
let grades  :[number, number, number, number, number?] =
  [81, 77, 85, 98, 100]
console.log(grades) // displays 81, 77, 85, 98, 100
grades = [75, 82, 68, 88]
console.log(grades) // displays 75, 82, 68, 88
```

但是如果我试着给元组分配一个第六等级:

```
grades = [71, 73, 83, 88, 91, 99]
```

我得到一个错误消息，指出类型`[number, number, number, number, number, number]`不可分配给类型`[number, number, number, number, [number | undefined]`。

最后，一个元组可以包含 rest 元素，这样您就可以定义一个元组的最小大小，但是元组的最终大小是可选的。例如:

```
let numbers :[number, ...number[]] = [1,2,3]
numbers = [1,2]
numbers = [1]
numbers = [1,2,3,4,5,6,7,8,9,10]
```

如本例所示，通过键入带有 rest 参数的元组，元组可以包含 3 个元素、2 个元素、1 个元素，甚至 10 个元素。使用 rest 参数为表示元组的元素数量提供了最大的灵活性。

# 只读数组和元组

TypeScript 允许您通过使用`readonly`关键字来创建不可变的数组或元组。当数组或元组被声明为只读时，不能在结构中添加或删除数据(当然，元组的大小是固定的)，如果要这样做，必须使用赋值来进行更改，或者使用非可变函数。

以下是一些例子:

```
let numbers :readonly number[] = [1,2,3]
//numbers.push(4) // not allowed because numbers is readonly
numbers = [1,2,3,4] // can assign new data on readonly array
numbers = numbers.concat(5) // can use concat with assignment
console.log(numbers) // displays [1, 2, 3, 4, 5]
// numbers.pop() // not allowed because numbers is readonly
numbers = numbers.slice(0,4)
console.log(numbers) // displays [1, 2, 3, 4]
```

我的 TypeScript 类型介绍的第 2 部分到此结束。在第 3 部分中，我将介绍剩余的 TypeScript 类型，以便我们可以继续使用这些类型来解决有趣的计算问题。

感谢您的阅读，请回复这篇文章或发邮件给我，告诉我您的意见和建议。