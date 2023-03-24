# Go 中变量使用指南

> 原文：<https://levelup.gitconnected.com/guide-to-using-variables-in-go-7288dd34e3e9>

![](img/fc586a357121e2637c737d26292e78e1.png)

照片由[小猪银行](https://unsplash.com/@piggybank?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 Go 中，*变量*被编译器显式声明和使用，例如检查函数调用的类型正确性。

变量可以像常量一样初始化，但是初始化器可以是在运行时计算的一般表达式。

# 长格式:

> **var i int**

这里 var 语句声明了一个变量列表；与函数参数列表一样，类型在最后。

一个`var`语句可以在包或函数级别。我们在上面的例子中看到了这两种情况。

# 简短形式:

> 我:= 10

这里我们声明一个名为“I”的变量，并存储 10 作为它的值。到这里通过类型推断自动猜测变量类型

# 带有初始值设定项的变量

> **var i，j int = 1，2**

var 声明可以包含初始化器，每个变量一个。如果初始值设定项存在，则可以省略该类型；变量将采用初始化器的类型。

# 多重声明

```
var (
    home string
    userID,age = 1,20
    isPresent boolean
)
```

这在同一个语句中声明了具有不同类型和值的多个变量。

在上面的例子中，userID 的第二行值是 1，年龄是 20。

# 基本类型

```
string
boolint  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptrbyte // alias for uint8rune // alias for int32
     // represents a Unicode code pointfloat32 float64complex64 complex128
```

下面的例子显示了几种类型的变量，并且变量声明可以“分解”到块中，就像导入语句一样。

```
package main

import (
 "fmt"
 "math/cmplx"
)

var (
 ToBe   bool       = false
 MaxInt uint64     = 1<<64 - 1
 z      complex128 = cmplx.Sqrt(-5 + 12i)
)

func main() {
 fmt.Printf("Type: %T Value: %v\n", ToBe, ToBe)
 fmt.Printf("Type: %T Value: %v\n", MaxInt, MaxInt)
 fmt.Printf("Type: %T Value: %v\n", z, z)
}
```

在 32 位系统上，`int`、`uint`和`uintptr`类型通常是 32 位宽，在 64 位系统上是 64 位宽。当你需要一个整数值时，你应该使用`int`,除非你有特殊的理由使用有大小的或者无符号的整数类型。

# 零值

没有显式初始值的变量被赋予零值。

零值为:

*   `0`对于数值类型，
*   `false`为布尔型，与
*   `""`(空字符串)为字符串。

# 应该使用哪种形式？

通常，当一个人不能事先知道要存储什么数据时，就使用长声明，否则，短声明一般也可以。当需要一起定义多个变量时，使用多个声明有助于提高代码的可读性。

短声明不能在函数之外使用，包括 *main* 函数，否则编译器会抛出以下错误:

*语法错误:函数体外部的非声明语句。不太好。*

# 其他包可访问的变量

这种使变量对代码中的其他包可用的方法被称为*导出*。对于要包作用域的变量，我们只能使用一个长声明或多个声明。

使一个变量可用就像大写它的第一个字母一样简单。

示例:

```
package main

func main() {
 var age int //unexported
 var Name string //exported
}
```