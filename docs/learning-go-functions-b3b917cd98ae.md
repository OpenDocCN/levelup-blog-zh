# 学习围棋:功能

> 原文：<https://levelup.gitconnected.com/learning-go-functions-b3b917cd98ae>

![](img/6fc6e405e56132d16edd0580214a4d08.png)

[寺鹂](https://unsplash.com/@templecerulean?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在本文中，我将演示如何在 Go 中编写和使用函数。我将讨论返回值函数和仅仅为了它们的效果而使用的函数(在一些编程语言中称为 void 函数)。

# 函数定义和函数调用语法模板

与您可能熟悉的大多数编程语言相比，Go 具有不同的语法模板来定义函数。以下是模板:

*func 函数名(参数列表)(结果列表){
函数体
}*

所有的函数定义都以关键字`func`开始。接下来是函数名和参数列表。参数按名称列出，然后用逗号分隔数据类型和多个参数。

参数列表之后是结果列表。结果列表当然会包括函数的返回数据类型，但它也可以包括函数返回的结果类型的名称。如果你留心的话，你会注意到我说的是“返回类型”是的，Go 函数可以返回多个值。我将在本文后面演示这是如何工作的。

如果一个函数定义没有结果列表，那么这个函数将被用于它的效果(就像一个 void 函数或者一个子程序)。

函数调用语法模板更简单:

*func-name(参数列表)*

函数调用可以在任何需要表达式的地方进行，函数调用最重要的规则是参数列表必须与形参列表匹配。

# Go 函数参数是按值传递的参数

默认情况下，传递给 Go 函数的参数是按值传递的参数。这意味着编译器在函数体中使用参数时会复制参数，这样函数就不能对参数进行永久更改。

您可以通过使用带有参数的引用操作符(`&`)将函数的参数转换为按引用传递的参数。我们将在本文后面讨论按值传递和按引用传递参数。

# Go 函数示例

让我们从一个类函数的例子开始。写一个函数，把摄氏温度转换成华氏温度。在循环中使用函数，将 20 摄氏度的温度从-10 摄氏度转换为+10 摄氏度。

下面是函数定义和使用该函数的程序:

```
func ctof(temp float64) float64 {
  return (9.0/5.0) * temp + 32.0
}func main() {
  for c:=-10; c<=10; c++ {
  fmt.Printf("%d Celsius is %.2f Fahrenheit.\n", c,
             ctof(float64(c)))
  }
}
```

这是另一个有两个参数的例子:

```
func maximum(val1 int, val2 int) int {
  if val1 > val2 {
    return val1
  } else {
    return val2
  }
}func main() {
  var num1, num2 int
  fmt.Print("Enter first number: ")
  fmt.Scan(&num1)
  fmt.Print("Enter second number: ")
  fmt.Scan(&num2)
  larger := maximum(num1, num2)
  fmt.Printf("%d is larger of the values %d and %d.\n", larger,
             num1, num2)
}
```

# 不返回值的 Go 函数

下面是一个仅用于其效果的函数示例:

```
func greeting(name string) {
  fmt.Printf("Hello, %s!\n", name)
}func main() {
  var name string
  fmt.Print("What's your name? ")
  fmt.Scan(&name)
  greeting(name)
}
```

不返回值的函数的一个经典例子是在 C++等语言中为交换变量而编写的函数。在 C++中，这个函数必须用指针编写，这样 swap 函数才能永久地交换它的参数值。

下面是这个交换函数在 Go 中的样子(以及一个演示它如何工作的程序):

```
func swap(val1 *int, val2 *int) {
  temp := *val1
  *val1 = *val2
  *val2 = temp
}func main() {
  num1 := 1
  num2 := 2
  fmt.Printf("num1: %d, num2: %d\n", num1, num2)
  swap(&num1, &num2)
  fmt.Printf("num1: %d, num2: %d\n", num1, num2)
}
```

在这个例子中，我们使用指针作为两个函数参数，然后使用 address-of 操作符(`&`)作为函数的参数。我将在另一篇文章中讨论 Go 指针。

# 从函数中返回多个值

上面的`swap`函数示例要求我们使用指针来修改函数参数。这是你在不允许函数返回多个值的传统语言中要做的事情，但在 Go 中却不是这样。

正如我在函数定义模板一节中提到的，一个函数可以返回多个值，而不是一个。为了演示这是如何工作的，下面是重新编写的交换函数，它返回传递给它的两个变量值:

```
func swap(val1, val2 int) (int, int) {
  return val2, val1
}func main() {
  num1 := 1
  num2 := 2
  fmt.Printf("num1: %d, num2: %d\n", num1, num2)
  num1, num2 = swap(num1, num2)
  fmt.Printf("num1: %d, num2: %d\n", num1, num2)
}
```

我们已经在 Go 中看到了返回多个值的函数。我在几个地方使用的`range for`循环实际上是一个函数，其中调用`range`函数时使用一个数组或片作为参数，该函数返回数据结构中的元素和存储该元素的索引:

```
func main() {
  numbers := []int{1,2,3,4,5}
  for index, number := range numbers {
    fmt.Println(index,":",number)
  }
}
```

允许一个函数有多个返回值为函数提供了很多新的功能，其重要性不言而喻。

# 围棋中的递归

Go 函数可以是递归的；换句话说，Go 函数可以从函数体内调用自己。这里我没有空间来教你递归，所以我只演示一个典型的递归函数来计算一个数的阶乘。

下面是函数定义和一个测试程序:

```
func factorial(number int) int {
  if number == 1 {
    return number
  } else {
    return number * factorial(number-1)
  }
}func main() {
  var num int
  fmt.Print("Enter number: ")
  fmt.Scan(&num)
  fmt.Printf("%d factorial is %d.\n", num, factorial(num))
}
```

递归是所有现代编程语言都必须实现的一项必要技术，因为你需要递归来处理递归数据结构。

# 可变函数

可变函数是一个可以接受未知数量参数的函数。变量函数的一个明显例子是`sum`函数，它可以对传递给函数的任意数量的数值参数求和。

内置变量函数的一个例子是`Printf`函数。这个函数可以接受任意数量的参数，因为您可以提供一个文本字符串，其中嵌入任意数量的变量。

可变函数的函数定义语法模板如下所示:

*func 函数名(参数…数据类型)(结果列表){
体
}*

省略号表示可以使用不同数量的参数，它们的数据类型列在参数列表的末尾。

下面是如何定义一个变量`sum`函数，以及使用该函数的程序:

```
func sum(numbers...int) int {
  total := 0
  for _, number := range numbers {
    total += number
  }
  return total
}func main() {
  total := sum(1,2,3,4,5)
  fmt.Print(total)
}
```

一个变量函数也可以包含不变的参数。在这种情况下，可变参数必须是参数列表中的最后一个。

下面的程序演示了一个简单的函数，它向不同数量的人显示问候语:

```
func greet(greeting string, names...string) string {
  g := greeting + " "
  for index, name := range names {
    if (index == len(names)-1) {
      g += "and " + name
    } else {
      g += name + ", "
    }
  }
  return g + "!"
}func main() {
  fmt.Println(greet("Hello", "Mike", "Terri", "Meredith",
              "Allison", "Mason"))
  fmt.Println(greet("Goodbye", "Cynthia", "Danny", "Jonathan"))
}
```

# 一级函数

Go 中的函数是第一类，这意味着它们可以用作函数参数、函数的返回值，以及在表达式中赋给变量。Go 函数也可以是匿名的，这意味着你可以定义一个没有名字的函数，并在任何需要表达式的地方使用它。

首先，让我们看看如何将函数赋给变量:

```
func square(number int) int {
  return number * number;
}func main() {
  num := 2
  sq := square
  fmt.Printf("The square of %d is %d.\n", num, sq(num))
}
```

现在让我们在表达式中使用匿名函数:

```
func main() {
  num := 2
  sq := func(num int) int { return num * num }
  fmt.Printf("The square of %d is %d.\n", num, sq(num))
}
```

在这个例子中，一个匿名函数被分配给变量`sq`。您可以看到，除了缺少函数名之外，匿名函数定义看起来像常规函数定义。

当您需要一个函数进行一次性计算时，匿名函数非常有用。匿名函数也可以让你的程序更具可读性。

# 更多即将推出的 Go 功能

我只是开始介绍所有需要介绍的关于 Go 中函数的内容。我省略了关于错误处理、延迟函数调用以及处理恐慌和恢复的内容。我将在一篇高级文章中回到这些主题。

感谢您的阅读，请给我发电子邮件提出意见和建议。