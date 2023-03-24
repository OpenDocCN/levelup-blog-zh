# 如何处理围棋中的错误

> 原文：<https://levelup.gitconnected.com/how-to-handle-errors-in-go-df459a907c2f>

## 权威指南

![](img/f978ff3c276c492e1704f99d7a3a3cd0.png)

Photo by [傅甬 华](https://unsplash.com/@hhh13?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Go 中的错误处理不同于其他编程语言，例如 Java 或 Python。Go 内置错误不包含堆栈跟踪，也不支持常规的`try` / `catch`方法来处理它们。相反，Go 中的错误只是函数返回的值。

罗布·派克关于“错误”的一篇非常好的文章:[https://go.dev/blog/errors-are-values](https://go.dev/blog/errors-are-values)

处理错误的方式与处理任何其他数据类型的方式非常相似，这导致了一个惊人的轻量级和简单的设计。

在这里，我将演示在 Go 中处理错误的基础知识，以及一些简单的策略，你可以在你的代码中遵循这些策略来使你的程序健壮并易于调试。

Go 中的错误类型被实现为如下的接口，这意味着`error`是实现以字符串形式返回错误消息的`Error()`方法的任何东西。

```
type error interface {
    Error() string
}
```

# 如何构建错误

构造错误有几种方法，其中一种是动态创建，可以使用 Go 内置的`errors`或`fmt`包来构造。例如，下面的函数使用`errors`包返回一个新的错误和一条静态错误消息:

```
package main
```

```
import "errors"func SayHello() error {
    return errors.New("I am unable to speak")
}
```

`fmt` package 也可以用来给错误添加动态数据，比如一个`int`、`string`或另一个`error`。例如:

```
package main
```

```
import "fmt"func Divide(a, b int) (int, error) {
    if b == 0 {
        return 0, fmt.Errorf("can't divide '%d' by zero", a)
    }
    return a / b, nil
}
```

在上面的例子中，最后一行返回一个错误`nil`,这意味着我们正在返回错误的默认值或“零”值。这很重要，因为检查`if err != nil`是确定是否遇到错误的惯用方法

当我们返回一个错误时，函数返回的其他参数通常作为它们的默认“零”值返回。类似地，对于没有错误的情况，`err`返回为`nil`(零值)

错误消息的一般概念通常用小写字母书写，并且不以标点符号结尾。

# 预期误差

这是通过定义预期错误，以便可以在调用代码中显式检查它们，如果遇到特定类型的预期错误，这对于执行另一组指令是有用的。

# 哨兵误差

根据前面的示例`Divide`，功能错误信号可以通过预定义“标记”错误来改善。调用函数可以使用`errors.Is`明确检查这个错误

```
package main
```

```
import (
    "errors"
    "fmt"
)var ErrDivideByZero = errors.New("divide by zero error")func Divide(a, b int) (int, error) {
    if b == 0 {
        return 0, ErrDivideByZero
    }
    return a / b, nil
}func main() {
    a := 50
    b := 0
    res, err := Divide(a, b)
    if err != nil {
        switch {
        case errors.Is(err, ErrDivideByZero):
            fmt.Println("divide by zero error")
        default:
            fmt.Printf("unexpected error: %s\n", err)
        }
        return
    } fmt.Printf("%d / %d = %d\n", a, b, res)
}
```

## 自定义错误类型

大多数情况下，上述处理错误的方法已经足够，但是，有时您可能希望错误携带额外的数据字段，或者在记录错误时，错误消息应该具有动态值。

这可以在 Go 中通过实现一个定制的错误类型来完成。

下面介绍了一个新的类型`DivisionError`，它实现了我们之前看到的`Error` `interface` ，现在它可以利用`errors.As`来检查标准误差，并将其转换为一个更具体的误差，我们的例子是`DivisionError`。

```
package main
```

```
import (
    "errors"
    "fmt"
)type DivisionError struct {
    A int
    B int
    Msg  string
}func (e *DivisionError) Error() string { 
    return e.Msg
}func Divide(a, b int) (int, error) {
    if b == 0 {
        return 0, &DivisionError{
            Msg: fmt.Sprintf("cannot divide '%d' by zero", a),
            A: a, B: b,
        }
    }
    return a / b, nil
}func main() {
    a := 10
    b := 0
    res, err := Divide(a, b)
    if err != nil {
        var divErr *DivisionError
        switch {
        case errors.As(err, &divErr):
            fmt.Printf("%d / %d is not mathematically valid: %s\n",
              divErr.A, divErr.B, divErr.Error())
        default:
            fmt.Printf("unexpected error: %s\n", err)
        }
        return
    } fmt.Printf("%d / %d = %d\n", a, b, res)
}
```

# 包装错误

到目前为止，这些错误都是通过一个函数调用创建、返回或处理的。换句话说，使错误“冒泡”的函数堆栈只有一层深度。

通常会涉及更多的函数——从产生错误的函数，到最终处理错误的函数，以及中间的任何数量的附加函数。

有像`errors.Wrap`和`errors.Unwrap`这样的函数，它们在为错误提供额外的上下文以及检查特定的错误类型时非常有用，不管错误已经被包装了多少次。

# 如何包装一个错误？

下面的代码片段展示了如何使用带有动词`%w`的`fmt.Errorf`来“包装”错误，因为它们“冒泡”通过其他函数调用，这些函数调用添加了所需的上下文，这样就可以推断出调用堆栈中哪个先前的调用失败了

```
package main
```

```
import (
    "errors"
    "fmt"
    "hey.com/fake/fruits/db"
)func FetchFruit(fruitName string) (*db.Fruit, error) {
 f, err := db.Find(fruitName)
 if err != nil {
  return nil, fmt.Errorf("FetchFruit: failed executing db query: %w", err)
 }
 return f, nil
}func SetFruitAge(f *db.Fruit, age int) error {
 if err := db.SetAge(f, age); err != nil {
  return fmt.Errorf("SetFruitAge: failed executing db update: %w", err)
 }
}func FindAndSetFruitAge(fruitName string, age int) error {
 var fruit *Fruit
 var err error
 fruit, err = FetchFruit(fruitName)
 if err != nil {
  return fmt.Errorf("FindAndSetFruitAge: %w", err)
 }
 if err = SetFruitAge(fruit, age); err != nil {
  return fmt.Errorf("FindAndSetFruitAge: %w", err)
 }
 return nil
}func main() {
 if err := FindAndSetFruitAge("Banana", 15); err != nil {
  fmt.Println("failed finding or updating fruit: %s", err)
  return
 }
 fmt.Println("successfully updated fruits's age")
}
```

如果上面的程序遇到错误，日志应该打印以下内容:

```
>_failed finding or updating fruit: FindAndSetFruitAge: SetFruitAge: failed executing db update: malformed request
```

现在错误信息包含了我们可以看到问题起源于`db.SetFruitAge`功能的信息。如果失败是在调用执行链中的几个函数之后，这将有助于缩小错误范围

错误包装可以以类似于堆栈跟踪的方式为错误提供额外的上下文。包装还保存了原始错误，这意味着`errors.Is`和`errors.As`继续工作，而不管错误已经包装了多少次。我们也可以使用`errors.Unwrap`它来返回链中的前一个错误。

# 结论

总而言之，

*   Go 中的错误是数值[https://go.dev/blog/errors-are-values](https://go.dev/blog/errors-are-values)
*   错误有助于排除程序失败的根本原因
*   包装错误提供了跟踪一系列函数调用的上下文

附加阅读

*   [https://go.dev/blog/errors-are-values](https://go.dev/blog/errors-are-values)
*   https://go.dev/blog/error-handling-and-go
*   【https://gobyexample.com/errors 