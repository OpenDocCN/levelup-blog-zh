# Go 中的匿名函数和闭包

> 原文：<https://levelup.gitconnected.com/anonymous-functions-and-closures-in-go-e53ccccad352>

![](img/d1758f5d84d32073b2c52435a9d42642.png)

在围棋中，函数是一等公民。因此，Go 支持将函数作为参数传递，从其他函数返回函数，以及将函数赋给变量。在下面的教程中，我们将看看匿名函数和闭包。

## 匿名函数

一个[匿名函数](https://en.wikipedia.org/wiki/Anonymous_function)是一个不依赖于标识符的函数定义。例如，我们可以定义一个匿名函数，并立即调用它，如下所示:

```
func() {
    fmt.Println("Hello!")
}()  // Prints "Hello!"
```

此外，我们可以给一个变量分配一个匿名函数，然后使用该变量的标识符调用该函数。

```
var foo func() = func() {
    fmt.Println("Hello!")
}
foo()  // Prints "Hello!"
```

为了简洁起见，我们应该允许从变量被赋给的类型来推断变量的类型。

```
var foo = func() { ...
```

事实上，这允许我们在运行时给一个变量分配不同的函数。例如，我们可能希望根据用户输入给函数分配不同的值。

```
var foo func()
​
var input int
fmt.Scan(&input) // 2
​
switch input {
case 1:
    foo = func() {
        fmt.Println("Hello!")
    }
case 2:
    foo = func() {
        fmt.Println("Goodbye!")
    }
default:
    foo = func() {
        fmt.Println("???")
    }
}
​
foo() // Prints "Goodbye!"
```

注意，用标识符声明的函数也可以赋给相同类型的变量。

```
func foo() {
    fmt.Println("Hello!")
}
​
func main() {
​
    var bar func()
    bar = foo
    bar() // Prints "Hello!"
}
```

## 关闭

一个[闭包](https://en.wikipedia.org/wiki/Closure_(computer_programming))是一个匿名函数，它引用了在其自身声明之外声明的变量。

```
i := 0
foo := func() int {
    i += 10
    return i
}
​
fmt.Println(foo())  // Prints 10
fmt.Println(foo())  // Prints 20
```

闭包也可以由函数返回。这里返回的函数被称为“关闭”变量`i`。

```
func foo() func(int) int {
    i := 0
    return func(j int) int {
        i += j
        return i
    }
}
​
func main() {
    bar := foo()
​
    fmt.Println(bar(10)) // Prints 10
    fmt.Println(bar(10)) // Prints 20
}
```

## 额外资源

*   [在 Go 中使用闭包的 5 种有用方法](https://www.calhoun.io/5-useful-ways-to-use-closures-in-go/)

[](https://gitconnected.com/learn/golang) [## 学习围棋-最佳围棋教程(2019) | gitconnected

### 22 大围棋教程-免费学习围棋。课程由开发者提交和投票，使您能够找到…

gitconnected.com](https://gitconnected.com/learn/golang)