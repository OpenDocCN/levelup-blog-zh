# 朱莉娅中的变量和类型

> 原文：<https://levelup.gitconnected.com/variables-and-types-in-julia-750b057c4537>

朱莉娅的变量和类型简介。

![](img/e6033961983ff95aac8c24d5e347d7cc.png)

这是 Julia 教程(关于 Julia 的一系列教程)的第 4 部分。如果你不熟悉茱莉亚的《REPL》，请浏览[第三部](https://medium.com/@adarshms/read-eval-print-loop-in-julia-24a4192b999b)以便更好地理解。

如果你不熟悉 Julia 并想开始使用它，请随意查看完整的系列—

[](https://medium.com/@adarshms/julia-tutorials-fa6b2b0e5498) [## 朱莉娅教程

### 关于朱莉娅的一系列教程。

medium.com](https://medium.com/@adarshms/julia-tutorials-fa6b2b0e5498) 

> 注意:使用 Julia 的 REPL 可以快速理解本教程中的代码

# 朱莉娅中的变量

像其他编程语言一样，变量是计算机内存中由用户命名的位置，用来保存与程序相关的数据。

**在 Julia 中创建变量**
在 Julia 中，变量名**区分大小写**并且还支持多种 unicode 字符(UTF 8 编码)。变量可以通过使用传统的赋值操作符“=”来赋值/创建。

```
test_string = "Julia variable"
pi_val = 3.14
count = 35
❄ = -12
```

在上述示例中，创建了 3 个变量，即“test_string”、“pi_val”和“count”，并为其分配了一些值(数据类型)。

**变量到变量的赋值** 一个变量中的值可以通过简单地遵循`n`等于`m`约定被赋值给另一个变量

```
m = 10
n = m      # value of m will be copied to n
```

**变量的命名约定** 虽然在命名变量方面没有太多限制，但还是推荐一些样式约定。

*   首选小写名称
*   变量名不能以数字或感叹号开头。
*   虽然不太喜欢在名称中使用“_ ”,但是可以使用“ **snake_cases** ”来提高长**函数**和**变量**名称的可读性。
*   **山茶花**优先用于命名**类型**和**模块**
*   为赋值运算符“=”提供前导和尾随空格

# 朱莉娅的类型

类型约束表达式(如变量或函数)可能采用的值。

类型可以大致分为 2 类:

*   **静态类型**——每个可执行程序表达式在执行前都被定义了一个类型
*   **动态类型** -编译器必须在代码编译时决定值的类型

Julia 的类型系统主要是动态的，并通过在需要的地方指定类型的能力得到了增强，这意味着没有必要告诉 Julia 某个特定值是什么类型，除非您想这样做。

基于 Julia 的类型层次结构，类型可以是 ***抽象*** 或 ***具体*** *。*

*   **抽象类型**——一种类型，仅仅用来作为其他类型的超类型，而不是特定对象的类型。也就是说，抽象类型可以分为子类型。一些例子是:`Number`、`String`、`Bool`、`Rational`、*等等……*
*   **具体类型**——这些是实际对象的类型。它们总是抽象类型的子类型，不允许有任何子类型。

**具体类型**又可分为两种，*和 ***复合。****

***基元类型**-**Julia 中的基元类型是具体类型，其值以位的形式表示。基本类型的一些例子有:`Int8`、`Int16`、`Int64`、`Float16`、`Float32`、`String`、*等……****

***与大多数语言不同，使用 Julia 你可以声明你自己的基本类型。***

***标准基本类型定义为:***

```
***primitive type Float64 <: AbstractFloat 64 end
primitive type Bool <: Integer 8 end
primitive type Char <: AbstractChar 32 end
primitive type Int64 <: Signed 64 end***
```

***类似地，我们可以使用以下语法定义自己的原语:***

```
***primitive type Byte 8 end    # Define the primitive Byte
Byte(val::UInt8) = reinterpret(Byte, val)
b = Byte(0x02)   # Assign a byte value to b***
```

***这里，`reinterpret`用于将 8 位无符号整数(UInt8)赋给字节。***

> *****注意**:你可能不知道`::`操作符是干什么用的，别担心，后续教程会解释的***

*****复合类型**-Julia 中的复合类型是命名字段的集合，它们可以被单独视为特定类型的单个值。复合类型可以通过使用 **struct** 关键字来声明。***

***语法:***

```
***struct Foo
    Field1::Type
    Field2::Type
end***
```

***示例:***

```
***# Define the struct
struct Greet
    x
    y::String
end # Creating object with constructor
greet1 = Greet("Hello", "World")***
```

***可以使用点符号来访问复合对象的值，***

```
***greet1.xOutput -> "Hello"***
```

***关于复合类型的一个有趣的事情是，它们在本质上是**不可变的**，也就是说，我们不能在实例化对象之后改变值。幸运的是，Julia 还提供了与不可变复合类型语法相似的*，唯一的区别是`struct`前面的`mutable`关键字的用法。****

****语法:****

```
****mutable struct Foo
    Field1::Type
    Field2::Type
end****
```

****示例:****

```
****# Define the mutable struct
mutable struct Num
    x::Float64
    y::Float64
end# Creating object with constructor
greet1 = Num(1, 5.3)# Update a value
greet1.x = 4.5****
```

****您可以在实例化后更改这些值。然而，这些值必须符合类型的定义，因为它们必须可转换为指定的类型(在我们的例子中是`Float64`)。例如，`Int64`输入是可以接受的，因为您可以很容易地将`Int64`转换成`Float64`。另一方面，一个字符串不能工作，因为你不能把它转换成`Float64`。****

****接下来:深入探究朱莉娅的类型****

****[](https://medium.com/@adarshms/dive-deep-into-julia-types-251d51f01c0) [## 深入探究朱莉娅的类型

### 了解一些内置运算符、文字和联合

medium.com](https://medium.com/@adarshms/dive-deep-into-julia-types-251d51f01c0)****