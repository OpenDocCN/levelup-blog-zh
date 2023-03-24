# 学习围棋:切片和其他任务

> 原文：<https://levelup.gitconnected.com/learning-go-slicing-slices-and-other-tasks-cdd3a089860f>

![](img/aed32e7fb334e0af66dd75c7fbf4ccea.png)

比安卡·阿克曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

在上一篇文章中，我向您介绍了切片数据结构，这是一种相同类型的未绑定数据序列。在本文中，我将向您展示如何从切片创建数据切片。

# 切片运算符

slice 运算符用于返回切片的子集。给定切片 s，切片运算符为:

*s【开始:停止】*

其中 *start* 是您想要用于子集的底层数组的第一个位置，而 *stop* 标记您想要停止子集的位置之后的一个元素。

让我们看一些切片操作符如何工作的例子:

```
func main() {
  letters := []string{"a","b","c","d","e"}
  firstThree := letters[0:3]
  fmt.Println(firstThree) // displays [a b c]
  twoThree := letters[1:3]
  fmt.Println(twoThree) // displays [b c]
  firstOne := letters[:1]
  fmt.Println(firstOne) // displays [a]
  lastOne := letters[len(letters)-1 :]
  fmt.Println(lastOne) // displays [e]
  all := letters[:]
  fmt.Println(all) // displays [a b c d e]
}
```

# 使用切片运算符

在这一节中，我将演示如何使用 slice 操作符编写一些有用的函数。

我将创建的第一个函数是`middle`。此函数将切片作为参数，并返回切片的中间元素，删除第一个和最后一个元素。下面是函数定义和一个测试程序:

```
func middle(s []string) []string {
  return s[1:len(s)-1]
}func main() {
  letters := []string{"a","b","c"}
  fmt.Println(middle(letters))
  words := []string{"now","is","the","time","for","all","good"}
  fmt.Println(middle(words))
}
```

接下来我要写的两个函数是`chopFront`和`chopBack`，它们分别取出切片的第一个元素和最后一个元素。下面是函数定义和一个测试程序:

```
func chopFront(s []string) []string {
  return s[1:]
}func chopBack(s []string) []string {
  return s[0:len(s)-1]
}func main() {
  letters := []string{"a","b","c"}
  fmt.Println(chopFront(letters))
  fmt.Println(chopBack(letters))
}
```

最后，下面是一个函数，它根据作为参数提供给该函数的开始和停止参数返回一个自定义切片:

```
func mySlice(s []string, start int, stop int) []string {
  return s[start:stop]
}func main() {
  letters := []string{"a","b","c","d","e","f"}
  fmt.Println(mySlice(letters, 2, 4))
}
```

# 从切片中移除元素

没有用于从切片中移除元素的内置函数。我们将不得不自己编写这个函数。您不能就地从切片中移除项目，因此您必须通过将切片的两个部分(移除项目之前的部分和移除项目之后的部分)附加在一起并返回附加的切片来完成。

下面是一个定义和一个测试程序:

```
func removeItem(s []string, index int) []string {
  return append(s[:index], s[index+1:]...)
}func main() {
  letters := []string{"a","b","c","d","e","f"}
  letters = removeItem(letters, 2)
  fmt.Println(letters)
}
```

return 语句末尾的省略号表示将返回切片中剩余的内容。

# 零切片

没有任何数据的切片是一个*零*切片:

```
func main() {
  var numbers []int
  fmt.Println(numbers == nil) // displays true
}
```

试图拿走一部分会导致恐慌:

```
func main() {
  var numbers []int
  fmt.Print(numbers[0:1]) // causes panic
}
```

此死机的输出如下所示:

```
panic: runtime error: slice bounds out of range [:1] with capacity 0goroutine 1 [running]:
main.main()
c:/go/bin/test.go:8 +0x93
exit status 2
```

因此，如果有可能出现零切片，您应该在对其执行操作之前测试该切片。

# 切片然后走

切片是一种非常强大的数据结构，也是在 Go 中存储顺序数据的首选方式。对于大多数应用程序，您应该更喜欢使用切片而不是数组。切片的大小不固定，比数组更灵活。您始终可以从数组创建切片，或者如果数据尚未存储在数组中，也可以从一个新的数组开始。编程专家多年来一直建议使用动态数据结构，比如 slice，而不是使用数组，你应该听从他们的建议。

感谢您的阅读，请将您的意见和建议通过电子邮件发送给我。