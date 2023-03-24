# 学习围棋:结构

> 原文：<https://levelup.gitconnected.com/learning-go-structs-ca4074dff40d>

![](img/8c332df7e98a25564e909731a64d994e.png)

由 [William Daigneault](https://unsplash.com/@williamdaigneault?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

结构(也称为结构)是一种聚合数据类型，允许您构建包含描述该类型的多个变量(字段)的用户定义类型。一个结构可以被复制到另一个结构，用作函数的参数和自变量，存储在数组中，并像使用内置类型一样使用。

# 创建和使用结构

定义结构的语法模板是:

*类型结构名称结构{
字段列表
}*

让我们看一个例子。我们可以使用以下声明将几何点定义为结构:

```
type Point struct {
  x, y int
}
```

在这里，我定义了一个`Point`结构来包含两个`int`字段，`x`和`y`。

一旦定义了一个结构，就可以像这样声明`struct`类型的实体:

```
var p1 Point
```

使用点运算符访问结构的字段:

```
p1.x = 1
p1.y = 2
fmt.Printf("x: %d, y: %d\n", p1.x, p1.y)
```

您可以直接打印结构，而不必像我上面所做的那样访问它的字段:

```
fmt.Println(p1)
```

创建结构的另一种方法是使用结构文本:

```
p1 := Point{1,2}
p2 := Point{2,3}
```

如果结构的所有字段都是可比较的，则可以直接比较两个结构对象。例如:

```
type Point struct {
  x, y int
}func main() {
  p1 := Point{1,2}
  p2 := Point{2,3}
  if p1 == p2 {
    fmt.Println("p1 and p2 are equal.")
  } else {
    fmt.Println("p1 and p2 are not equal.")
  }
}
```

可以将一个结构复制到另一个结构，生成的副本是深度副本，如下面的程序所示:

```
type Point struct {
  x, y int
}func main() {
  p1 := Point{1,2}
  p2 := p1
  fmt.Println(p1) // {1 2}
  fmt.Println(p2) // {1 2}
  p1.x = 3
  fmt.Println(p1) // {3 2}
  fmt.Println(p2) // {1 2}
}
```

# 结构和复合类型字段

结构可以包含复合数据类型作为字段。下面是一个`Student` 结构，它有一个存储测试成绩的片:

```
type Student struct {
  name string
  major string
  grades []int
}func main() {
  var st1 Student
  st1.name = "Jane Doe"
  st1.major = "History"
  st1.grades = append(st1.grades, 81)
  st1.grades = append(st1.grades, 93)
  st1.grades = append(st1.grades, 88)
  fmt.Println(st1)
}
```

您还可以使用 struct 文本创建一个 Student 对象，甚至可以使用 slice，如下所示:

```
st1 := Student{"Jane Doe", "History", []int {81, 90, 88}}
```

除了拥有像字段这样的复合类型之外，结构还可以拥有其他结构作为其字段的一部分。下面是一个有两个`Point` 结构作为其字段的`Line`结构的例子:

```
type Point struct {
  x, y int
}type Line struct {
  p1 Point
  p2 Point
}func main() {
  var line1 Line
  line1.p1.x = 3
  line1.p1.y = 4
  line1.p2.x = 5
  line1.p2.y = 8
  fmt.Println(line1)
}
```

# 结构和函数

结构可以作为参数传递给函数，也可以是函数的返回值。让我们先看看作为返回值的结构。

下面是一个函数，它通过接受点的 x 和 y 坐标作为参数来生成一个`Point`:

```
type Point struct {
  x, y int
}func makePoint(x, y int) Point {
  var p Point
  p.x = x
  p.y = y
  return p
}func main() {
  p1 := makePoint(1,2)
  fmt.Println(p1)
}
```

结构也可以是函数参数。在下面的示例中，`computeAvg`函数将一个`Student`结构作为参数，并返回学生的平均测试分数:

```
type Student struct {
  name string
  major string
  grades []int
}func computeAvg(st Student) float64 {
  total := 0
  for _, grade := range st.grades {
    total += grade
  }
  return float64(total) / float64(len(st.grades))
}func main() {
  var st1 Student
  st1.name = "Jane Doe"
  st1.major = "History"
  st1.grades = append(st1.grades, 81)
  st1.grades = append(st1.grades, 93)
  st1.grades = append(st1.grades, 88)
  average := computeAvg(st1)
  fmt.Printf("%s's grade average is %.2f.\n", st1.name, average)
}
```

通常，为了使程序更高效，指向结构的指针被传递给函数。我们只需要对上面的程序做两个改变。首先，我们需要创建一个指向`Student`结构的指针，然后我们需要在函数定义中引用该指针。

以下是新程序:

```
func computeAvg(st *Student) float64 {
  total := 0
  for _, grade := range (*st).grades {
    total += grade
  }
  return float64(total) / float64(len(st.grades))
}func main() {
  var st1 Student
  st1.name = "Jane Doe"
  st1.major = "History"
  st1.grades = append(st1.grades, 81)
  st1.grades = append(st1.grades, 93)
  st1.grades = append(st1.grades, 88)
  pst1 := &st1
  average := computeAvg(pst1)
  fmt.Printf("%s's grade average is %.2f.\n", st1.name, average)
}
```

我也可以从一开始就使`st1`成为一个`Student`指针变量，而不是从`st1`创建一个指针。

注意函数定义中的 `range for`。在使用点运算符获取`grades`字段之前，必须对`Student`结构取消引用，这是通过将取消引用放在括号中来实现的，以便在访问该字段之前执行该操作。如果你不这样做，你会引起恐慌。

# 为什么使用结构

使用结构的最明显原因是当数据类型包含多个字段时。尽管如此，有三个原因可以解释为什么使用结构会帮助你的代码。我从史蒂夫·麦康奈尔(Steve McConnell)的优秀著作《代码完成(T7)》中汲取了这些原因。

第一个原因是结构提供了数据关系的清晰性。当您跟踪几何点时，有一个具有 x 和 y 域的实体是有意义的，而不是单独的 x 和 y 点，它们不能相互关联。

第二个原因是结构简化了对数据块的操作。如果您的程序涉及员工数据，将所有员工数据保存在一个实体中比每条数据都有单独的变量更有意义。

最后一个原因是结构通过使函数参数列表更短来简化它们。如果您将有关学生的数据(从本文前面定义的学生结构)传递给一个函数，那么将数据作为一个实体而不是作为单独的数据传递要简单得多。使用结构时，参数列表可以从三个或更多个参数减少到一个。

出于所有这些原因，当您在解决编程问题时可以将数据包装到结构中时，您应该考虑这样做。

感谢阅读，并请通过电子邮件发送您的评论和建议。