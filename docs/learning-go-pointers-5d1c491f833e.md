# 学习围棋:指针

> 原文：<https://levelup.gitconnected.com/learning-go-pointers-5d1c491f833e>

![](img/63bcb700a88b75ba8c5c3561eb6b0403.png)

照片由[法比安·吉斯克](https://unsplash.com/@fbngsk?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

*指针*是保存内存地址的变量。您可以使用指针间接访问和操作存储在变量中的数据。在这篇文章中，我将讨论如何在 Go 中使用指针。

# 创建指针

指针存储变量的地址。通过使用带有变量的地址运算符(`&`)创建指针。创建指针的语法模板是:

**ptr-name := &变量名*

下面是一个简单的程序，演示了如何声明、赋值和访问指针变量:

```
func main() {
  x := 1
  ptrX := &x
  fmt.Printf("Address of x: %x\n", ptrX)
  fmt.Printf("Value stored in x: %d\n", *ptrX)
}
```

这个程序的输出是:

```
Address of x: c0000100b0
Value stored in x: 1
```

当引用不带星号的指针变量时，访问内存地址，当使用`*`运算符将星号添加到指针变量的*解引用*时，访问内存地址中存储的值。

您可以通过更改指向变量的指针的值来修改该变量的值，如下例所示:

```
func main() {
  x := 1
  ptrX := &x
  fmt.Printf("X's current value: %d\n", x) // displays 1
  *ptrX++
  fmt.Printf("X's current value: %d\n", x) // displays 2
}
```

当然，如果你改变一个指针所指向的变量的值，指针也会随之改变:

```
func main() {
  x := 1
  ptrX := &x
  fmt.Printf("X's current value: %d\n", x) // displays 1
  *ptrX++
  fmt.Printf("X's current value: %d\n", x) // displays 2
  x = 100
  fmt.Printf("X's current value via pointer: %d\n", *ptrX)
  // 100 is displayed
}
```

如果你声明一个变量而没有给它分配地址，它的值为零:

```
func main() {
  var ptr *int
  fmt.Println("ptr's value is: ",ptr) // ptr's value is <nil>
}
```

一旦你给指针分配了一个地址，它的值就会变成那个地址。

创建指针的最后一种方法是使用关键字 new。new 的语法模板是:

*指针名称:=新(数据类型)*

下面是一个使用 new 创建指针的示例:

```
func main() {
  ptr := new(int)
  fmt.Println("ptr's value:",*ptr)
  *ptr = 14
  fmt.Println("ptr's value:",*ptr)
}
```

这个程序的输出是:

```
ptr's value: 0
ptr's value: 14
```

如您所见，当您使用 new 创建指针时，指针的值将被设置为指定数据类型的默认值。

# 使用指针

指针可以用来修改函数的参数，以避开 Go 的按值传递行为。这里有一个例子:

```
func addFive(val *int) {
  *val += 5
}func main() {
  x := 0
  ptrX := &x
  addFive(ptrX)
  fmt.Println("x's value:",x)
}
```

你可以用指针写一个函数来交换变量，就像这样:

```
func swap(val1 *int, val2 *int) {
  temp := *val1
  *val1 = *val2
  *val2 = temp
}func main() {
  x := 1
  y := 2
  fmt.Printf("x: %d, y: %d\n", x, y) // x: 1, y: 2
  swap(&x, &y)
  fmt.Printf("x: %d, y: %d\n", x, y)  // x: 2, y: 1
}
```

这是可行的，但是在 Go 中交换两个变量的惯用方法是这样的:

```
func swap(val1, val2 int) (int, int) {
  return val2, val1
}func main() {
  x := 1
  y := 2
  fmt.Printf("x: %d, y: %d\n", x, y)
  x, y = swap(x, y)
  fmt.Printf("x: %d, y: %d\n", x, y)
}
```

这是可行的，因为 Go 函数可以返回多个值。

您也可能想通过传递指向数组的指针来修改函数中的数组，如下例所示:

```
func curve(arr *[5]int, amount int) {
  for i:=0; i<5; i++ {
    (*arr)[i] += amount
  }
}func main() {
  const size = 5
  grades := [size]int{81, 77, 71, 83, 67}
  fmt.Println(grades)
  curve(&grades, 5)
  fmt.Println(grades)
}
```

这是可行的，但不是惯用的方法。相反，您应该将数组切片传递给函数，如下例所示:

```
func curve(arr []int, amount int) {
  for index, _ := range arr {
    arr[index] += amount
  }
}func main() {
  const size = 5
  grades := [size]int{81, 77, 71, 83, 67}
  fmt.Println(grades)
  curve(grades[:], 5)
  fmt.Println(grades)
}
```

指针在 Go 中的最后一个用途是当你使用结构体的时候。因为结构可以保存大量数据，所以结构应该作为指针传递给函数，这样它就不会通过值传递给函数。这里有一个例子:

```
type Student struct {
  name string
  id int
  major string
}func changeMajor(s *Student, m string)  {
  s.major = m
}func main() {
  st1 := &Student{"Jane Doe", 123, "Computer Science"}
  fmt.Println(*st1)
  changeMajor(st1, "Information Science")
  fmt.Println(*st1)
}
```

此示例还演示了如何使用 struct 文本创建指针变量。

正如我前面提到的，将结构作为指针传递给函数在 Go 中是惯用的，正如将对象作为引用传递在 C++中是惯用的效率问题，因为结构可以包含大量数据，并且通过值传递它们可能非常低效。

# Go 指针和数组

与 C 和 C++不同，Go 数组名不是指向数组的指针，因此不能使用指针作为访问数组的替代方法。另外，Go 没有指针算法，所以你不能在指针上使用递增和递减操作符。

# 去指点一下

Go 提供了使用指针间接访问变量值的能力。在某些情况下，指针会提高程序的效率，例如，当您将结构传递给函数时，您希望以最有效的方式来传递。Go 指针不如 C 和 C++等语言中的指针强大，但这可能是一个特性，而不是一个错误。

感谢您阅读这篇文章，请给我发电子邮件，提出您的意见和建议。