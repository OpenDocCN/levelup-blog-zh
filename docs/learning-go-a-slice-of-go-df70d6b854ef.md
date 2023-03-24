# 学习围棋:围棋的一部分

> 原文：<https://levelup.gitconnected.com/learning-go-a-slice-of-go-df70d6b854ef>

![](img/311f1a6ba8111c9b73c5b136459c67dc.png)

照片由[亨利贝](https://unsplash.com/@henry_be?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在这篇文章中，我将演示如何使用切片数据结构。切片是同一类型的未绑定数据序列。切片可以增长和收缩，因此它的大小不像数组那样固定。关于切片有很多要说的，所以我也将在下一篇文章中继续讨论它们。

在我开始讨论如何使用片之前，我需要提到的是，现在绝大多数编程专家都说，当需要存储数据时，您应该考虑的第一个数据结构是某种类型的动态结构，比如片。现代编程语言实现这些数据结构的效率比过去高得多，而且像 slice 这样的动态结构带来了如此多的便利，以至于当 slice、vector、ArrayList 或 List 也可以使用时，使用数组就没有意义了。

# 切片基础

正如我提到的，切片是同一数据类型的未绑定数据序列。从头开始创建切片时，也会创建一个底层数组。如果从零开始创建一个存储片，它开始时的容量为 0，随着新元素的添加，容量也会随之增加。这是通过向底层阵列分配新内存来实现的。

像数组一样声明一个片，但是没有指定大小。以下是一些例子:

```
var names[] string
var numbers[] int
var flags[] bool
```

也可以从现有阵列创建切片。通过使用 slice 操作符从数组中引用所需的元素，可以实现这一点。切片操作符写成*【start:stop】*，其中 start 是切片开始的元素，stop 是要停止的元素，但不包括该元素。

在下面的示例中，切片`firstThree`获取了`numbers`数组的前三个元素:

```
numbers := [size]int{1,2,3,4,5}
firstThree := numbers[0:3]
```

通过省略起始值和停止值，可以将整个数组移动到切片中:

```
firstThree := numbers[:]
```

下面是另一个从数组中提取切片的例子，该数组提取最后两个元素:

```
lastTwo := numbers[3:]
```

请记住，如果基础数组的元素发生变化，基于基础数组的切片的元素也会发生变化。例如:

```
func main() {
  const size = 5
  numbers := [size]int{1,2,3,4,5}
  lastTwo := numbers[3:]
  fmt.Println(lastTwo) // [4 5] is displayed
  numbers[3] = 5
  fmt.Println(lastTwo) // [5 5] is displayed
}
```

创建切片的另一种方法是使用内置的`make`函数。此函数采用数据类型切片说明符、切片长度和切片容量(可用的总元素)。`make`函数的语法模板如下所示:

*切片名称:=制作([]数据类型，长度，容量)*

您可以省略容量，这将使其与长度参数相同。下面是语法模板:

*切片名称:=制作([]数据类型，长度)*

下面是演示如何使用`make`函数的代码片段:

```
numbers := make([]int, 0)
```

如果您从一个空切片开始，您可以使用`append`函数向切片添加元素。追加的语法模板是:

*切片名称=追加(切片名称，元素)*

您必须将附加的切片重新分配回切片中，因为您无法确定新的切片是否仍将引用旧切片的基础数组。从语法上来说，你无论如何都要做作业。

下面是一个程序，它将十个随机生成的数字分配给一个切片:

```
package mainimport (
  "fmt"
  "math/rand"
  "time"
)func main() {
  seed := rand.NewSource(time.Now().Unix())
  rng := rand.New(seed)
  var numbers[] int
  for i:=1; i<=10; i++ {
    numbers = append(numbers, rng.Intn(100))
  }
  fmt.Print(numbers)
}
```

# 复制切片和其他切片功能

先说对比。不能用`==`操作符比较两个切片是否相等。如果你尝试，你会得到一个错误。相反，你将不得不写一个函数来做这件事。这里有一个例子:

```
package mainimport (
  "fmt"
  "math/rand"
  "time"
)func equal(s1, s2 []int) bool {
  if len(s1) != len(s2) {
    return false
  }
  for i := range s1 {
    if s1[i] != s2[i] {
      return false
    }
  }
  return true
}func main() {
  seed := rand.NewSource(time.Now().Unix())
  rng := rand.New(seed)
  numbers := make([]int, 0)
  for i:=1; i<=10; i++ {
    numbers = append(numbers, rng.Intn(100))
  }
  numbersCopy := make([]int, len(numbers))
  copy(numbersCopy, numbers)
  fmt.Println(numbers)
  fmt.Println(numbersCopy)
  if equal(numbers, numbersCopy) {
    fmt.Print("The same.")
  } else {
    fmt.Print("Different.") 
  }
}
```

您可以编写一个函数来反转切片的内容(不像其他语言那样有内置的反转函数)。这里有一个来自多诺万和克尼根的 Go 编程语言的例子:

```
package mainimport (
  "fmt"
  "math/rand"
  "time"
)func reverse(sl[] int) {
  for i, j := 0, len(sl)-1; i < j; i, j = i+1, j-1 {
    sl[i], sl[j] = sl[j], sl[i]
  }
}func main() {
  seed := rand.NewSource(time.Now().Unix())
  rng := rand.New(seed)
  numbers := make([]int, 0)
  for i:=1; i<=10; i++ {
    numbers = append(numbers, rng.Intn(100))
  }
  fmt.Println(numbers)
  reverse(numbers)
  fmt.Println(numbers)
}
```

有两个内置函数可用于切片。一个函数`len`返回切片的长度，如下例所示:

```
for i:=1; i<=10; i++ {
  numbers = append(numbers, rng.Intn(100))
}
fmt.Println(numbers)
fmt.Printf("The length of numbers is %d.\n", len(numbers))
```

您也可以使用`cap`功能检索切片的*容量*。一个片的容量是可以添加到该片的元素总数，而不必为更多存储空间分配内存。

以下程序演示了随着更多数据添加到原本为空的切片中，容量是如何分配的:

```
func main() {
  numbers := make([]int, 0)
  for i:=1; i<=10; i++ {
    numbers = append(numbers, i)
    fmt.Printf("The capacity of numbers is %d.\n", cap(numbers))
  }
}
```

这个程序的输出是:

```
The capacity of numbers is 1.
The capacity of numbers is 2.
The capacity of numbers is 4.
The capacity of numbers is 4.
The capacity of numbers is 8.
The capacity of numbers is 8.
The capacity of numbers is 8.
The capacity of numbers is 8.
The capacity of numbers is 16.
The capacity of numbers is 16.
```

# 处理切片的所有元素

有两种方法可以处理切片的所有元素。第一种方法是使用索引`for`循环。下面是一个演示这种技术的程序:

```
func main() {
  grades := []int {88, 71, 91, 83, 100}
  var total int
  for i:=0; i<len(grades); i++ {
    total += grades[i] 
  }
  average := float64(total) / float64(len(grades))
  fmt.Printf("The average is %.2f.\n", average)
}
```

迭代切片值的更安全的方法是使用一个`range for`循环。下面是上面用一个`range for`循环重写的程序:

```
func main() {
  grades := []int {88, 71, 91, 83, 100}
  var total int
  for _, grade := range grades {
    total += grade
  }
  average := float64(total) / float64(len(grades))
  fmt.Printf("The average is %.2f.\n", average)
}
```

# 一片切片

在这篇文章中，我只介绍了切片的一部分功能。在我的下一篇文章中，我将更详细地介绍如何使用切片的切片，这也是切片比使用数组真正有优势的地方。

感谢您阅读这篇文章，请发送电子邮件提出意见或建议。