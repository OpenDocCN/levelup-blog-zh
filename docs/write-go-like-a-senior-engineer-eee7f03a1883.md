# 像高级工程师一样写围棋

> 原文：<https://levelup.gitconnected.com/write-go-like-a-senior-engineer-eee7f03a1883>

## 当我开始写围棋的时候，我希望我知道的是

![](img/7efc7984e7883cdb5b45d6900f06c4d8.png)

Renee French， [CC BY 3.0](https://creativecommons.org/licenses/by/3.0) ，通过维基共享

2018 年开始专业写围棋。以下是我开始时希望能告诉自己的话。

# Go 是按值传递的

Go 完全是按值传递的。这意味着每个函数接收一个你传入的值的副本。没有例外。

算是吧。

您也可以传递指针(从技术上讲，您传递的是指针的值—地址)。通过 Go 的强类型，你可以对底层指针进行类型检查。

在这个例子中，我们通过值来传递`rect`结构。该结构进入函数，所有操作都修改传入的值，但原始对象保持不变，因为它不是通过引用传递的。

```
// https://go.dev/play/p/TAiWwsOZ_n6
package main

import "fmt"

type Rectangle struct {
    Width  int
    Height int
}

func DoubleHeight(rect Rectangle) {
    rect.Height = rect.Height * 2
}

func main() {
    rect := Rectangle{
        Width:  10,
        Height: 3,
    }

    // this won't actually modify rect
    DoubleHeight(rect)

    fmt.Println("Width", rect.Width)
    fmt.Println("Height", rect.Height)
}
```

这个函数没有做我们想要它做的事情。该对象未被更新。

但是我们可以使用指向`rect`的指针作为`DoubleHeight`函数的参数，并通过引用有效地传递`rect` *。*

```
// https://go.dev/play/p/7L5QQtvzdDU
package main

import "fmt"

type Rectangle struct {
    Width  int
    Height int
}

func DoubleHeight(rect *Rectangle) {
    rect.Height = rect.Height * 2
}

func main() {
    rect := Rectangle{
        Width:  10,
        Height: 3,
    }

    // this modifies rect
    DoubleHeight(&rect)

    fmt.Println("Width", rect.Width)
    fmt.Println("Height", rect.Height)
}
```

# 使用(但不要滥用)指针

与指针相关的操作符有两个:`*`和`&`。

## `*`操作员

`*`操作符被称为“指针”操作符。类型`*Rectangle`是一个指向`Rectangle`实例的指针。声明一个类型为`*Rectangle`的参数意味着函数接受“一个指向矩形实例的指针”。

```
var rect *Rectangle
```

指针的零值是`nil`。在很多情况下超级有用！但是当你试图在一个空指针上调用函数时，这通常会导致`panic`被调用。调试起来很糟糕(稍后会详细介绍)。

## &运算符

`&`操作符(称为“地址”操作符)生成一个指向其操作数的指针。

```
rect := Rectangle{
    Width:  10,
    Height: 3,
}
pntr := &rect
```

要获得指针的基础值，使用`*`操作符。这被称为“取消指针引用”

```
// read rect through the pointer
fmt.Println(*pntr)

// set rect through the pointer
*pntr = Rectangle{
    Width: 20,
    Height: 4,
}
```

如果你想知道“我应该在这里使用指针吗？”答案很可能是“是的。”如有疑问，请使用指针。

## 零指针取消引用错误

我存在的祸根。

有时当你使用指针时，你会得到这个`panic`完全关闭你的程序:

```
panic: runtime error: invalid memory address or nil pointer dereference
```

这是因为你试图取消引用一个指针`nil`。

```
// https://go.dev/play/p/XjtkrEudRe9
package main

import "fmt"

type Rectangle struct {
    Width  int
    Height int
}

func (r *Rectangle) Area() int {
    return r.Width * r.Height
}

func main() {
    var rect *Rectangle      // default value is nil
    fmt.Println(rect.Area()) // this will panic
}
```

修复很容易。在对指针值调用方法之前，可以检查指针值。

```
// https://go.dev/play/p/VM0fdzZjiq_Y
package main

import "fmt"

type Rectangle struct {
    Width  int
    Height int
}

func (r *Rectangle) Area() int {
    return r.Width * r.Height
}

func main() {
    var rect *Rectangle

    if rect != nil {
        fmt.Println(rect.Area())
    } else {
        fmt.Println("rect is nil!")
    }
}
```

这些错误通常很难排除，它们花费了我*小时*的时间来寻找 bug。如果你决定使用指针，总是*总是*检查零值！

# 在使用接口的地方声明接口

好的。指针在围棋中非常重要。它们并不是唯一的。但是当 JS 或 Python 工程师转换时，接口真的会把他们搞垮。

Go 中的接口就像一个约定，它指定了一组方法，类型*必须*实现这些方法才能完成约定。例如，如果你有一个名为`Reader`的接口，它定义了一个`Read()`方法，那么任何实现了`Read()`方法的类型都被称为实现了`Reader`接口，并且可以在任何需要`Reader`的地方使用。

Go 中接口的一个很酷的地方是一个类型可以实现多个接口。例如，如果您有一个名为`File`的类型，它实现了`Reader`和`Writer`接口，那么您可以在需要`Reader`或`Writer`的任何地方使用`File`，或者甚至在需要实现这两个接口的任何地方使用。这使得编写灵活且可重用的代码变得容易。

Go 中接口的另一个很酷的地方是，你可以用它们来定义通用的算法或函数来处理多种类型。例如，您可以编写一个排序函数，它将一个`sort.Interface`作为输入，并且可以对实现所需方法的任何类型进行排序。

```
package sort

type Interface interface{
    Len()           int
    Less (i , j)    bool
    Swap(i , j int)
}
```

这使得编写高度灵活和可定制的代码变得容易。您可以创建一个自定义类型(如`UserList`或`PostFeed`)并定义这些方法，并将其用于标准库。

## 秘密武器

我不知道为什么 Go 不能开箱即用。但是它有很多功能不是现成的(例如错误处理)。我只能假设这是有意的设计。

问题是这样的:

你如何保证一个结构符合一个接口的所有方法？

```
type SomeInterface interface {
    Method()
}

type Implementation struct{}

func (*Implementation) Method() { fmt.Println("Hello, World!") }

var _ SomeInterface = (*Implementation)(nil) // ← this is the line
```

这最后一行确保`Implementation`满足`SomeInterface`的所有方法，如果`SomeInterface`添加了`Implementation`不满足的方法，*将无法编译*。

[我已经为这个概念创建了一个更完整的演示，你可以在 go.dev →](https://go.dev/play/p/RTbbdlBitJE) 上玩玩

# 更喜欢桌面测试，但不要过度

当你测试一个函数时，你实际上只是改变了输入和期望输出。Go 允许您使用表测试(或表驱动测试)以一种非常直接的方式来完成这项工作。

让我们从我们想要测试的代码开始。它有点乱，但它在我们的系统中运行着一个关键的功能，所以在不知道它确实如我们所愿的情况下，我们不想碰它。

```
package main

import "strings"

func ToSnakeCase(str string) string {
    // step 0: handle empty string
    if str == "" {
        return ""
    }

    // step 1: CamelCase to snake_case
    for i := 0; i < len(str); i++ {
        if str[i] >= 'A' && str[i] <= 'Z' {
            if i == 0 {
                str = strings.ToLower(string(str[i])) + str[i+1:]
            } else {
                str = str[:i] + "_" + strings.ToLower(string(str[i])) + str[i+1:]
            }
        }
    }

    // step 2: remove spaces
    str = strings.ReplaceAll(str, " ", "")

    // step 3: remove double-underscores
    str = strings.ReplaceAll(str, "__", "_")

    return str
}
```

我们可能希望测试大范围的输入，以确保得到正确的输出。不要为此编写单独的测试，而是使用表格测试来获得同样的结果，一个更清晰、更易于维护的结果。

```
 package main

import "testing"

func TestToSnakeCase(t *testing.T) {
    type testCase struct {
        description string
        input       string
        expected    string
    }

    testCases := []testCase{
        {
            description: "empty string",
            input:       "",
            expected:    "",
        },
        {
            description: "single word",
            input:       "Hello",
            expected:    "hello",
        },
        {
            description: "two words (camel case)",
            input:       "HelloWorld",
            expected:    "hello_world",
        },
        {
            description: "two words with space",
            input:       "Hello World",
            expected:    "hello_world",
        },
        {
            description: "two words with space and underscore",
            input:       "Hello_World",
            expected:    "hello_world",
        },
    }

    for _, tc := range testCases {
        t.Run(tc.description, func(t *testing.T) {
            actual := ToSnakeCase(tc.input)
            if actual != tc.expected {
                t.Errorf("expected %s, got %s", tc.expected, actual)
            }
        })
    }
}
```

这些测试用例真的很好读！

## 何时避免桌面测试

一个很好的试金石是:如果你在实际的测试调用中进行逻辑分支，你要么不应该使用表测试，要么你的函数太复杂，应该被分解。

这个例子并不太难理解(尽管它不是很好的代码)，但是它仍然是一个代码味道，可能表明一个设计不良的函数。

```
// this is BAD
for _, tc := range testCases {
    t.Run(tc.description, func(t *testing.T) {
        result, err := SomeOverlyComplexFunction(tc.input)

        if err == nil {
            if tc.expectedResult != result {
                t.Errorf("expected %v, got %v", tc.expectedResult, result)
            }
        } else {
            if !strings.Contains(err.Error(), tc.expectedErr.Error()) {
                t.Errorf("expected error to be %s, got %s", tc.expectedErr.Error(), err.Error())
            }
        }
    })
}
```

# 其他资源

有效的围棋是新围棋工程师的起点。即使在使用这门语言多年之后，它仍然是我的一个永恒的书签。

[](https://go.dev/doc/effective_go) [## 有效的 Go-Go 编程语言

### 围棋是一门新的语言。虽然它借鉴了现有语言的思想，但它有一些不寻常的属性，使它变得有效…

go.dev](https://go.dev/doc/effective_go) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰更多内容请查看[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)