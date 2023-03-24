# 学习 Go:数组和两个数组处理模板

> 原文：<https://levelup.gitconnected.com/learning-go-arrays-and-two-array-processing-templates-65a33afb577b>

![](img/f8998d0e24f3d75efd6d1b4c12ad299c.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Mazhar Zandsalimi](https://unsplash.com/@m47h4r?utm_source=medium&utm_medium=referral) 拍摄的照片

在本文中，我将讨论如何在 Go 中使用数组。数组不是大多数 Go 程序员选择的数据结构；切片是。但是为了理解如何使用切片，我们必须理解如何使用数组，所以本文将演示如何创建和使用数组。

同时，我将向您展示两个与数组相关的编程模板— *填充数组*和*处理数组*的每个元素。

# 声明 Go 数组

像大多数其他编程语言中的数组一样，Go 数组是相同类型的固定长度数据序列。使用下标符号访问数组元素，Go 数组的索引从 0 开始，经过比数组长度小 1 的位置。

Go 中的数组使用以下语法模板声明:

*var array-name[number-of-elements]data-type
var array-name[number-of-elements]data-type { initializer-list }
var array-name[…]data-type { initializer-list }*

最后一个语法模板表明，当你使用初始化列表时，你可以在括号中放一个省略号，编译器将通过计算初始化列表中的元素来决定数组的大小。

下面是一些声明数组的代码示例:

```
var numbers[10] int
var floats[20] float64
var names[…] string{"Cynthia", "Jonathan", "Danny", "Raymond"}
grades[3] := int{81, 77, 92}
```

# 使用填充数组模板填充数组

如果数组没有用初始化列表声明，你必须用数据填充数组。*填充数组*模板可以帮助完成这项任务。以下是模板:

*将数组索引变量设置为 0
当有更多输入时，执行以下操作:
读取一个值
将该值存储在索引数组位置
将索引变量增加 1*

填充一个数组最简单的方法是用一个初始化列表，就像我在上一节中展示的那样。但是，对于大量的数据或者如果您希望交互式地输入数据，这是不切实际的。让我们首先看看如何通过用户输入来填充数组。

下面是一个程序，它声明一个由 5 个整数组成的数组，并提示用户输入数组元素:

```
func main() {
  const size = 5
  var numbers[size] int
  var num int
  for i := 0; i < size; i++ {
    fmt.Print("Enter number: ")
    fmt.Scan(&num)
    numbers[i] = num
  }
}
```

我喜欢用数据填充数组的方法之一是使用随机数。这比手动输入数据要容易得多，尤其是当数组元素只是测试程序其他部分所需的虚拟值时。

下面是填充数组的另一个示例，这次使用随机数生成:

```
func main() {
  const size = 5
  var numbers[size] int
  seed := rand.NewSource(time.Now().Unix())
  rng := rand.New(seed)
  for i := 0; i < size; i++ {
    numbers[i] = rng.Intn(100)
  }
  fmt.Print(numbers)
}
```

最后一行演示了我最喜欢的 Go 特性之一——打印数组而不遍历它的能力，就像在 Python 和 JavaScript 等语言中一样。

# 处理一维数组的每个元素

一旦用数据填充了数组，就可以在执行某些操作时访问元素了。最常见的方法是使用一个`for`循环，通过索引访问每个数组元素。

该模板的伪代码如下所示:

*用于将索引设置为 0；在最后一个元素处停止；将 index 增加 1:
对数组元素[index]做些什么*

下面是一个程序，它创建一个由 10 个整数(随机生成)组成的数组，然后对数组元素求和，计算并显示数组元素的平均值:

```
func main() {
  const size = 10
  var numbers[size] int
  seed := rand.NewSource(time.Now().Unix())
  rng := rand.New(seed)
  for i := 0; i < size; i++ {
    numbers[i] = rng.Intn(100)
  }
  fmt.Println(numbers)
  var total int
  for i := 0; i < size; i++ {
    total += numbers[i]
  }
  average := float64(total) / float64(size)
  fmt.Printf("The average value is %.2f.", average)
}
```

使用基于索引的循环的问题是，您可能会意外地超出数组的界限，这将导致 go 中出现混乱(错误)。对此的解决方案是使用一个`range for`循环来访问数组元素。

在我们讨论这种循环类型之前，让我们检查一下带有范围类型循环的模板的伪代码:

*对于数组的每个元素:
对元素做一些事情*

`range for`循环用于迭代一定范围的值，比如一个数组的所有元素，这样就不会意外地试图访问数组末尾以外的值。`range for`循环的语法模板如下所示:

*对于索引值，变量:=范围数据结构{
语句
}*

您会注意到`range for`循环返回两个值，每个数组元素的索引和数组元素本身。如果需要，可以使用索引值，但通常不需要，所以可以使用下划线字符将该值丢弃。

让我们修改上面的程序，使用一个`range for`循环来访问数组元素。代码如下:

```
func main() {
  const size = 10
  var numbers[size] int
  seed := rand.NewSource(time.Now().Unix())
  rng := rand.New(seed)
  for i := 0; i < size; i++ {
    numbers[i] = rng.Intn(100)
  }
  fmt.Println(numbers)
  var total int
  for _, number := range numbers {
    total += number
  }
  average := float64(total) / float64(size)
  fmt.Printf("The average value is %.2f.", average)
}
```

Go 中的`range for`就像 C++中的`range for` 和 JavaScript 中的`for-of`循环。大多数编程专家现在建议，除非有某种原因需要使用索引`for`循环来访问数组，否则应该使用`range for`循环来访问数组，以避免程序中出现越界错误。

# 处理数组所有元素的建议

我已经展示了两个模板和两种处理数组所有元素的技术。我还提到使用一个`range for`循环来处理一个数组的所有元素是实现这个模板的首选方法。在他的书《有效的 Java》中，Joshua Bloch 建议他的读者避免使用索引循环。他演示了在一个典型的索引 `for`循环中，索引变量是如何被多次使用的，每次都有出错的机会。他还提到了索引变量是如何弄乱你的代码的。另一方面,`range for`循环清理了混乱，消除了出错的可能性，因为根本没有使用索引变量。

我们都应该听从布洛赫的建议，当`range for`循环同样有效时，停止使用索引`for` 循环。

感谢您阅读这篇文章，如果您有任何意见或建议，请发邮件给我。