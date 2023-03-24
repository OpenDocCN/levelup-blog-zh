# 在围棋中控制程序流程

> 原文：<https://levelup.gitconnected.com/controlling-program-flow-in-go-860b241b6f4e>

获得关于条件句、循环和格的工作知识。

![](img/4d856f388c9c91e2808921871ea27a96.png)

Go 对控制你的程序流程有独特的见解。事实上，不存在 do 循环和 while 循环的组合。那是因为 Go *专用于*循环！然而，这并不妨碍开发人员创建灵活的条件来执行。如果有的话，它是开发人员的控制流工具箱的一个强大的简化。

## 强大的 For 循环体验

让我们从 Go 中各种 for 循环控件的一些例子开始。打印出一些数字的经典例子如下:

```
package mainimport "fmt"func main() {
    var i int
    for i < 10 {
        fmt.Println(i)
        i++
    }
}
```

这里发生了一些事情。首先我们将`i`初始化为`int`，然后我们创建一个 for 循环来检查`i`是否小于 10。这个程序将输出 0 到 9，因为 0 是一个 int 的缺省值。还要注意`i`的增量不是内置的，我们必须自己做。在某种程度上，这个基本的 for 循环几乎就像一个 if 语句本身！

如果我们想让**早点脱离**循环，我们可以简单地在循环中添加一个 **if 语句**。

```
for i < 10 {
    fmt.Println(i)
    if i == 5 {
        break
    }
}
```

你也可以使用`continue`语句，如果你猜对了，你只是想继续你的程序。

```
for i < 10 {
    fmt.Println(i)
    i++
    if i == 5 {
        fmt.Println("continuing...")
        continue
    }
}
```

我们可以很容易地将`i`的增量转移到 for **循环条件语句中。**

```
var i int
for i < 10; i++ {
    fmt.Println(i)
}
```

在我们想要定义`i`到与循环条件相同的行上的某个值的情况下，我们可以做下面的隐式初始化。看看这和 C 语言中的 for 循环多么相似！

```
for i := 0; i < 10; i++ {
    fmt.Println(i)
}
```

如果你想要一个无限的 for 循环，不要在条件循环中定义任何东西。

```
for {
    fmt.Println("I'm printing for eternity!")
}
```

对一个**数组**或**切片**的循环可以通过难看的索引或使用`range`关键字来取回索引和值来完成。

```
kimboSlice := []string{"BAM!", "BOOM!", "POW!"}// Gross! Don't do this if you can help it!
for i := 0; i < len(kimboSlice); i++ {
    fmt.Println(kimboSlice[i])
}// Much better, we get the index and value back.
for idx, val:= range kimboSlice {
    fmt.Println(idx, val)
}/* Output of the range for loop
0 BAM!
1 BOOM!
2 POW!
*/0 BAM!
1 BOOM!
2 POW!
```

同样的模式也适用于**映射**，但是您将循环遍历键和值，而不是索引和值。

```
kimboSlice := map[string]string{
    "Jab":"POP!",
    "Hook":"BAM!", 
    "Uppercut":"POW!",
}for key, val := range kimboSlice {
    fmt.Println(key, val)
}/* Output
Jab POP!
Hook BAM!
Uppercut POW!
*/
```

在对映射进行迭代时，也可以只获取键或值，尽管只获取值是通过忽略返回的键来实现的。

```
// Print out just the keys of a map
for key := range someMap {
    fmt.Println(key)
}// Ignore the key, just get the values
for _, val := range someMap {
    fmt.Println(val)
}
```

有点笨拙，但是`_`特殊字符意味着你不关心这个变量，也不需要引用或存储它。

**If 语句**可用于检查等式和某些条件。您可以添加一个`else if`来检查其他情况。Go 中的 if-statement conditional 在定义上是基本的，所以这里有一点英语语法可以让事情变得复杂。

```
if "similar" == "the same" {
     fmt.Println("similar is the same as the same")
} else {
     fmt.Println("similar is not the same, but it is similar")
}
```

Go 再次强制执行该语句的语法。您不能在下面一行写 else 语句，因为那会抛出一个错误。不管你喜不喜欢，Go 保持了代码库的统一，在我看来这是一个巨大的好处。

**转换陈述**是当你有一大堆你可能陷入的情况时。根据具体情况，它们可以帮助提高代码的可读性。以下是如何用 Go 和 switch 语句检查一天的情况。

```
switch time.Now().Weekday() {
    case time.Saturday:
        fmt.Println("Today is Saturday.")
    case time.Sunday:
        fmt.Println("Today is Sunday.")
    default:
        fmt.Println("Today is a weekday.")
}
```

总之，Go 有各种方法使用 for 循环、if 语句和 switch 语句来控制程序流。如果你喜欢你所读到的，或者想听到更多关于这些工具的信息，请在下面留下你的评论！感谢阅读。