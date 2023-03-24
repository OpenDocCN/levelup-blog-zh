# 基于 C++和 Zig 模板的编程

> 原文：<https://levelup.gitconnected.com/c-vs-zig-template-based-programming-d3e248885aa>

## C++和 Zig 中编译时 duck 类型的比较

![](img/8ebb3e88512ffe5a3dad8f63e9630269.png)

照片由[Fereshteh ghazisaedi](https://unsplash.com/@ghazisaeedi?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

许多语言都有某种在编译时运行代码的系统。Java 和 C#中的泛型是在编译时创建具体类型的一种方式，所以我们不必手动创建`IntArray`、`FloatArray`、`StringArray`和类似的类型。

这也避免了例如为每个可能的数字类型编写`max`函数的需要。我们不想重复写一个`maxFloat32`、`maxFloat64`、`maxInt16`、`maxInt32`以及类似的函数。我们想要一个能够处理多种数字类型的`max`函数。

在像 Julia 这样的语言中，数字类型存在于一个复杂的类型层次结构中，定义这样一个`max`函数是相当容易的。你可以这样写:

```
**function** maximum(a::Number, b::Number)
    **if** a > b
        a
    **else**
        b
    **end**
**end**
```

但是在大多数静态类型语言中，如 Java、C#、C++、Zig 等，类型层次中不存在数字，max 函数也不容易定义。但无论如何让我们试试。

## C++模板错误

看看这个简单的 C++程序演示了一个 max 函数。

```
#include <iostream>

**using** **namespace** std;

**template**<**typename** T, **typename** S>
T maximum(T a, S b) {
    T result;
    **if** (a > b)
        result = a;
    **else**
        result = b;
    **return** result;
}

**int** main (int argc, char const *argv[])
{
    int a = 12;
    int b = 3;
    int biggest = maximum("12", b);

    cout << "Max of " << a << " and "
                      << b << " is " 
                      << biggest << endl;
    return 0;
}
```

一切都运行得很好，直到我们使用`maxiumum`函数对该行进行更改，改为编写:

```
int biggest = maximum("12", b);
```

现在我们得到这个编译器错误:

```
main.cpp:19:9: error: cannot initialize a variable of type 'int' with an rvalue of type 'const char *'
    int biggest = maximum("12", b);
        ^         ~~~~~~~~~~~~~~~~
main.cpp:8:11: error: comparison between pointer and integer ('const char *' and 'int')
    if (a > b)
        ~ ^ ~
main.cpp:19:19: note: in instantiation of function template specialization 'maximum<const char *, int>' requested here
    int biggest = maximum("12", b);
                  ^
```

这是 C++中较好的模板错误信息之一，但也不容易理解。

## Zig 编译时间错误

让我们与 Zig 版本进行比较:

```
**const** std = @import("std");

**fn** maximum(a: **anytype**, b: **anytype**) @TypeOf(a) {
    **var** result: @TypeOf(a) = undefined;
    **if** (a > b) {
        result = a;
    } **else** {
        result = b;
    }

    return result;
}

**pub** **fn** main() !void {
    **const** stdout = std.io.getStdOut().writer();

    **const** a = 12;
    **const** b = 3;

    **const** biggest = maximum(a, b);

    **try** stdout.print("Max of {} and {} is {}\n", 
                     .{ a, b, biggest });
}
```

在 Zig 中，这实际上并不是您通常编写这些函数的方式，但是出于演示的目的，这很有用。

大多数人可能从未见过 Zig 代码，所以我们需要在这里解释一些看起来很奇怪的概念。

像 Go、Rust、Swift、Julia 和当今许多其他语言一样，类型信息出现在变量名之后，这与 C/C++、Java、C#和更老的语言不同。这样做的一个好处是，一行中的前几个关键字可以很快告诉你你看到的是什么类型的语句。例如，如果一行以`fn`开头，你知道它是一个函数，而如果它以`const`开头，它将是某种类型的变量。

那么以`@`符号开头的函数呢，比如`@import`和`@TypeOf`？这些是编译器内部函数。它们不是语言中的关键字或库中的函数。相反，它们是由编译器本身提供的函数。通常这些函数在编译时执行代码。

例如`@TypeOf(a)`将在编译时运行，并返回函数参数`a`的类型。

我们已经定义了我们的`maxium`函数来使用类似 C++的编译时鸭子类型，除了在 Zig 中它的工作方式有点不同。这里没有类型占位符变量`T`。相反，我们使用了`anytype`关键字，它告诉 Zig`a`和`b`可以是任何类型。编译器将在编译时确定类型。

如果我们像 C++例子中那样提供一个非数字参数，会发生什么？

```
const biggest = maximum("12", b);
```

这将不起作用，因为您不能用数字和字符串执行`a > b`。我们得到以下编译错误:

```
main.zig:5:13: error: integer value 3 cannot be coerced to type '*const [2:0]u8'
    if (a > b) {
            ^
main.zig:20:28: note: called from here
    const biggest = maximum("12", b);
                           ^
main.zig:14:21: note: called from here
pub fn main() !void {
                    ^
main.zig:5:11: note: referenced here
    if (a > b) {
```

我们可以看到错误消息彼此并没有太大的不同。

然而，Zig 给了我们一个机会来创建我们自己定制的编译错误消息。

下面是一个在一些帮助下重写`maximum`函数的例子:

```
**fn** assertInteger(**comptime** T: **type**) **void** {
    **const** is_int = **switch** (T) {
        i8, i16, i32, i64 => **true**,
        u8, u16, u32, u64 => **true**,
        comptime_int => true,
        **else** => **false**,
    };

    **if** (!is_int) {
        @compileError("Function require inputs which are integers");
    }
}

**fn** maximum(a: **anytype**, b: **anytype**) @TypeOf(a) {
    **const** A = @TypeOf(a);
    **const** B = @TypeOf(b);

    assertInteger(A);
    assertInteger(B);
    **var** result: @TypeOf(a) = **undefined**;
    **if** (a > b) {
        result = a;
    } **else** {
        result = b;
    }

    **return** result;
}
```

我定义了一个额外的函数`assertInteger`，它是一个只能在编译时运行的函数，因为我已经用关键字`comptime`标记了它的一个参数`T`以便在编译时才知道。

由于我是一个 Zig 新手，这篇文章可能写得不够优雅和好。但是它在 Zig 中展示了一个简单的能力。我们能够在运行时运行`assertInteger`函数，以确保`a`和`b`的参数都是整数值。如果不是，我们会在编译时打印一条错误消息。

Zig 给了我们许多函数，我们可以在编译时与用户交流。例如`@compileError`让我们在编译时写错误信息。当我们试图编译时，会出现以下错误消息:

```
main.zig:11:9: error: Function require inputs which are integers
        @compileError("Function require inputs which are integers");
        ^
main.zig:19:18: note: called from here
    assertInteger(A);
                 ^
main.zig:37:28: note: called from here
    const biggest = maximum(a, b);
                           ^
main.zig:31:21: note: called from here
pub fn main() !void {
                    ^
main.zig:19:18: note: referenced here
    assertInteger(A);
                 ^
main.zig:37:28: note: referenced here
    const biggest = maximum(a, b);
```

## C++ 20 概念呢？

这让我们能够在类型不匹配时向用户提供更好的错误消息，而无需添加更多的语言特性。例如，C++ 20 正在添加概念来帮助指定类型参数的契约，例如`T`。

Swift 可能是 C++ 20 中引入的 concepts 思想的较好实现之一。蒂姆·比尔斯在这里举了一个[的例子](https://medium.com/swift2go/mastering-generics-with-protocols-the-specification-pattern-5e2e303af4ca)，我将做一个简化的版本:

```
**protocol** Colored {
    var color: Color { get set }
}

**struct** SizeSpecification<T: Sized> {

    **var** size: Size

    **func** isSatisfied(item: T) -> Bool {
        **return** item.size == size
    }
}
```

Swift 中的协议类似于 Java 中的接口，但是您可以使用它们作为运行时多态性的接口，也可以约束类型参数，例如`T`。在这种情况下，我们说`isSatisfied`函数适用于某种类型的项目`T`。该类型必须实现`Colored`协议，否则它不是`T`占位符类型的有效类型。

所以像这样的东西显然是有用的。但这可能与保持语言简约的愿望相冲突。Swift 和 C++都不是极简语言。

使用编译时函数的 Zig 模板/泛型方法的好处是，它可以被广泛使用，并且可以根据具体情况进行定制。这意味着我们不用添加更多的语言特性，而是可以通过创建像`assertInteger`这样的函数来解决库的编译时鸭子类型的许多问题。

## 带有类型参数 T 的 Zig

在我们的第一个例子中，参数`a`和`b`没有约束。

例如在 C++中，我们可以声明两个参数应该是相同的类型`T`。

```
**template**<typename T>
T minimum(T a, T b) {
    T result;
    **if** (a < b)
        result = a;
    **else**
        result = b;
    **return** result;
}
```

然后，我们可以像之前调用`maximum`一样调用`minimum`函数，并让`T`直接推断或指定它:

```
int smallest = minimum<long>(a, b);
```

基本上，我们提供`T`作为`maximum`函数的特殊参数。在 Zig 案例中有趣的是，类型并没有作为任何一种特殊的参数而过时。它们与任何其他参数一样，只是在编译时必须用关键字`comptime`标记为已存在:

```
**fn** minimum(**comptime** T: **type**, a: T, b: T) T {
    assertInteger(T);

    **var** result: T = **undefined**;
    **if** (a < b) {
        result = a;
    } **else** {
        result = b;
    }

    **return** result;
}
```

因此，我们这样呼吁:

```
**const** smallest = minimum(u16, a, b);
```

这里`u16`表示 16 位无符号整数点值。Zig 使用短类型名。

至少对我来说，这种语法的一个好处是没有什么新东西需要学习。不需要学习特殊的类型参数语法。类型就像任何其他参数一样。甚至一个 none 类型的参数也可以被指定为`comptime`,例如

我可以提供一个浮点值作为参数，因为我知道我的断言不喜欢这样:

```
**const** smallest = minimum(f16, a, b);
```

这将产生以下编译错误:

```
main.zig:12:9: error: Function require inputs which are integers
        @compileError("Function require inputs which are integers");
        ^
main.zig:33:18: note: called from here
    assertInteger(T);
                 ^
main.zig:55:29: note: called from here
    const smallest = minimum(f16, a, b);
                            ^
main.zig:45:21: note: called from here
pub fn main() !void {
```

我们得到一个堆栈回溯，它告诉我们在堆栈帧中的每一点问题发生在哪里。例如，`@compileError`是由`assertInteger(T)`呼叫引起的，而`assertInteger(T)`呼叫又是由`const smallest = minimum(f16, a, b);`呼叫引起的。

## Zig CompTime 取代了预处理宏

当分析 Zig `comptime`时，重要的是不要忘乎所以，想象和精心设计复杂的系统，用于随时间演进的泛型编程。Zig 并不是要成为 Haskell、C++、Rust 或任何类似的语言。

`comptime`的存在主要是为了取代 C 预处理器。可怕的 C 预处理器宏是大多数 C 开发人员认为是一个错误。然而，这是一个难以避免的错误，因为它提供了 C 语言中不存在的灵活性和功能性。C 语言中的许多常见任务都需要预处理器。Zig 的创造者安德鲁·凯利不得不拿出一个最小的替代品。那就是`comptime`是什么。

有趣的是，一个原本被设计得很小的功能，最终却产生了更广泛的影响。据 Andrew 自己说，他无意为泛型编程创建一个系统。

因此，在研究 Zig 特性时，我们不应该以“我们需要在 Zig 中实现什么来尽可能地进行令人敬畏的通用编程”为出发点，而是应该考虑如何利用 Zig 中的最小特性来达到这个目的？