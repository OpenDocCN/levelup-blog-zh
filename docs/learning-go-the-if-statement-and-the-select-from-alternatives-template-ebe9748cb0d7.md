# 学习 Go:if 语句和从备选项中选择模板

> 原文：<https://levelup.gitconnected.com/learning-go-the-if-statement-and-the-select-from-alternatives-template-ebe9748cb0d7>

![](img/7706507e5ddc8b5175de49f8db4dec55.png)

照片由[摄影师](https://unsplash.com/@ffstop?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在我的上一篇文章中，我讨论了 Go 中的循环。在本文中，我将讨论另一个控制流结构，if 语句，以及一个使用 if 的模板， *Select from Alternatives* 。

# if 语句

Go 中的`if`语句很像其他语言中的相同结构。`if`报表有三种形式:简单的`if`报表；`if-else`语句；和`if-else if`声明。以下是这三种形式的语法模板:

*//简单 if
if 条件{
语句；
}*

*// if-else
if 条件{
语句；
} else {
语句；
}*

*// if-else if
if 条件{
语句；
} else if (condition) {
语句；
}
…
} else {
语句；
}*

当涉及到`if-else if`时，C++或 Java 等语言的`if`语句和 Go 的`if`语句之间有一个重要的区别。在 Go 中，`else if`必须与它上面的块的右括号出现在同一行。你不能像这样放下别的东西:

```
if (condition) {
  // do something
}
else if (condition) {
  // do something else
}
```

它必须看起来像这样:

```
if (condition) {
  // do something
} else if (condition) {
  // do something else
}
```

放弃了`else`的 C 风格语言的程序员将不得不花一些时间来习惯这种形式，但是它很快就变成了第二天性，而且它与另一种常见的编程风格相匹配。

# 从备选项中选择模板

*从选项中选择*编程模板描述了如何在程序中做出决策。这个模板有几种不同的版本，您将使用的编程结构取决于您试图解决的问题。如果您在几个单一的选项中进行选择，您将使用`switch`构造。如果你在一系列选项中选择，你将使用`if`结构。我们将在本文中演示如何将`if`与该模板一起使用，并在下一篇文章中讨论如何将 switch 与该模板一起使用。

很难为这个模板提供伪代码，因为每个问题都是不同的，所以我将直接进入问题。

第一个问题使用了`if-else`。显示通过/失败测试的结果。用户应该输入他们的分数，然后收到一条消息说他们要么通过，要么失败。及格的最低分数是 70 分。

以下是解决方案:

```
func main() {
  var score int
  fmt.Print("Enter your score: ")
  fmt.Scan(&score)
  if score >= 70 {
    fmt.Print("Passed!")
  } else {
    fmt.Print("Failed!")
  }
}
```

这里有一个更棘手的问题。您正在为银行构建贷款申请。如果一个人超过 21 岁，你会先给他们贷款，然后如果他们目前有工作。一个全职的 19 岁的人不能获得贷款，一个失业的 44 岁的人也不能。

这里有一个解决方案:

```
func main() {
  var age int
  var employed string
  fmt.Print("Enter your age: ")
  fmt.Scan(&age)
  if age >= 21 {
    fmt.Print("Are you employed (Y/N)? ")
    fmt.Scan(&employed);
      if employed == "Y" {
        fmt.Print("Proceed with application.")
      } else {
          fmt.Print("You must be employed to get a loan.")
      }
  } else {
      fmt.Print("You must be at least 21 to get a loan.")
  }
}
```

这个解决方案使用了一个嵌套的 if 语句，仅当申请人年满 21 岁时，该语句才继续贷款资格。

这里是最后一个问题。编写一个程序，该程序接受一个测试分数，并按照 90 A、80 B、70 C、60 D、60 F 以下的分级标准为该测试分数指定一个字母等级。在课程结束时报告考试成绩和字母成绩。

这里有一个解决方案，后面还有一个更有效的解决方案:

```
func main() {
  var grade int
  var letterGrade string
  fmt.Print("Enter your test score: ")
  fmt.Scan(&grade)
  if grade >= 90 && grade <= 100 {
    letterGrade = "A"
  } else if grade >= 80 && grade <= 89 {
    letterGrade = "B"
  } else if grade >= 70 && grade <= 79 {
    letterGrade = "C"
  } else if grade >= 60 && grade <= 69 {
    letterGrade = "D"
  } else {
    letterGrade = "F"
  }
  fmt.Printf("A score of %d equals a letter grade of %s.\n",
              grade, letterGrade)
}
```

这个问题有更有效的解决方法。如果您认为一个 if-else if 在发现真条件后会脱离构造，那么您只需要测试一个等级是否大于或等于每个字母等级的最低值。这是新的解决方案:

```
func main() {
  var grade int
  var letterGrade string
  fmt.Print("Enter your test score: ")
  fmt.Scan(&grade)
  if grade >= 90 {
    letterGrade = "A"
  } else if grade >= 80 {
    letterGrade = "B"
  } else if grade >= 70 {
    letterGrade = "C"
  } else if grade >= 60 {
    letterGrade = "D"
  } else {
    letterGrade = "F"
  }
  fmt.Printf("A score of %d equals a letter grade of %s.\n",
             grade, letterGrade)
}
```

还有一个问题。编写一个简单的数学指导程序，为用户提供两个要相加的随机数。如果用户答对了问题，程序将显示“正确”,否则程序将显示正确答案。将程序放入一个循环中，给用户五个问题。

你需要导入到你程序中的随机数库是`math/rand`。你需要使用的函数是`rand.Intn(n)`，其中`n`比你要生成的数的上界小一。这个程序不植入随机数发生器，所以每次程序运行时都产生相同的一组数字。你可以通过在互联网上寻找如何播种随机数发生器来使这个程序更有用。我决定把它排除在这个程序之外，以保持新材料最少。

这里有一个解决方案:

```
package mainimport (
        "fmt"
        "math/rand"
       )func main() {
  var number1, number2, sum, answer int
  const stop = 10
  for i:=1; i <= stop; i++ {
    number1 = rand.Intn(100)
    number2 = rand.Intn(100)
    sum = number1 + number2
    fmt.Printf("%d + %d = ? ", number1, number2)
    fmt.Scan(&answer)
    if answer == sum {
      fmt.Println("Correct!")
    } else {
      fmt.Printf("The answer is %d.\n", sum)
    }
  }
}
```

# Go 和 if 语句

if 语句的 Go 版本与 JavaScript、Java 和 C++等语言中的 if 语句非常相似。一个主要的区别是你没有把条件表达式放在括号里，另一个区别是你不能在前一个块的右花括号后的新行开始一个 else 块。

在我的下一篇文章中，我们将研究实现 Select from Alternatives 模板的另一个构造 switch 语句。

感谢您阅读这篇文章，如果您有任何意见或建议，请发邮件给我。