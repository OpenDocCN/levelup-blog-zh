# 学习 Go:各种数组处理模板

> 原文：<https://levelup.gitconnected.com/learning-go-various-array-processing-templates-865c6ba4627>

![](img/1cde692b4c3975d87b9e45c8cd64574c.png)

照片由[马腾纽霍尔](https://unsplash.com/@laughayette?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

上一篇文章展示了一些在 Go 中使用数组的编程模板。在本文中，我将介绍更多的模板。这些模板包括用于搜索数组、复制数组内容以及向数组中插入新元素的模板。

# 搜索数组模板

可以使用简单的线性算法搜索一维数组。该算法从数组的第一个元素开始，检查是否匹配，如果匹配，则返回真值或元素位置，或者继续到下一个元素。算法结束，如果没有找到匹配，则返回一个`false`值或一个`-1`(一个无效的数组位置)。

我们可以使用两个伪代码模板，一个用于布尔返回，另一个用于返回找到的元素的数组位置或-1 的算法。以下是这两种可能性的伪代码，首先是布尔模板:

*声明布尔变量并设置为假
从第一个数组元素
开始，而不是从最后一个数组元素开始:
如果数组元素匹配搜索值:
将布尔变量设置为真
否则
移动到下一个元素*

这是一个整数值的版本:

*将位置变量设置为-1
从第一个数组元素
开始，而不是最后一个数组元素:
如果数组元素与搜索值匹配:
将位置变量设置为数组元素位置
否则
移动到下一个元素*

现在让我们看看如何在 Go 中实现这些模板。首先，这里是布尔变量版本:

```
package mainimport (
  "fmt"
  "math/rand"
  "time"
 )func main() {
  const size = 10;
  seed := rand.NewSource(time.Now().Unix())
  rng := rand.New(seed)
  var numbers[size] int
  for i := 0; i < size; i++ {
    numbers[i] = rng.Intn(100)
  }
  fmt.Println(numbers)
  var find int
  found := false
  fmt.Print("Enter a number to search for: ")
  fmt.Scan(&find)
  for _, num := range numbers {
    if find == num {
      found = true
    }
  }
  if found {
    fmt.Println(find,"is in the array.")
  } else {
    fmt.Println(find,"is not in the array.")
  }
}
```

这是整数位置版本:

```
package mainimport (
  "fmt"
  "math/rand"
  "time"
)func main() {
  const size = 10
  seed := rand.NewSource(time.Now().Unix())
  rng := rand.New(seed)
  var numbers[size] int
  for i := 0; i < size; i++ {
    numbers[i] = rng.Intn(100)
  }
  fmt.Println(numbers)
  var find int
  position := -1
  fmt.Print("Enter a number to search for: ")
  fmt.Scan(&find)
  for index, num := range numbers {
    if find == num {
      position = index
    }
  }
  if position > -1 {
    fmt.Printf("%d is at position %d.\n", find, position)
  } else {
    fmt.Println(find,"is not in the array.")
  }
}
```

这种类型的搜索称为线性搜索或顺序搜索，适用于小数据集，但对于大数据集效率很低。一个更有效的算法是二分搜索法算法，它需要一个排序的数组，但在数组中找到一个元素需要更少的比较。

# 复制数组模板

有些情况下，您需要将一个数组的内容复制到另一个数组。您可以通过声明一个新的空数组，然后使用循环将现有数组的内容复制到新数组来实现这一点。该模板的伪代码如下所示:

*为旧数组的每个元素声明新数组
:
将元素复制到新数组的相同位置*

下面是该模板的一个实现:

```
package mainimport (
  "fmt"
  "math/rand"
  "time"
)func main() {
  const size = 10
  seed := rand.NewSource(time.Now().Unix())
  rng := rand.New(seed)
  var numbers[size] int
  for i := 0; i < size; i++ {
    numbers[i] = rng.Intn(100)
  }
  var numbersCopy[size] int
  for index, number := range numbers {
    numbersCopy[index] = number
  }
  fmt.Println("Old array: ")
  fmt.Println(numbers)
  fmt.Println("Copied array: ")
  fmt.Println(numbersCopy)
}
```

然而，复制数组的额外循环是不必要的。您可以简单地将旧数组分配给新数组，以复制旧数组。你不能在 C++中这样做，因为它创建了旧数组的一个*浅拷贝*。使用浅层复制，如果原始数组中的一个元素被更改，新数组中该位置的元素也会被更改。

我们上面所做的将原始数组复制到新数组的操作称为*深度复制*。如果我们在 C++中执行这种复制，对原始数组的更改将不会反映在新数组中。

在 Go 中，通过赋值来复制数组会执行数组的深度复制，因此不需要逐个元素地复制，我们可以编写如下代码:

```
package mainimport (
  "fmt"
  "math/rand"
  "time"
)func main() {
  const size = 10
  seed := rand.NewSource(time.Now().Unix())
  rng := rand.New(seed)
  var numbers[size] int
  for i := 0; i < size; i++ {
    numbers[i] = rng.Intn(100);
  }
  numbersCopy := numbers
  fmt.Println("Old array: ")
  fmt.Println(numbers)
  fmt.Println("Copied array: ")
  fmt.Println(numbersCopy)
}
```

# 将元素插入数组模板

当您需要将数据元素插入数组时，可以使用该模板。当插入到一个数组中时，首先要找到想要插入新值的地方。然后将插入点右侧的所有值移动一个位置，为新值腾出空间。最后，在移位完成后，在移位后腾出的空间中插入新值。

模板的伪代码如下所示:

*(新值将被插入到集合中的位置 p)
将变量(I)设置为集合的最后一个元素的索引加 1(k)
当 I 大于 p+1 时重复:
coll[I]= coll[I-1]
coll[p]=新值
将集合元素的编号加 1*

下面是一个使用这个模板的问题。您有一组按数字顺序排列的成绩，您需要在这组成绩的中间添加一个成绩来保持顺序。下面是一个解决这个问题的程序:

```
func main() {
  const size = 20
  last := 9
  grades := [size] int {71,72,73,75,76,77,78,80,89,90}
  fmt.Println(grades)
  newGrade := 74
  var start int
  for index, grade := range grades {
    if grade > newGrade {
      start = index
      break
    }
  }
  for i := last; i >= start; i-- {
    grades[i+1] = grades[i]
  }
  grades[start] = newGrade
  fmt.Println(grades)
}
```

这个模板对于小数据集来说很好，但是对于大数据集来说效率非常低，因为如果插入点在大数据集的前半部分，就必须进行多次移动来为插入的元素打开一个槽。

必须进行大量插入和删除的问题的解决方案是使用不同的数据结构，比如链表，但这超出了本文的范围。

# Go 数组和切片

数组是所有严肃的计算机编程语言中常见的数据结构。过去，数组是大多数编程入门课程中讨论的唯一数据结构。然而，这种情况已经发生了变化，因为大多数语言现在提供了一种更灵活的数据结构，其效率实际上与数组相同。在其他语言中，这是 C++中的 vector，或者 Java 或 C#中的 ArrayList，或者 Python 中的 List。在 Go 中，这种更灵活的数据结构被称为切片，这将是我下一篇文章的主题。

感谢您的阅读，如果您有任何意见或建议，请发邮件给我。