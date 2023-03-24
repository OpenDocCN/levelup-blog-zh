# Golang 类型转换摘要

> 原文：<https://levelup.gitconnected.com/golang-type-conversion-summary-dc9e36842d25>

一篇讲清楚 Golang 类型转换的文章。

![](img/d1d385cc7fe541e06e0970ad78c036a6.png)

帕特里克·帕金斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

围棋中有 4 种`type-conversions`:

*   断言。
*   强制类型转换。
*   显式类型转换。
*   隐式类型转换。

一般说的类型转换是指`assertion`，日常生活中不使用强制类型转换，显式是基本类型转换，隐式使用但不注意。

断言、强制、显式这三类在 Go 语法描述中都有说明，隐式在日常使用过程中有总结。

**断言类型转换。**

通过判断变量是否可以转换为某种类型来断言。

注意:类型断言只能发生在`interfaces`上。

我们先来看一个简单的断言表达式。

```
var s = x.(T)
```

如果`x`不是`nil`，并且`x`可以转换为类型`T`，则断言成功，返回类型`T`的变量`s`。

如果`T`不是`interface`类型，则要求`x`的类型是`T`，如果`T`是`interface`，则要求`x`实现`T`接口。

如果断言类型为`true`，表达式返回值为`T`类型的`x`，断言失败将触发`panic`。

如果断言失败，它将`panic`。Go 提供了另一种断言语法，并返回:

```
s, ok := x.(T)
```

这种方法和第一种几乎一样，只是`ok`会返回断言是否成功而不需要`panic`，`ok`表示是否成功。

在进行类型声明时，我们应该知道变量的底层类型，但情况并非总是如此。

这就是为什么类型断言表达式实际上返回第二个可选值。

使用第二个值，我们可以很容易地确定断言结构是否成功。

```
var foo interface{} = "123" 
fooStr, ok := foo.(string)if ok {
    // ...}
```

> 更多详情见官方文档:[https://golang.org/ref/spec#Type_assertions](https://go.dev/ref/spec#Type_assertions)

**类型开关。**

Go 语法中提供了另一种类型切换的断言方法。

`x`被断言为`type`类型，`type`类型的具体值为开关箱的值。

如果`x`被成功断言为`case`类型，则该案例可以被执行。此时`i := x.(type)`返回的`i`就是该类型的变量，可以直接作为 case 类型使用。

当您不确定接口的类型时，可以使用类型开关语法。

让我们看一个具体的例子:

> 更多详情见官方文档:[https://go.dev/ref/spec#Type_switches](https://go.dev/ref/spec#Type_switches)

**强制类型转换。**

通过修改变量类型强制进行类型转换

这种方法并不常见。主要用于不安全包和接口类型检测。它需要 Go 变量的知识。

```
var f float64
bits = *(*uint64)(unsafe.Pointer(&f))type ptr unsafe.Pointer
bits = *(*uint64)(ptr(&f))var p ptr = nil
```

将`float64`强制转换为`uint64`类型，float 的地址是值但类型是 float64，然后创建一个`uint64`类型变量，地址值也是 float64 的地址值，两个变量的值相同，类型不同，最后强制类型。

`Unsafe`强制是指针的底层操作。使用`C`语言的开发人员非常熟悉这样的指针类型转换。只有使用内存对齐，转换才是可靠的。

比如`int`和`uint`有符号位差，不安全转换后的值可能不一样，但是内存中存储的二进制是完全一样的。

> 关于不安全的具体细节，请参考官方文档:[https://go.dev/ref/spec#Package_unsafe](https://go.dev/ref/spec#Package_unsafe)

**接口类型检测。**

```
var _ Context = (*ContextBase)(nil)
```

`nil`的类型为`nil`，地址值为`0`，强制转换为`* ContextBase`。返回的变量类型为`*ContextBase`，地址值为`0`。

然后分配`Context=xx`。如果 xx 实现了`Context`接口就 ok 了。如果没有实现，编译时会报错，实现会在编译时检查接口是否实现。

**显式类型转换。**

显式可转换表达式`T (x)`，其中`T`为类型，`x`为可转换为类型`T`的表达式，例如:`uint(123)`。

在下列任何一种情况下，变量`x`都可以转换为类型`T`:

*   `x`可分配给类型`T`。
*   忽略结构标签的类型`x`和`T`具有相同的底层类型。
*   忽略结构标志`x`类型和`T`是未定义类型的指针类型，它们的指针基类型具有相同的基类型。
*   `x`和`T`的类型都是整型或浮点型。
*   `x`和`T`的类型都是复杂类型。
*   `x`的类型为整数或`[]byte`或`[]rune`，而`T`的类型为字符串。
*   `x`的类型为字符串`T`的类型为`[]byte`或`[]rune`。

比如下面的代码使用规则进行转换，规则实现可以参考`reflect.Value.Convert`方法逻辑。

```
int64(123)
[]byte("hello")type A int
A(0)
```

> 更多详情见官方文档:[https://go.dev/ref/spec#Conversions](https://go.dev/ref/spec#Conversions)

**隐式类型转换。**

隐式类型转换在日常使用中感觉不到，但在操作中确实会发生，下面列出了其中的两种。

*# 1。在组合之间重新声明类型。*

```
type Reader interface {
    Read(p []byte) (n int, err error)
}type ReadCloser interface {
    Reader
    Close() error
}var rc ReaderClose
r := rc
```

ReaderClose 接口结合了 Reader 接口，但是当 r=rc 赋值时发生类型转换，Go 使用系统内置函数进行类型转换。

以前遇到过像接口组合类型这样的变量赋值，然后使用`pprof`和基准测试来发现这个细节，在接口类型传输上浪费了一些性能。

*# 2。相同类型之间的赋值。*

```
type Handler func()func NewHandler() Handler {
    return func() {}
}
```

虽然 type 定义了`Handler`类型，`Handler`和`func()`是两个实际的类型，但是这两个类型并不相等，并且这两个类型在使用反射和断言时是不同的。

检验代码:

```
package mainimport (
    "fmt"
    "reflect"
)type Handler func()func a() Handler {
    return func() {}
}func main() {
    var i interface{} = main
    _, ok := i.(func())
    fmt.Println(ok) _, ok = i.(Handler)
    fmt.Println(ok)
    fmt.Println(reflect.TypeOf(main) == reflect.TypeOf((*Handler)(nil)).Elem())
}
```

结果是:

```
true
false
false
```

如果你喜欢这样的故事，想支持我，请给我鼓掌。

你的支持对我很重要，谢谢。