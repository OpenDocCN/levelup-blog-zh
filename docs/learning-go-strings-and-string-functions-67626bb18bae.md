# 学习围棋:字符串和字符串函数

> 原文：<https://levelup.gitconnected.com/learning-go-strings-and-string-functions-67626bb18bae>

![](img/26ff913d9569bc97b7ee892f314aa9d4.png)

塔拉·埃文斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我将向您展示如何在 Go 中形成和使用字符串。像大多数现代语言一样，string 数据类型的大部分功能来自一个库，或者在 Go 中是一个包。这个包叫做`strings`，我将演示这个包中的几个函数。

# 字符串数据类型

Go 字符串是不可变的字节序列。不可变意味着一旦一个字符串被创建，它就不能被改变，尽管如果一个字符串被存储在一个变量中，这个变量是可以被改变的。

字符串是通过将希望被视为字符串的字节放在双引号之间而形成的。以这种方式形成的字符串称为字符串文字，可以在赋值中使用，也可以作为函数的参数使用。

以下是字符串变量和字符串文字的一些示例:

```
name := "Dennis Ritchie"
var greeting string = "Hello, world!"
```

在下一节中，我将演示字符串不可变的含义。

# 对字符串的基本操作

我要演示的第一个操作是 len 函数。这个函数返回字符串中的字符数。这里有一个例子:

```
func main() {
  greeting := "Hello, world!"
  fmt.Printf("The length of greeting is %d.\n", len(greeting))
}
```

Go 字符串的索引类似于数组和切片。我可以使用索引的`for`循环显示字符串的每个字节(也可以使用 range 函数):

```
func main() {
  greeting := "Hello, world!"
  for i:=0; i<len(greeting); i++ {
    fmt.Println(greeting[i])
  }
}
```

稍后我将向您展示如何将字节转换成字母字符。

尽管可以通过索引访问字符串的每个字节，但不能以这种方式修改字节:

```
greeting[1] = "E"
```

这导致了恐慌，并证明了 Go 字符串的不变性。

我可以把一根绳子切成几段，把它切成几片:

```
func main() {
  greeting := "Hello, world!"
  fmt.Println(greeting[0:5]) // displays Hello
}
```

所有不同的处理切片的方法都适用于字符串。

可以使用串联将多个字符串“粘合”在一起。Go 串联运算符是`+`。这里有一个例子:

```
func main() {
  first := "Ken"
  last  := "Thompson"
  full := first + " " + last
  fmt.Println(full) // displays Ken Thompson
}
```

和其他语言一样，Go 有特殊的字节字符序列，可以嵌入到字符串中。这些序列类似于 C 和 C++中的序列，比如新行的`\n`和 tab 的`\t`。

# 字符串包

处理字符串所需的大部分功能都在`strings`包中。在这一节中，我将介绍这个包中的几个函数。

我们要看的第一个函数是`contains`。这个布尔函数检查一个字符串是否包含指定的子串。这里有一个例子:

```
package mainimport (
  "fmt"
  "strings"
)func main() {
  address := "3000 W. Scenic Drive"
  if strings.Contains(address, "Scenic") {
    fmt.Print("Located on Scenic Drive")
  } else {
      fmt.Print("Located somewhere else.")
  }
}
```

我要看的下一个函数是`count`。此函数返回指定子串在字符串中出现的次数。它是这样工作的:

```
package mainimport (
  "fmt"
  "strings"
)func main() {
  s := "now is the time for all good people"
  os := strings.Count(s, "o")
  fmt.Println(s)
  fmt.Printf("There are %d o's in the string.\n", os)
}
```

接下来是`fields` 函数。这个函数将一个字符串分解成一个或多个空白字符序列，返回剩余子字符串的一部分。该函数的一个示例如下所示:

```
package mainimport (
  "fmt"
  "strings"
)func main() {
  s := "   now is   the     time for    all good       people"
  words := strings.Fields(s)
  fmt.Println(words)
}
```

`index`函数返回子串在字符串中的索引位置，如果在字符串中没有找到子串，则返回`-1`。下面是一个使用该函数的程序:

```
package mainimport (
  "fmt"
  "strings"
)func main() {
  s := "now is the time for all good people"
  foundAt := strings.Index(s, "time")
  if foundAt > -1 {
    fmt.Printf("time is found at position %d in the string.\n",
               foundAt)
  } else {
    fmt.Println("time is not found in string.")
  }
}
```

我描述的下一个函数用于将几个子字符串放入一个字符串中。这个功能叫做`join`。第一个参数是一个包含一组字符串的切片。第二个参数是一个分隔符，它将被放置在切片中的每个字符串之间。

这里有一个例子:

```
package mainimport (
  "fmt"
  "strings"
)func main() {
  sl := []string{"now","is","the","time"}
  joined := strings.Join(sl," ")
  fmt.Println(joined)
}
```

`repeat`函数将字符串重复指定的次数:

```
package mainimport (
  "fmt"
  "strings"
)func main() {
  fmt.Println(strings.Repeat("ei", 2) + "o") // displays eieio
}
```

`replace`函数将用另一个子字符串替换前 *n* 个子字符串，并返回新的字符串。如果 *n* 小于 0，那么它将尽可能多地进行替换。

下面的示例演示如何使用函数在字符串中进行一次替换:

```
package mainimport (
  "fmt"
  "strings"
)func main() {
  misspellings := "recieve decieve reciever deciever believe"
  fmt.Println(misspellings)
  count := 1
  spellings := strings.Replace(misspellings, "cie", "cei",
                               count)
  fmt.Println(spellings)
}
```

如果你想用一个不同的子串替换一个字符串中的所有子串，使用`ReplaceAll`函数。它是这样工作的:

```
package mainimport (
  "fmt"
  "strings"
)func main() {
  misspellings := "recieve decieve reciever deciever believe"
  fmt.Println(misspellings)
  spellings := strings.ReplaceAll(misspellings, "cie", "cei")
  fmt.Println(spellings)
}
```

我要演示的下一个函数是`join`的姊妹函数。`split`函数接受一个字符串和一个分隔符，并返回一个包含分隔符之间所有文本的切片。如果字符串是`“Meredith,Allison,Mason”`，分隔符是，那么返回的切片是`[Meredith Allison Mason]`。下面是实现这一点的代码:

```
package mainimport (
  "fmt"
  "strings"
)func main() {
  siblings := "Meredith,Allison,Mason"
  splitUp := strings.Split(siblings, ",")
  fmt.Println(splitUp)
}
```

接下来的两个函数可以用来改变字符串的大小写。`ToLower`将字符串改为全小写，而`ToUpper`将大小写改为全大写。这里有一个例子:

```
package mainimport (
  "fmt"
  "strings"
)func main() {
  s := "I'M YELLING"
  fmt.Println(strings.ToLower(s))
  s = "i'm whispering"
  fmt.Println(strings.ToUpper(s))
}
```

我要演示的最后一个例子是`TrimSpace`函数，它从字符串的开头和结尾开始修剪所有的空格。这个函数是这样工作的:

```
package mainimport (
  "fmt"
  "strings"
)func main() {
  name := "  Dennis Ritchie    "
  fmt.Println(name)
  name = strings.TrimSpace(name)
  fmt.Println(name)
}
```

这只是在 strings 包中找到的函数的一个示例，您应该查看包中的 Go 文档以查看完整的列表。

# 字符串转换

我需要非常简单地再提一个包— `strconv`。这个包包含了将字符串转换成数字和将数字转换成字符串的函数，以及其他转换。

函数`Itoa`将整数转换成字符串。下面是它的用法示例:

```
package mainimport (
  "fmt"
  "strconv"
)func main() {
  fmt.Print("Hello, Agent " + strconv.Itoa(99) + ".")
}
```

函数`Atoi`将字符串转换成整数。它还返回一个必须在函数调用中处理的错误对象，如下例所示:

```
package mainimport (
  "fmt"
  "strconv"
)func main() {
  number1, err := strconv.Atoi("1")
  number2, err1 := strconv.Atoi("2")
  result := number1 + number2
  if err == nil && err1 == nil {
    fmt.Printf("%d + %d = %d.\n", number1, number2, result)
  }
}
```

在`strconv`包中还有几个功能，但这是你最常用的两个。

还有另外两个关于 Go strings、bytes 和 Unicode 的包，但是我不打算在这里介绍它们。如果您对这些包感兴趣，请参阅 Go 文档。

# 字符串和字符

您应该注意到 Go 中没有 char 类型。`byte`类型用于此目的，正如我们在本文开始时看到的，当时我使用索引 for 循环遍历一个字符串。在这篇文章的最后，我将向您展示如何使用`Printf`函数将字节转换成字符串:

```
func main() {
  greeting := "Hello, world!"
  for i:=0; i<len(greeting); i++ {
    fmt.Printf("%c\n", greeting[i])
  }
}
```

格式规范%c 会将字节转换为字符，以便在输出中显示。

# 去和弦

Go 有一个很好的、现代的字符串实现。在这篇文章中，我介绍了我认为在 Go 中使用字符串最重要的方面，但是我没有介绍所有的内容。我特别避免谈论 Go 如何在字符串的实现中使用 Unicode。如果你想了解更多这方面的知识，我建议你读一读艾伦·多诺万和布莱恩·柯尼根的书《Go 编程语言》。

感谢您的阅读，请给我发电子邮件提出意见和建议。