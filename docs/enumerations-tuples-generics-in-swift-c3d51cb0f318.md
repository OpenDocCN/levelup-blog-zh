# Swift 中的枚举、元组和泛型

> 原文：<https://levelup.gitconnected.com/enumerations-tuples-generics-in-swift-c3d51cb0f318>

浅谈如何在 Swift 编程语言中使用枚举、泛型和元组。

![](img/beb16c50b78d5e4a2807c3aac1569624.png)

照片由[负空间](https://www.pexels.com/@negativespace)通过[像素](https://www.pexels.com/)拍摄

# 概观

像其他编程语言一样，Swift 也有一些特殊的特性，如枚举、元组和泛型。根据不同的条件，它们有不同的用例。在本教程中，我们将学习

Swift 中使用**枚举**、**元组、泛型**的内容、时间、方式？所以让我们开始吧

> *本教程使用* ***Swift 5 和 Xcode 11.4.1 编写。***

# 1.列举

根据 swift 文档"*，枚举为一组相关值定义了一个通用类型，使您能够在代码中以类型安全的方式使用这些值*。

*   **创建枚举:**您可以在 swift 中像这样声明一个枚举

```
enum TimePeriod{
case morning
    case noon
    case afternoon
    case night
}
```

其中，该枚举的名称是“TimePeriod ”,它有四种情况，即“上午”、“中午”、“下午”和“晚上”。

我们也可以像这样在一行中声明案例

```
enum TimePeriod{
    case morning, noon, afternoon, night
}
```

*   **enum 类型:**当我们定义 enum 本身时，在 swift 中定义一个新类型。我们可以这样定义一个变量

```
var presentTime = TimePeriod.morning
print(presentTime)   // morning
```

“当前时间”变量是“时间周期”枚举类型，其值是“早晨”

一旦定义了“presentTime”是“TimePeriod”类型，我们就可以使用“.”来设置枚举的其他值操作

```
presentTime = .noon
presentTime = .afternoon
presentTime = .night
```

*   **匹配枚举值:** Swift switch 语句是匹配枚举值的简单直接的解决方案。

```
switch presentTime {case .morning:
    print("Foggy morning")
case .noon:
    print("Sunny noon")
case .afternoon:
    print("Cloudy afternon")
case .night:
    print("Rainy night")}
```

*   **迭代枚举事例:**要迭代枚举事例，我们必须将枚举声明为 **"CaseIterable"** 。在我们的例子中，我们可以像这样声明我们的“TimePeriod”枚举。

```
enum TimePeriod: CaseIterable{
    case morning
    case noon
    case afternoon
    case night
}
```

现在我们可以得到所有的情况，迭代如下。

```
let numberOfChoices = TimePeriod.allCases.count
print("\(numberOfChoices) timeperiod available")for timeperiod in TimePeriod.allCases{
    print(timeperiod)
}
```

*   **原始值:**我们可以为每个枚举案例提供值。这些值被称为原始值。它可以隐式和显式地提供。在此之前，我们必须在枚举名称后提供原始值的类型

```
enum Institute: Int{case school
case colleague
case university}
```

`enum Institute`对于每种情况都有一个整数类型的原始值。它可以是 swift 中的任何类型。在这种情况下，学校的值为 0，同事为 1，大学为 2。

每个案例的原始值都可以这样检查

```
print(Institute.school.rawValue) //0
print(Institute.colleague.rawValue) //1
print(Institute.university.rawValue) //2
```

如果我们愿意，我们还可以为每种情况提供我们想要的任何显式原始值。

```
enum Institute: Int{case school = 10
case colleague = 12
case university = 14}
```

我们还可以明确地为第一种情况提供原始值。在这种情况下，swift 将隐式提供其他情况的原始值

```
enum Institute: Int{case school = 4
case colleague
case university}
```

这里学校的原始值被明确定义为 4。Swift 将隐式地为`colleague`和`university`提供原始值，分别为 5 和 6。

我们可以使用原始值从 enum 中搜索案例。如果存在任何具有相应原始值的案例，swift 将返回该案例名称，否则将返回 nil。

```
var instituteType = Institute(rawValue: 9)if let value = instituteType2 {print(value) // nil because we haven't any case with rawvalue 9}
```

最后，我们将看到一个案例的字符串原始值的例子

```
enum MoveDirection : String {case forward = "you moved forward"
    case back = "you moved backwards"
    case left 
    case right = "you moved to the right"}
```

对于字符串原始值类型 enum，如果我们未提供任何案例的原始值，swift 将隐式提供案例名称作为原始值。

```
print(MoveDirection.forward.rawValue) // you moved forward
print(MoveDirection.back.rawValue) // you moved backwards
print(MoveDirection.left.rawValue) // left
print(MoveDirection.right.rawValue) // you moved to the right
```

*   **将值与枚举用例相关联:**有时我们也必须设置将值与枚举用例相关联。在这种情况下，当我们将一个变量声明为枚举类型时，我们还必须为这些元组赋值。

```
//enum area with associate values with casesenum Area{
    case square(Double,Double)
    case cube(Double, Double, Double)
}//define a variable "sampleArea" with assign it a value Area.square with associated tuple value (10,15)var sampleArea = Area.square(10, 15)
```

在这里，我们还可以使用 swift 语句匹配枚举值。这一次，我们有一些与每个枚举案例相关联值。这些值可以很容易地提取为变量或常量，以便在 switch 语句中使用。

```
switch sampleArea {case .square(let height, let width):
    print("Square of: \(height), \(width).")
case .cube(let height, let width, let length):
    print("Square of: \(height), \(width), \(length)")}
```

如果所有相关的值都是常量或变量，那么我们可以简单地将 var 或 let 放在案例名称之前

```
switch sampleArea {case let .square(height, width):
    print("Square of: \(height), \(width).")
case let .cuve(height, width, length):
    print("Square of: \(height), \(width), \(length)")}
```

# 2.元组:

Tuple 本身是 swift 中的一种类型，它可以有多种类型的逗号分隔值，这些值用括号括起来。它通常使用来存储多种类型的数据，并将它们作为函数调用返回。

*   **创建一个元组:**在 swift 中声明一个元组非常简单，如下所示

```
var personInfo = ("John", 24)
```

这里的“personInfo”是一个保存字符串和整数值的元组类型变量。元组的这些值可以通过“.”来访问接线员。第一个元素为 0，第二个元素为 1，依此类推。我们也可以在声明后为每个元素赋值。

```
var name = personInfo.0 // John
var age = personInfo.1 // 24personInfo.0 = "John Doe"
personInfo.1 = "30"
```

*   **元组的命名元素:**我们也可以通过为元组中的每个元素显式提供名称来声明元组类型变量。

```
var person = (firstName: "John", lastName: "Smith")
var firstName = person2.firstName2 // John
var lastName = person2.lastName2 // Smith
```

我们还可以提供名称，并以这种方式将它们赋给变量

```
let httpSuccess = (200, "Data Found")
let (statusCode, statusMessage) = httpSuccessprint("Status code \(statusCode)") // Status code 200print("Status message \(statusMessage)") // Status message Data Found"
```

如果我们不想要元组的值，我们也可以在分解时避免赋值

```
let httpSuccess = (200, "Data Found")
let (statusCode, _) = httpSuccessprint("Status code \(statusCode)") // Status code 200print("Status message \(statusMessage)") // Status message Data Found"
```

*   **元组是值类型:**重要的一点是元组是值类型。当 use 将一个分配给另一个时，它实际上创建了一个副本。

```
var area = (w: 0, h: 0)
var subArea = areasubArea.w = 3
subArea.h = 5print(area) // (0, 0)
print(subArea) // (3, 5)
```

*   **多个赋值:**

元组有多个赋值

```
var (x, y, z) = (1, 2, 3)
```

*   **互换两个值:**

使用元组可以很容易地交换两个值

```
var x= 5 
var y= 4 
(y, x) = (x, y)
```

*   **另一个元组中的元组:**

一个元组在另一个元组中可以这样描述

```
let person: (String, (string, Int)) = ("John", ("Dhaka", 30))
print(person.0) // John
print(person.1.0) // Dhaka
print(person.1.1) // 30
```

# 3.仿制药:

Generics 是 swift 最强大的功能之一。通过编写与类型无关的代码，它有助于编写灵活的、可重用的函数。我们将在这里看到泛型的一些应用

*   **泛型介绍:**假设我们将编写一个函数，它接受两个数字，将它们相加并返回。我们将编写一个函数，并像这样使用它

```
func addIntNumbers(num1: Int, num2: Int) -> Int{
    return num1 + num2
}let result = addIntNumbers(num1: 10, num2: 20) // 30
```

现在，如果我们想把两个双数相加，那么我们必须写另一个函数来把它们相加

```
func addDoubleNumbers(num1: Double, num2: Double) -> Double{
    return num1 + num2
}let result2 = addDoubleNumbers(num1: 13.75, num2: 20) // 33.75
```

对于单个操作，我们要写两个不同的函数，仅仅是为了类型的不同。除此之外，一切都是一样的。

泛型可以与任何类型一起工作，可以将我们从这种情况中解救出来，编写一个可重用的函数。我们可以通过定义协议类型来定义功能

```
func addNumbers<T: Numeric>(a: T, b: T) -> T
{
    return a + b
}
```

这里的“T”是数值类型，所以我们可以在调用这个函数时将任何数值类型作为参数传递

```
//Int type argument
let value = addNumbers(a: 10, b: 20) // 30//Double type argument
let value2 = addNumbers(a: 10.5, b: 20.4) // 30.9
```

这是使用泛型的最基本和最主要的原因，但是我们当然可以做更多的事情。

祝贺🎉 🎉 🎉我想现在您对 Swift 中的 enum、tuples 和 generics 有所了解，知道如何以及在哪里使用它们。我建议您查看 Apple 和 swift 的官方文档，以便进一步参考。

**如果你觉得这篇文章有用，请分享并鼓掌**👏👏👏
在[媒体](https://medium.com/@arifulislam14)上查看我的其他文章，在 [LinkedIn](https://www.linkedin.com/in/arifparvez14/) 上联系我。

感谢您阅读&快乐编码🙂