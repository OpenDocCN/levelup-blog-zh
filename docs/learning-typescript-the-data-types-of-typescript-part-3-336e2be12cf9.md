# 学习类型脚本:类型脚本的数据类型(第 3 部分)

> 原文：<https://levelup.gitconnected.com/learning-typescript-the-data-types-of-typescript-part-3-336e2be12cf9>

![](img/7b3101f90476e1764e3e3497bc0cc193.png)

克里斯托弗·伯恩斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

这是关于 TypeScript 编程语言提供的数据类型的三篇系列文章中的第三篇。零件 1 和 2 在这里是[在这里是](/learning-typescript-the-data-types-of-typescript-part-1-fe49a966ccc7)在这里是[在这里是](/learning-typescript-the-typescript-data-types-part-2-4f38b7dfd5bc)。在本文中，我将介绍枚举以及值`undefined`、`null`、`never`和`void`，它们可以表现为数据类型。

# 列举

在大多数编程语言中，枚举是为整数值分配标识符的一种方式，使整数成为常数。下面是一个 C++的例子，JavaScript 程序员应该可以理解:

```
enum Day {MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY};
```

下面是 TypeScript 中的等效内容:

```
enum Day {MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY}
```

除了 C++中必需的分号之外，这两条语句没有什么不同。

花括号中命名的每个标识符现在都映射到一个整数，从 0 开始。您也可以通过声明如下的枚举来使此映射显式化:

```
enum Day {MONDAY=0, TUESDAY=1, WEDNESDAY=2, THURSDAY=3,
          FRIDAY
```

如果不想从 0 开始，可以从任何数字开始，让系统完成编号，如下例所示:

```
enum Day {MONDAY=1, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY}
```

标识符`TUESDAY`现在被赋值为 2，`WEDNESDAY`被赋值为 3，依此类推。

一旦你声明了一个枚举，你可以声明一个枚举类型的变量。例如:

```
let workday = Day.MONDAY
```

您也可以创建枚举数组:

```
enum Day {MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY,
          SATURDAY, SUNDAY}
let workDays :Day = []
workDays.push(Day.MONDAY)
workDays.push(Day.TUESDAY)
workDays.push(Day.WEDNESDAY)
workDays.push(Day.THURSDAY)
workDays.push(Day.FRIDAY)
```

正如我提到的，枚举变量存储一个整数值。这个例子证明了这个事实:

```
enum Day {MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY,
          SATURDAY, SUNDAY}
let workDays :Day = []
workDays.push(Day.MONDAY)
workDays.push(Day.TUESDAY)
workDays.push(Day.WEDNESDAY)
workDays.push(Day.THURSDAY)
workDays.push(Day.FRIDAY)
for (let aDay of wordDays) {
  console.log(aDay)
}
```

该程序显示:

```
0
1
2
3
4
```

当必须做出可以归结为整数结果的选择时，枚举是有用的。我最喜欢的例子是将枚举与 switch 语句结合使用，如下面的代码片段所示:

```
enum Day {MONDAY=1, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY,
          SATURDAY, SUNDAY}
let dayOfWeek = Day.WEDNESDAY
switch(dayOfWeek) {
  case Day.MONDAY:
    console.log("Meeting day")
    break
  case Day.TUESDAY
    console.log("Work in cubicle day")
    break
  case Day.WEDNESDAY
    console.log("In the field day")
    break
  case Day.THURSDAY
    console.log("Work in cubicle day")
    break
  case Day.FRIDAY
    console.log("Half day in office")
    break
  default:
    console.log("Don't know what day it is")
}
```

TypeScript 枚举不必映射到整数值。您也可以将它们映射到字符串。这里有一个例子:

```
enum people {CYNTHIA = "Cynthia", DANNY = "Danny", MIKE = "Mike"}
let c = people.CYNTHIA, d = people.DANNY, m = people.MIKE
let numbers = new Map()
numbers.set(c, "2333")
numbers.set(d, "2334")
numbers.set(m, "2372")
for (let k of numbers.keys()) {
  console.log(k,numbers.get(k))
}
```

在此示例中，枚举映射的是字符串而不是整数，因此当显示输出时，表示每个姓名的字符串会与该人的电话分机一起显示:

```
"Cynthia", "2333"
"Danny", "2334"
"Mike", "2372"
```

程序中从来不需要枚举，但它通常可以使程序更容易阅读和推理，特别是当枚举常数被用于整数值时。

# null、undefined、void 和 never 类型

为了结束我对 TypeScript 数据类型的讨论，我将介绍四种类型，它们通常不在程序中进行注释，但可以由 TypeScript 自动赋值。

其中第一个是`null`。就变量而言，`null`类型的含义是变量缺少一个值。例如，如果你试图给一个字符串变量分配一个外部文件的文本，但是程序在读完文件之前就停止了，这个变量就会有一个`null`类型。很难证明一个变量最终会被类型化为`null`，所以你只能相信我的话。

这个家族的下一个类型是`undefined`。许多程序员互换使用`undefined`和`null`，但它们并不意味着同一件事。`undefined`类型是为没有类型注释但没有赋值的变量保留的。这很容易在程序中演示:

```
let x
console.log(typeof x) // displays “undefined”
```

`x`的类型一旦赋值就会改变。

`void`类型用于不返回值的函数。您可以使用此数据类型专门注释不返回值的函数，如下例所示:

```
function greet(name) :void {
  console.log("Hello, " + name + "!");
}let myName = "Cynthia"
greet(myName)
```

该组的最后一个数据类型是`never`。`never`类型被称为底部类型，这意味着它可以被分配给任何东西，可以在任何地方安全地使用。`never`类型的一个例子是从不返回值的函数，比如被定义为运行无限循环的函数:

```
function infinite() {
  while(true) {
    // do anything
  }
}
```

这就结束了我对 TypeScript 数据类型的讨论。在我的下一篇文章中，我将开始讨论如何在 TypeScript 中使用函数。

感谢您的阅读，请回复这篇文章或发邮件给我，告诉我您的意见和建议。