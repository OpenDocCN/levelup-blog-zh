# 学习 Go:对于循环和输入，处理一个模板

> 原文：<https://levelup.gitconnected.com/learning-go-for-loops-and-the-input-one-process-one-template-de54d1af1571>

![](img/bcfb39675228a5627508ca90550eb76e.png)

照片由 [Nareeta Martin](https://unsplash.com/@splashabout?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在这篇文章中，我将介绍如何在 Go 中执行迭代(循环)。Go 只有一个循环结构，即`for`循环。本文将只讨论`for`循环的一个版本——这个版本取代了其他编程语言中的`while`循环。我还将在一个新的编程模板的上下文中演示循环— *输入一个，处理一个*。

# `for`回路

我们编写计算机程序的一个主要原因是重复执行任务(迭代)。当人类连续多次执行一项任务时，他们会感到厌烦，并倾向于开始出错。另一方面，计算机永远不会感到无聊和疲倦，所以我们可以给它们编程，让它们重复同样的任务数百次，而不用担心出错。

编程语言都有执行迭代的构造。大多数编程语言都有三四个迭代结构，但是 Go 只有一个 for 循环。Go 的 for 循环被设计成可以处理你想在程序中执行迭代的所有不同方式。

我将在本文中讨论的 for 循环版本的语法模板如下所示:

*for 条件{
语句
}*

当条件为真时，`for`循环执行循环体内的语句。如模板所示，循环体中可以有多个语句。

下面是一个打印数字 1 到 10 的简单示例(我排除了 Go 程序的开始部分，除非我需要导入一个包):

```
func main() {
  i := 1
  for i <= 10 {
    fmt.Println(i)
    i++
  }
}
```

请注意，条件没有写在括号内，事实上，如果您使用括号，这是一个错误。您可以将条件放在括号中，但去掉括号是常见的 Go 编程风格。

但是，即使循环体只包含一条语句，也必须包含循环体的大括号。对于那些允许你去掉大括号的语言来说，这已经成为一种常见的编程习惯，我推荐 Go 开发团队在所有的`for`循环中使用大括号。

# 控制具有标记值的循环

一种经常使用的回路是*哨兵控制的*回路。标记是对程序有特殊意义的值。它和`for`循环一起使用通常是为了表示是时候停止循环了。

下面是一个示例 Go 程序，它会回显用户的输入，直到用户写下“Stop”:

```
func main() {
  var sentence string
  fmt.Print("Enter a word (type Stop to stop): ")
  fmt.Scan(&sentence)
  for sentence != "Stop" {
    fmt.Println(sentence)
    fmt.Print("Enter a word (type Stop to stop): ")
    fmt.Scan(&sentence)
  }
}
```

在这个程序中,“Stop”是一个标记值，当用户进入循环时，它会强制循环停止。

# 输入一个，处理一个模板

既然我们已经介绍了使用`for`循环的基础知识，并了解了如何使用 sentinel 值控制循环，那么让我们看看如何将它应用到编程模板中。 *Input One，Process One* 模板用于提示用户输入一个值，然后在一个循环中多次使用该值进行操作。下面是模板的伪代码:

*重复这些步骤:
从输入
中读取一个值，处理该值*

下面是一个应用这个模板解决问题的例子。你是一名教师，你想计算一组考试成绩的平均值。您不确定您需要输入多少分数，所以您决定停止输入-1 作为测试分数，因为最低的测试分数是 0(尽管我在过去曾受到学生的负面分数威胁)。下面是程序，以及指示模板中步骤的注释:

```
func main() {
  const stopValue = -1
  var grade, total, count int
  fmt.Print("Enter a score (-1 to quit): ")
  // input one
  fmt.Scan(&grade)
  for grade != stopValue {
    count++
    // process one
    total += grade
    fmt.Print("Enter a score (-1 to quit): ")
    fmt.Scan(&grade)
  }
  average := float64(total) / float64(count)
  fmt.Printf("The average grade is %.2f.\n", average)
}
```

在这个程序中有一些事情需要注意。首先，我使用一个常数来表示 sentinel 值。这只是好的编程风格，应该在任何编程语言的所有程序中遵循。

第二件事是为了计算平均值，我必须使用类型转换将`total`和`count` 变量从`int`转换为`float64`。这是因为如果两个操作数都是整数，那么`/` 运算符将执行整数除法，而 Go 不允许只转换分子或分母。您必须将两个操作数都转换为浮点，程序才能编译。

# 计数控制回路

还有另一个版本的 `for`循环可以用于计数控制循环，但是如果你愿意，你可以使用我在这里讨论的版本。如果我们将上述问题转化为已经知道我们需要输入多少个测试分数，我们就不必使用哨兵控制的循环，而是可以使用计数控制的循环。

下面是对上述程序的修改，将它改为计数控制回路，而不是哨兵控制回路:

```
func main() {
  var grade, total int
  count := 0
  const numGrades = 5
  for count < numGrades {
    fmt.Print("Enter a grade: ")
    // input one
    fmt.Scan(&grade)
    // process one
    total += grade
    count++;
  }
  average := float64(total) / float64(count)
  fmt.Printf("The average grade is %.2f.\n", average)
}
```

对于那些懂一些编程的人来说，你可能会奇怪为什么我在这个程序中没有使用常规的 For 循环。我故意不只是为了证明这种格式的 Go for 循环可以用于计数控制的循环，尽管这样做并不被认为是好的编程实践。在我的下一篇文章中，我将使用一个常规的 for 循环来演示下一个编程模板— *按指定的次数做某事*。

# 转到 for 循环

Go 编程语言中我最喜欢的特性之一是该语言中有限的循环结构。当只有一个构造来创建循环时，这使得语言更容易学习和使用。一些程序员可能会觉得这有局限性，但我相信这使得用 Go 编程比用拥有所有可以想象的特性的语言编程更有趣。

我的下一篇文章将展示 for 循环的更标准版本，以及它如何与*Do thing a Specified Number Times*模板一起使用。

感谢您阅读这篇文章，如果您有任何意见或建议，请发邮件给我。