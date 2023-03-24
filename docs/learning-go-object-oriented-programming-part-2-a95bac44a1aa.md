# 学习 Go:面向对象编程第 2 部分

> 原文：<https://levelup.gitconnected.com/learning-go-object-oriented-programming-part-2-a95bac44a1aa>

![](img/3eb5c4a3e95b70fe28bfb173dc68e35c.png)

[Geronimo Giqueaux](https://unsplash.com/@ggiqueaux?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在之前的[文章](/learning-go-object-oriented-programming-part-1-e8da5c91488e)中，我讨论了如何用 Go 完成面向对象编程，即使 Go 没有类。在本文中，我将通过演示如何封装 Go 结构的数据和方法来继续讨论 Go 中的 OOP。在文章的第二部分，我将讨论 OOP 的另一个方面，通过创建 Go 接口来创建抽象类型。

# 什么是封装？

OOP 的主要目标之一是封装或数据隐藏。为什么这很重要？如果没有数据隐藏，可以直接访问结构的字段，并且可以将该字段的数据类型合法的任何值赋给该字段。例如，给定这个结构定义:

```
type Person struct {
  name string
  age int
}
```

根据这个定义，我们可以将任何有效的整数分配给一个人的年龄:

`p1 := Person{“Jane Doe”, 823}`

哇哦。除非我们生活在圣经时代，否则人们不可能活到 823 岁。但是一个整型变量最有可能容纳 823。显然，我们需要一些过程来控制如何访问结构的字段。

大多数 OOP 编程语言都提供了访问机制，通过使字段具有私有访问权限来隐藏类的字段，这意味着该字段只能在类定义中被访问。Go 没有这个，所以我们需要找到另一种方法。

另一种方法是通过包名导出。

# 如何将软件包导出名称

创建包时，您可以控制在导入包时公开哪些函数和其他实体。要做到这一点，可以将想要导出到导入程序的实体的名称大写。我在这篇文章中没有足够的篇幅来讨论如何创建包，但是这篇文章做得很好。

为了进行演示，下面是导出用于处理几何点的功能的包的定义:

```
package pointtype Point struct {
  x, y float64
}func (p *Point) Create(newx, newy float64) {
  p.x = newx
  p.y = newy
}func (p *Point) X() float64 {
  return p.x
}func (p *Point) Y() float64 {
  return p.y
}func (p *Point) MoveTo(newx, newy float64) {
  p.x = newx
  p.y = newy
}
```

我们来看看这个定义。这些字段在结构定义中声明。像构造函数一样使用方法`Create`来初始化一个新的`Point`结构。方法`X()`和`Y()`类似于 Java 等语言中的 getters，用于返回存储在`x`和`y`字段中的值。

在这个定义中，您应该注意到所有的方法名都是大写的，这意味着它们被导出到一个导入程序中，但是字段名不是大写的。因此，您不能直接访问这些字段，尝试这样做会导致死机。

如果您熟悉其他 OOP 语言，这可能看起来是实现封装的一种激进方式，但是使用包也提供了另一个理由，通过拆分大多数结构定义使您的程序更加模块化。

# Go 接口

传统上，接口是由一组方法组成的类，这些方法必须由实现接口的任何子类(或派生类)来实现。围棋界面与传统界面相似，但有一些不同，在这一节中，我将讨论如何在我们的围棋程序中使用它们。

为了演示接口在 Go 中是如何工作的，我将开发一组实现几何形状的结构(尽管没有实际的图形)。这些形状将基于一个接口`shape`，它命名了一个形状应该具有的行为类型，但没有明确定义这些行为。

我将首先定义接口`shape`。作为接口，它是抽象的，不能直接实现。但是它可以用作定义特定类型形状的基础。定义如下:

```
type shape interface {
  X() float64
  Y() float64
  Draw()
  MoveTo(newx, newy float64)
  Radius() float64
}
```

Go 接口不能指定字段，但是它可以并且必须指定实现该接口的任何结构都必须实现的方法。

现在，我们可以将特定的形状定义为一个结构:

```
type circle struct {
  x, y float64
  radius float64
}
```

该结构定义存储该类型数据的字段。现在，我们可以通过创建一个实现接口的实体来将两者结合起来:

```
var circ shape
circ = Circle(1,2,5)
```

现在你可以看到一个接口是如何实现的。首先声明一个接口类型的变量。然后，通过调用 struct 文本为该变量赋值，以完成实现。

这段代码是乱序的，因为我仍然需要定义接口中指定的所有方法。这些定义如下:

```
func (c circle) X() float64 {
  return c.x
}func (c circle) Y() float64 {
  return c.y
}func (c circle) Radius() float64 {
  return c.radius
}func (c circle) MoveTo(newx, newy float64) {
  c.x = newx
  c.y = newy
}func (c circle) Draw() {
  fmt.Printf("Drawing a circle at %.0f,
             %.0f with radius of %.0f.",
             c.X(), c.Y(), c.Radius())
}
```

这些方法定义看起来就像是从一个结构定义构建代码一样，但是如果没有定义接口中指定的所有方法，就会有一些问题。

下面是一个例子，说明如果我们注释掉`Radius`函数定义会发生什么:

```
c:\Go\bin\shapes>go run shapeprog.go
# command-line-arguments
.\shapeprog.go:37:29: c.Radius undefined (type circle has no field or method Radius, but does have radius)
.\shapeprog.go:42:8: cannot use circle literal (type circle) as type shape in assignment:
circle does not implement shape (missing Radius method)
```

恐慌的最后一行是最有教育意义的。它告诉我，我的`circle` 结构没有实现接口，因为没有定义`Radius`方法。这强化了接口是一个契约的观点，即任何选择实现接口的结构都必须定义接口中指定的所有方法。

现在，我将把所有这些代码合并到一个程序中，以便更容易地看到发生了什么:

```
package mainimport "fmt"type shape interface {
  X() float64
  Y() float64
  Draw()
  MoveTo(newx, newy float64)
  Radius() float64
}type circle struct {
  x, y float64
  radius float64
}func (c circle) X() float64 {
  return c.x
}func (c circle) Y() float64 {
  return c.y
}func (c circle) Radius() float64 {
  return c.radius
}func (c circle) MoveTo(newx, newy float64) {
  c.x = newx
  c.y = newy
}func (c circle) Draw() {
  fmt.Printf("Drawing a circle at %.0f,
             %.0f with radius of %.0f.",
             c.X(), c.Y(), c.Radius())
}func main() {
  var circ shape
  circ = circle{1,2,5}
  fmt.Println("circle radius: ",circ.Radius())
  circ.Draw()
}
```

我强烈建议您将我的例子扩展到其他形状，以便更好地理解界面在 Go 中是如何工作的。

# 去吧

在本文中，我展示了两个重要的 OOP 技术，数据封装和接口实现，以及如何在 Go 中执行这些技术。虽然 Go 不是一种严格的面向对象编程语言，但它至少提供了编写基于对象的代码的机制，而且在我看来，这种语言正在引领未来编程语言的设计方式以及未来计算机程序的编写方式。

感谢您阅读这篇文章，请给我发电子邮件，提出您的意见和建议。