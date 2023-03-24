# 学习 Go:switch 语句和从备选项中选择模板

> 原文：<https://levelup.gitconnected.com/learning-go-the-switch-statement-and-the-select-from-alternatives-template-15aad769774a>

![](img/686bf93457be69a2bfcd54f8f80bd718.png)

由[维多利亚诺·伊斯基耶多](https://unsplash.com/@victoriano?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在我的上一篇[文章](/learning-go-the-if-statement-and-the-select-from-alternatives-template-ebe9748cb0d7)中，我讨论了如何将 Go 的`if`语句表单与 Select from Alternatives 编程模板一起使用。在本文中，我将看看如何在这个模板中使用 Go 的`switch` 语句。

# switch 语句

`switch`语句是一个多向分支结构，它使用单个值来决定采用程序的哪个分支。您不能使用带有一系列值的`switch`语句，但是与 C++和其他语言不同，`switch`语句评估的操作数可以是任何数据类型。这意味着您可以“切换”字符串、布尔值或其他类型。

switch 语句有两个主要的语法模板。第一个使用一个操作数，像这样:

*切换操作数{
case option1:
语句
case option2:
语句
case option-n:
语句
默认:
语句
}*

第二个语法模板不使用操作数，而是在以下情况下计算条件:

*switch {
case condition 1:
语句
case condition2:
语句
case condition-n:
语句
默认值:
语句
}*

在这两种情况下，default 子句都是可选的，其行为很像`if-else if`中的`else` 。

在我们查看模板并解决一些问题之前，这里是 Go 的`switch`语句的第一个例子(为了节省空间，省略了 package 和 imports 语句):

```
func isEven(number int) bool {
  if number % 2 == 0 {
    return true
  }
  return false
}func main() {
  var number int
  for i:=1; i <= 5; i++ {
    fmt.Print("Enter a number: ")
    fmt.Scan(&number)
    switch isEven(number) {
      case true:
        fmt.Printf("%d is even.\n", number)
      case false:
        fmt.Printf("%d is odd.\n", number)
      default:
        fmt.Println("Was that a number?")
    }
  }
}
```

这个程序使用一个函数来确定一个数是奇数还是偶数。我还没有讨论 Go 函数，但是你应该能够理解它的定义。操作数由`isEven()`函数接收，然后案例评估操作数的布尔值。

# 从备选方案中选择项目模板

上面的程序是如何在 Go 中实现*从备选项中选择*模板的一个例子。首先，这里有一些描述这个模板的伪代码:

*如果选项 1，执行任务 1
如果选项 2，执行任务 2
如果选项-n，执行任务-n*

让我们用这个模板来解决一些问题。

编写一个简单的计算器，从用户那里接受两个数字，然后让用户决定他们想要解决哪个算术问题:加法、减法和乘法。一旦用户做出选择，程序应该让用户输入他们的答案，然后显示答案是否正确。将此放入运行 5 次的循环中。使用随机数生成来生成问题的数字。

这里有一个可能的解决方案:

```
package mainimport (
        "fmt"
        "math/rand"
        "time"
        )func main() {
  var number1, number2, result, answer, choice int
  const stop = 5
  seed := rand.NewSource(time.Now().Unix())
  randGen := rand.New(seed)
  for i:=1; i<stop; i++ {
    number1 = randGen.Intn(100)
    number2 = randGen.Intn(100)
    fmt.Println("1\. Addition")
    fmt.Println("2\. Subtraction")
    fmt.Println("3\. Multiplication")
    fmt.Print("Enter choice: ")
    fmt.Scan(&choice)
    switch choice {
      case 1:
        result = number1 + number2
        fmt.Printf("%d + %d = ? ", number1, number2)
        fmt.Scan(&answer)
        if answer == result {
          fmt.Println("That's right.")
        } else {
          fmt.Printf("Sorry, the answer is %d.\n", result)
        }
      case 2:
        result = number1 - number2
        fmt.Printf("%d - %d = ? ", number1, number2)
        fmt.Scan(&answer)
        if answer == result {
          fmt.Println("That's right!")
        } else {
          fmt.Printf("Sorry, the answer is %d.\n", result)
        }
      case 3:
        result = number1 * number2
        fmt.Printf("%d x %d = ? ", number1, number2)
        fmt.Scan(&answer)
        if answer == result {
          fmt.Println("That's right!")
        } else {
          fmt.Printf("Sorry, the answer is %d.\n", result)
        }
    }
  }
}
```

与使用`if-else if`语句相比，`switch`语句通常会产生更结构化的解决方案。

还要注意，我植入了随机数生成器，这样程序每次运行时都会出现不同的数字。种子基于系统时间(`Time.Now()`)和另一个函数调用`Unix()`，后者返回自 1970 年 1 月 1 日以来的秒数。

这里还有一个问题，演示了`switch`语句的操作数是一个字符串。我提到这一点是因为在某些语言中，`switch`操作数只能是整数值。

写一个让用户输入月份的程序。该程序返回该月的天数。这个程序还演示了如何在 case 子句中进行“测试”。代码如下:

```
func main() {
  var month string
  var days int
  fmt.Print("What month? ")
  fmt.Scan(&month)
  switch {
    case month == "February":
      days = 28;
    case month == "September" ||
         month == "April" ||
         month == "June" ||
         month == "November":
      days = 30;
    default:
      days = 31;
  }
  fmt.Printf("%s has %d days in it.\n", month, days)
}
```

# 软件设计哲学的一个侧面

如果您是编程新手，您可能想知道何时选择使用一个`switch`语句，而不是使用一系列`if-else if`语句。一般规则是，当选择选项简单时，选择一个`switch` 语句。当您必须在一系列选项中进行选择时，您应该选择一个`if-else if`语句。

这里有两个例子。您正在编写一个评分程序，该程序将+和-标记分配给等级，例如 A-、A-和 A+。这些选择是基于分数的范围，比如 90 到 93 分可以得 A-；93 到 96 赚 A；97 分及以上得 A+。这个问题要用一个`if-else if`语句来解决。

另一方面，如果您正在编写一个将数字转换为罗马数字的程序，您会选择一个`switch`语句，因为没有可供选择的范围。

下面是一个使用`switch`语句将数字转换成罗马数字的程序:

```
func main() {
  var number int
  var roman string
  fmt.Print("Enter a number (o to quit): ")
  fmt.Scan(&number)
  for number != 0 {
    switch number {
      case 1:
        roman = "I"
      case 2:
        roman = "II"
      case 3:
        roman = "III"
      case 4:
        roman = "IV"
      case 5:
        roman = "V"
      case 6:
        roman = "VI"
      case 7:
        roman = "VII"
      case 8:
        roman = "VIII"
      case 9:
        roman = "IX"
      case 10:
        roman = "X"
      default:
        fmt.Println("Didn't recognize that input.")
    }
    fmt.Printf("%d in Roman numerals is %s.\n", number, roman)
    fmt.Print("Enter a number (0 to quit): ")
    fmt.Scan(&number)
  }
}
```

# switch 语句和从备选方案中进行选择

Go 中有两个用于选择选项的构造: `if-else if`语句和`switch`语句。当替代项嵌入在范围中时，您应该选择使用`if-else if`语句。如果备选方案简单且独立，那么您应该选择`switch`语句。另一个考虑是是否有几个简单的选择。如果是这种情况，应该选择`switch`语句，因为`switch`语句中的代码通常比`if-else if`语句中的代码更容易阅读。

感谢您阅读这篇文章，如果您有任何意见或建议，请发邮件给我。