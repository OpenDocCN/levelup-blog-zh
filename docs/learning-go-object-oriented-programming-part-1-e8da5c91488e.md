# 学习 Go:面向对象编程第 1 部分

> 原文：<https://levelup.gitconnected.com/learning-go-object-oriented-programming-part-1-e8da5c91488e>

![](img/7e24bce5462a5f9fae6b45b6b0afbd01.png)

图为[托德·夸肯布什](https://unsplash.com/@toddquackenbush?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

每一种现代计算机编程语言，或者至少是自 20 世纪 80 年代中期以来开发的语言，都提供了一些执行面向对象编程(OOP)的方法。虽然这些类中有许多是通过类来实现这一点的，但在 Go OOP 中，是通过定义在结构范围内调用的结构和方法的组合来实现的。在这篇文章中，我将介绍 OOP 是如何在 Go 中执行的，包括定义方法和组合的基础知识。

# 结构的快速概述

OOP 是通过封装数据和行为实现的。在许多编程语言中，完成封装的方法是创建一个类。围棋没有课。相反，在 Go 中封装数据的方法是使用 struct。如果你想了解更多关于结构的知识，这里的[是我的关于在 Go 中使用结构的文章的链接。](/learning-go-structs-ca4074dff40d)

在这里，我将通过演示如何创建一个结构来跟踪几何点来提供一个简要的概述:

```
type Point struct {
  x, y int
}
```

我们可以使用结构文字来定义结构:

```
p1 := Point{1,2}
```

我们可以使用点符号来访问结构中的数据:

```
fmt.Printf("x: %d, y: %d\n", p1.x, p1.y)
```

# 用方法定义对象行为

上一节演示了我们如何在对象中定义和使用数据。下一步是定义一个对象的行为，我们用*方法*来完成。

方法是一个函数，它被定义为由一个结构*接收*。方法定义看起来像函数定义，但是它包括一个额外的参数来引用作为方法定义的接收者的结构。

例如，下面是一种计算两点之间距离的方法:

```
package mainimport (
  "fmt"
  "math"
)type Point struct {
  x, y float64
}func (p1 Point) Distance(p2 Point) float64 {
  return math.Sqrt(math.Pow((p2.x-p1.x),2) +
                   math.Pow((p2.y-p1.y),2))
}func main() {
  p1 := Point{3,5}
  p2 := Point{1,2}
  dist := p1.Distance(p2)
  fmt.Println(p1)
  fmt.Println(p2)
  fmt.Printf("Distance between the two points is %.2f.\n", dist)
}
```

当我们用 struct 调用方法时，我们调用的是一个*选择器*，这个表达式被称为选择器表达式— `p1.Distance(p2)`。

让我们看另一个使用指针的例子。如果我们想写一个直接修改`Point`的函数，或者，正如我之前讨论结构时所说的，我们想有效地将结构传递给函数，我们希望`Point` 参数是一个指针。这里有一个函数和一个程序来完成这个任务，将一个点从一个地方移动到另一个地方:

```
func (p *Point) MoveTo(x, y float64) {
  p.x = x
  p.y = y
}func main() {
  p1 := Point{3,5}
  fmt.Println(p1) // {3, 5}
  pp1 := &p1
  pp1.MoveTo(1,2)
  fmt.Println(*pp1) // {1, 2}
  fmt.Println(p1) // {1, 2}
}
```

为了提高程序的效率，应该始终将结构作为指针传递给函数。

# 通过结构嵌入构建对象

Go 语言没有继承，但是创建复杂类型的一种方法是通过结构嵌入。这只是意味着通过在定义中嵌入其他结构类型来构建一个类型。

通过嵌入其他类型来构建类型模型*有-a* 关系。例如，汽车“有”方向盘；一个人“有”名字；一个形状“有”一个原点。这种类型构建被称为*组合*，是 Go 使用继承从其他类型创建类型的答案。

对于我们的第一个复合示例，我们将通过在其中嵌入一个`Point` 类型来构建一个圆形类型。代码如下:

```
type Point struct {
  x, y float64
}type Circle struct {
  origin Point
  radius float64
}func main() {
  var c1 Circle
  c1.origin = Point{1,2}
  c1.radius = 5
  fmt.Println(c1)
}
```

熟悉 OOP 的人会注意到，这些结构的数据是公共的，可以直接访问。这意味着我们的数据没有被恰当地封装，至少没有按照正常的 OOP 标准封装。结果是 Go 在包级别封装了数据，我将在下一篇关于 Go 中 OOP 的文章中介绍如何实现这一点。

# Go 中的构造函数

Go 中没有构造函数这种东西，所以如果你想要一个构造函数，你必须通过一个方法创建一个，并把结构体作为接收者。下面是一个使用`Student`结构定义的例子:

```
type Student struct {
  name string
  id int
  major string
}func (st *Student) Create (stuName string, stuId int, 
                           stuMajor string) {
  st.name = stuName
  st.id = stuId
  st.major = stuMajor
}func main() {
  var student1 Student
  student1.Create("David Horton", 123, "History")
  fmt.Println(student1)
}
```

`Create`方法作为一个构造函数工作，因为它为`Student`结构的所有字段设置值。

# 涵盖的 Go OOP 特性摘要

下面是我们在这篇文章中学到的内容的总结。Go 不像其他 OOP 语言那样有类，所以你必须使用一个 struct。Go 允许您创建被称为方法的函数，可以从结构变量中调用这些函数。这些方法是用点运算符访问的，就像访问结构字段一样。Go 没有继承，但是您可以使用组合来创建将其他结构作为字段的结构。最后，你不能在 Go 中创建构造函数，但是你可以创建方法来执行与其他 OOP 语言中的构造函数相同的任务。

在我的下一篇文章中，我将介绍更多在 Go 中实现 OOP 的方法，通过包级封装和创建接口来实现继承。

感谢您的阅读，请给我发电子邮件提出意见和建议。