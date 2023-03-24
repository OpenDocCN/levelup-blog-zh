# 学习围棋:地图

> 原文：<https://levelup.gitconnected.com/learning-go-maps-c56404049f78>

![](img/cea4cb242b7cf1143a016a9240c302fb.png)

安德鲁·斯图特斯曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

一个*映射*是一个关联数据结构，它根据*键*和*值*存储相关数据。电话簿(或年轻人的联系人列表)是地图的一个很好的例子，其中姓名与电话号码相关联。词典是地图的另一个例子，其中一个单词与一个定义相关联。

从技术上讲，Go map 是一个哈希表，其中的键被哈希为一个值，该值被用作存储该值的数据结构的键。如果您对哈希表不熟悉，请到这里查看介绍。

# 在 Go 中声明地图

可以用几种方式来声明一个映射。最好的方法是使用内置的`make`函数。该函数的语法模板如下所示:

*映射名称:= make(映射[关键字-数据类型]值-数据类型)*

这里有一个例子:

```
inventory := make(map[string] int)
```

键和值由冒号分隔，条目必须以逗号结束。

用于键的值类型必须是可比较的类型。您不应该对键使用浮点类型，因为众所周知，浮点值在比较时是不明确的。

# 地图操作

使用数组表示法访问地图值。例如，要更改库存图中的卫生纸数量，请编写:

```
inventory["toilet paper"] = 10
```

如果只需要将一卷添加到原料中，可以使用增量运算符:

```
inventory["toilet paper]++
```

现在，我将花点时间创建一个更大的清单:

```
func main() {
  inventory := map[string] int {
    "toilet paper": 0,
  }
  inventory["clorox wipes"] = 0
  inventory["milk"] = 2
  inventory["hand sanitizer"] = 1
  inventory["paper towels"] = 5
  inventory["eggs"] = 0
}
```

我可以通过使用一个`range for`循环来显示 map 中的键和值的列表:

```
for item, count := range inventory {
  fmt.Printf("%s: %d", item, count)
}
```

此代码的输出是(对于一次运行):

```
hand sanitizer: 1
paper towels: 5
eggs: 0
toilet paper: 0
clorox wipes: 0
milk: 2
```

当我再次执行循环时，我得到:

```
clorox wipes: 0
milk: 2
hand sanitizer: 1
paper towels: 5
```

很明显，地图中的项目没有按照任何顺序存储。这是因为排序是随机的。如果你想看到你的地图以某种顺序显示，比如按字母顺序，你必须首先对关键字进行排序，然后显示地图中的项目。有一个内置的 `sort`功能，它是`sort`包的一部分。以下是您的操作方法:

```
package mainimport (
  "fmt"
  "sort"
)func main() {
  inventory := map[string] int {
    "toilet paper": 0,
  }
  inventory["clorox wipes"] = 0
  inventory["milk"] = 2
  inventory["hand sanitizer"] = 1
  inventory["paper towels"] = 5
  inventory["eggs"] = 0
  var items []string
  for item := range inventory {
    items = append(items, item)
  }
  sort.Strings(items)
  for _, item := range items { 
    fmt.Printf("%s: %d\n", item, inventory[item])
  }
}
```

第一步是创建一个片来存储库存项目。下一步是遍历地图，将每个项目键添加到切片中。接下来，使用`sort.Strings`功能对切片进行排序。最后，遍历切片，使用每个商品名称作为键来返回它在库存图中的相关计数。

以下是该程序的输出:

```
clorox wipes: 0
eggs: 0
hand sanitizer: 1
milk: 2
paper towels: 5
toilet paper: 0
```

映射的键是唯一的，因此如果您尝试插入与现有键具有相同键的键/值对，新键将替换现有键。这里有一个例子:

```
func main() {
  inventory := map[string] int {
    "toilet paper": 0,
  }
  inventory["clorox wipes"] = 0
  inventory["milk"] = 2
  inventory["milk"] = 5
  for item, count := range inventory {
    fmt.Printf("%s: %d\n", item, count)
  }
}
```

milk 的最新键/值对被插入到映射中，以便库存列表显示 milk 的计数为 5 而不是 2。

您可以使用内置的`len`函数来查找映射中键/值对的数量:

```
fmt.Printf("There are %d items in inventory.\n", len(inventory))
```

您可以使用内置的`delete`函数删除映射键/值对:

```
delete(inventory, "eggs")
```

零映射是已经声明但尚未用键/值对初始化的映射。以下代码片段返回 true，因为地图中没有任何数据:

```
var numbers map[string] string
fmt.Print(numbers == nil)
```

您可以在 nil 映射上调用`len` 和`delete`函数，因为这些函数将返回所声明映射的值类型的默认值。换句话说，如果值类型是`string`，在 nil 映射上调用`len`或`delete`将返回一个空字符串。

下面是一个使用零映射的例子，它的值类型是`int`:

```
var numbers map[string] int
val := len(numbers)
fmt.Print(val) // displays 0
```

当您试图访问一个不存在的键时，这也是有效的。Go 中一个常见的习语是这样编写键访问:

```
func main() {
  inventory := map[string] int {
    "toilet paper": 0,
  }
  inventory["clorox wipes"] = 0
  inventory["milk"] = 2
  key := "eggs"
  count, ok := inventory[key]
  if !ok {
    fmt.Printf("%s not found in inventory.", key)
  } else {
    fmt.Printf("There are %d %s in inventory.\n", count, key)
  }
}
```

# 切片作为贴图值

在很多应用程序中，您会希望使用切片来表示地图的值。下面的程序演示了一种使用切片来存储与学生相关的一组成绩的方法:

```
func main() {
  grades := map[string] []int {
    "Joey": {81, 77, 83},
    "Audie": {91, 100, 88},
    "Bill": {77, 81, 72},
  }
  total := 0
  allGrades := grades["Joey"]
  for _, aGrade := range allGrades {
    total += aGrade
  }
  average := float64(total) / float64(len(allGrades))
  fmt.Printf("Joey's grade average is %.2f\n", average)
}
```

为了计算平均成绩，我们访问 Joey 的成绩，并将其存储在一个新的切片中。然后，我们使用新切片来计算平均分数。

# 去找地图

该映射为程序员提供了轻松创建数据结构的机会，这些数据结构可用于存储关联数据的应用程序。虽然我在本文中没有涉及到它，但是地图也可以用于实现一组数据结构，它存储唯一的数据元素。如果你对此感兴趣，请参阅 Alan Donovan 和 Brian Kernighan 的《Go 编程语言》。

感谢您阅读这篇文章，如果您有任何意见和建议，请发邮件给我。