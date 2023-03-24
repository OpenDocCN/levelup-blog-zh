# 面向对象开发人员的 F#简介

> 原文：<https://levelup.gitconnected.com/a-brief-introduction-to-f-for-object-oriented-developers-e3ff71e665d9>

![](img/ee26d9b63c40c42aab40de6ae5f3b962.png)

最近我一直在学习如何用 F#写代码。没听说过的，F#是微软的/。NET 对功能优先编程语言的回答。我的动机是学习一种函数式编程语言，这种语言将使科学计算和数据分析的编码更具表现力、更简洁、更易维护，同时还能无缝地适应。我已经知道并喜爱的. NET 生态系统。F#完全符合这个要求。

# 为什么是函数式编程？

对于一个面向对象的程序员来说，函数式编程的思想一开始可能有点陌生。我们习惯于这样的应用程序，其中所有的东西都是一个对象，应用程序通过改变这些对象的状态(突变)来工作。相比之下，函数式程序由值和函数组成，应用程序通过将这些函数应用于值来产生新的值。

这听起来可能类似于面向对象风格的方法，但是有两个关键的区别:

1.  值不能被修改(据说它们是不可变的)。相反，操作创建并返回新值。
2.  函数应该是纯的，这意味着给定的输入总是产生相同的输出。这与类方法相反，在类方法中，类的某些内部状态可能会对方法的输出产生影响。

让我们在一个代码示例中来看看这一点，在这个示例中，我们希望按照一个给定的系数来提高雇员的工资:

```
// C# Object Oriented Example
public class Employee
{
 public string Name { get; set; }
 public float Salary { get; set; }
}public class SalaryRaiser
{
 public float Factor { get; set; }

 public void RaiseSalary(Employee employee)
 {
        // function not pure - depends on Factor, which isn't a parameter
  var raise = employee.Salary * Factor;

        // employee object is mutated directly
  employee.Salary += raise;
 }
}var employee = new Employee { Name = "Bob", Salary = 12345.67F };
var raiser = new SalaryRaiser { Factor = 0.05F };
raiser.RaiseSalary(employee);// F# Functional Example
type Employee = { Name: string; Salary: float }let factor = 0.05
let calculateSalary employee factor =
    // function is pure - factor is now a parameter
    let raise = employee.Salary * factor// new employee record returned
    { employee with  Salary = employee.Salary + raise }let employee = { Name = "Bob"; Salary = 12345.67 }
let updatedEmployee = calculateSalary employee factor
```

这些功能特性(不变性和纯函数)的主要好处是，它们使您的代码更易于维护和测试，因为您可以确保值不会被意外地改变(也称为副作用)，并且对于给定的输入，给定的函数将始终表现相同。

# F#中的数据类型

在 F#中，数据类型是静态的和强类型的，这意味着它们是在编译时确定的，给定值不能改变它的类型。这对于 C#程序员来说是很熟悉的。但是，有些细微的差异可能需要一些时间来适应:

*   F#有一个强大的类型推断系统，这意味着你几乎不需要显式地声明类型是什么——它是从上下文中推断出来的

```
// C#
public int AddInt(int a, int b)
{
    return a + b;
}var value = AddInt(1, 2)// F#
let addInt x y = x + ylet value = addInt 1 2
```

*   F#需要类型之间的显式转换

```
// C#
public float AddFloat(float a, float b)
{
    return a + b;
}int a = 1;
int b = 2;var value = AddFloat(a, b); // ints are implicitly converted to floats// F#
let addFloat (a:float) (b:float) = a + b // for the purpose of the example, explictly state the a and b must be floatslet value = addFloat 1 2 // will throw exception since 1 and 2 are ints// instead
let a = float 1
let b = float 2let value = addFloat a b
```

除此之外，F#包含了你在 C#和许多其他语言中习惯的所有原始数据类型。

# 收集

## 列表

F#中的列表类似于 C#中的列表，除了(因为它们是不可变的)像 Add/Remove 这样的方法不存在。相反，要执行等效操作，必须从现有列表创建新列表。

```
// 1\. Declare a list
let list = [1;2;3] // creates a list with the elements 1, 2 and 3// 2\. Declare a list using range syntax
let list = [1..5] // create a list with the element 1, 2, 3, 4, and 5// 3\. Add an element to the start of a list
let list = [1;2;3]
let list2 = 4::list // create a list [4;1;2;3]// 4\. Remove elements from the list
let list = [1;2;3]
let list2 = list.[0..1] // returns a list containing the elements between index 0 and 1\. Equivalent to removing the index 3
```

## 顺序

序列类似于列表，只是它们是延迟求值的，这意味着元素值直到需要时才加载到内存中。这对于处理非常大的序列非常有用。一个相当长的列表必须将所有元素同时存储在内存中，甚至可能会使你的电脑崩溃！

```
// large list
// WARNING: may crash your pc
let list = [1..1000000] // create a list containing 1 million elements// large sequence
let sequence = seq {1..1000000}
```

## 元组

我们之前看到的集合必须包含所有具有相同数据类型的数据(即一个 int 列表)。元组不同之处在于它包含多个值，每个值具有不同的数据类型。

```
// 1\. declare a tuple
let myTuple = (1, "hello")// 2\. destructure a tuple
let myTuple = (1, "hello")
let (num, str) = myTuple // assigns num the value 1, and str the value "hello"
```

## 记录

记录将数据分组到不同的对象中，类似于类。记录甚至可以有方法。然而，与 F#中的大多数东西一样，记录是不可变的，因此“编辑”记录需要从现有记录创建一个新记录。

```
// 1\. Declare a record type with a method
type Person = {
    Name: string; 
    Age: int;
} with member this.IsAdult = this.Age >= 18// 2\. Create a record
// Note that we don't need to tell the compiler that this is a Person record
// It can infer it from the fields we provide
let sam = { Name = "Sam"; Age = 27 }// 3\. Create a record based on an existing one
let olderSam = { sam with Age = 28 }
```

## 受歧视的工会

有区别的联合是 F#中的一个强大特性，可能很多程序员都不熟悉——我当然不熟悉！有区别的联合允许值是许多命名事例中的一个，每个事例都有潜在的不同类型和值。这大概说明了

```
// 1\. Basic discriminated union
// this declares a Status type, that can have the value Active or Inactive
type Status = Active | Inactive
let myStatus = Status.Active// 2\. A more complex example
type Shape = 
    | Rectangle of width:float * length:float // takes a tuple of two floats
    | Circle of radius:float // takes a float// both have the Shape type
let cir = Circle 5.
let rect = Rectangle(5., 6.)
```

# 模式匹配

模式匹配在 F#中非常常见，是一种基于匹配输入值控制应用程序流的方法。这可以比作使用 if..else 语句以这种方式，if 语句确实存在于 F#中，但是模式匹配更加惯用(希望你会同意，更加强大)。

```
// 1\. Pattern matching a discriminated union
type Status = Active | Inactive// return true if Active, return false if inactive
let isActive status = 
    match status with
    | Active -> true
    | Inactive -> false// 2\. Pattern matching to handle errors
type Result =
    | Error of string
    | Data of int// handle printing data or error cases to the console
let handle result = 
    match result with
    | Data data -> printfn "My result: %i" data
    | Error error -> printfn "An error ocurred: %s" error3\. Pattern matching tuple values
let matchPoint point = 
    match point with
    | (0, 0) -> printfn "This is the origin"
    | (x, 0) -> printfn "X is %i, while Y is 0" x
    | (0, y) -> printfn "Y is %i, while X is 0" y
    | _ -> printfn "Both values are non-zero" // _ is a catch all case
```

# 一些功能概念

## 固化和局部应用

一个纯函数只能有一个输入和一个输出，对于 F#中的函数也是如此。然而，我们已经看到了一些带有多个参数的函数。例如:

```
let addInt x y = x + yaddInt 5 6 // evaluates to 11
```

在幕后，F#实际上是把这个声明分成两个独立的函数，每个函数只有一个参数。大致如下的东西:

```
let addInt x  =     
    let subFunction y = x + y
    subFunctionlet intermediate = addInt 5 // evaluates to a function that adds five to its parameterintermediate 6 // evaluates to 11
```

将一个多参数函数拆分成一个单参数函数管道的过程称为 currying。这固然很好，但对我们有什么实际好处呢？有趣的结果之一是部分应用。

回到 C#中的加法函数。它有两个参数，你只能用两个参数调用它。用一个参数调用它只会导致编译器错误。

然而，如果我们在 F#中只使用一个参数调用 addInt 函数，就会发生一些有趣的事情。它返回一个函数，其中包含了我们的第一个参数！这就是所谓的部分应用，对于创建可重用的代码片段来说，这是一个非常有用的概念。

```
let addInt x y = x + ylet add5 = addInt 5 // partially apply addInt to create a new function that adds 5 to the inputadd5 6 // evaluates to 11
add5 7 //evaluates to 12
```

## 平静的

管道是一种特殊类型的运算符，允许您将参数放在函数之前。例如:

```
let addInt x y = x + y
let add5 = 5 |> addInt // pipe 5 into the addInt function
```

它的用途可能看起来不是很明显，但是它对于创建函数管道非常有用，在管道中，一个函数的输出直接输入到另一个函数的输入中。

```
[1; 2; 3] // create a list
|> List.map (fun x -> x + 1) // add 1 to each element of the list
|> List.filter (fun x -> x > 2) // only return elements that have a value greater than 2 (returns [3; 4])
```

注意，上面以`fun`为前缀的表达式只是 lambda 表达式(或匿名函数),就像你在许多其他语言中看到的一样。

## 作文

组合是将多个功能连接在一起以创建一个新功能的行为。乍一看，这似乎与管道非常相似，但它有细微的不同:管道必须以一个值开始，并立即被求值以给出输出；函数组合将多个函数连接在一起，并返回新的函数(即，在此阶段不计算任何内容)。

```
// Example comparing piping and compositionlet add5 = (+) 5
let double = (*) 2// piping
5
|> add5
|> double // evaluates to 20// composition
let add5ThenDouble = add5 >> double // compose into a new functionadd5ThenDouble 5 // evaluates to 20
```

# 结论

这是我对面向对象程序员的 F#的简要介绍。我所涉及的主题绝非详尽无遗，但希望它足以激起您对 F#和函数式编程的兴趣。

为了更多的资源 [F#为了乐趣和利益](https://fsharpforfunandprofit.com/)是一个优秀的网站，它将帮助你更深入地学习这门语言。

我发布的大部分内容都是关于全栈的。NET 和 Vue web 开发(很快可能会有更多的 F#内容！).为了确保你不会错过任何帖子，请关注这个博客并[订阅我的简讯](https://samwalpole.com)。如果你觉得这篇文章有帮助，请喜欢它并分享它。你也可以在[推特](https://twitter.com/dr_sam_walpole)上找到我。

*原载于*[*https://samwalpole.com*](https://samwalpole.com/a-brief-introduction-to-f-for-object-oriented-developers)*。*