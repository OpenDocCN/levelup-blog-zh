# 围棋中的 4 个陷阱我仍然偶尔会陷入

> 原文：<https://levelup.gitconnected.com/4-pitfalls-in-go-i-still-occasionally-fall-into-7099eae3ecd1>

![](img/a7cf2945b0216946899b7c77779802ba.png)

在之前的[文章](/8-code-hacks-for-go-that-i-wish-id-known-when-i-started-56a6f4399acf)中， [I](https://www.linkedin.com/in/andrew-hayes-belfast/) 写了我多年来遇到的编程技巧。对于这篇文章，我决定写一些我也遇到过的陷阱。这些类型的错误会导致编译器会忽略的怪癖，并且在代码审查中很容易被遗漏。

# 1.引用循环变量不是一个好主意

这一个是相当众所周知的，但是它仍然不时地抓住我。如果您循环遍历值并创建一个指向循环变量的指针，可能不会得到您期望的结果。下面的代码是一个简单的函数，它接受一组用户并提取他们的 id:

```
package mainimport "fmt"type User struct {
 ID   int
 Name string
}func main() {
 users := []User{{1, "Andy"}, {2, "John"}, {3, "Jane"}} userIDs := getUserIDs(users)
  for _, id := range userIDs {
   fmt.Println(*id)
  }
}func getUserIDs(users []User) []*int {
 ids := make([]*int, 0, len(users))
 for _, user := range users {
  ids = append(ids, &user.ID)
 }
 return ids
}
```

这样的输出是:

```
3
3
3
```

这是围棋中循环方式的一个怪癖。当你引用一个循环变量时，它本身就是在引用数组中的值。这实际上意味着当你引用循环变量时，你最终引用了循环变量引用的最后一个值。简单的解决方案是更新循环，如下所示:

```
func getUserIDs(users []User) []*int {
 ids := make([]*int, 0, len(users))
 for _, user := range users {
  id := user.ID
  ids = append(ids, &id)
 }
 return ids
}
```

这样，它就获得了循环变量值的一个副本，并引用它。

# 2.当心影子变量！

影子变量不仅有一个很酷的名字，而且它们也会导致错误。当您在较低的范围内重用变量名时，就会出现这种情况。当您在 Go 中使用“:=”来声明一个变量时，您是在它周围的“{}”范围内声明它的。但是您可以在较低的范围内愉快地使用相同的变量名，例如在“if”语句中。在下面的代码示例中，您可以看到“importantVariable”在函数的顶层被设置为默认值。然后，代码进行一些检查，并更新“if”语句中的变量。然而，它最后输出的值并不完全是我们想要的，看看您是否能找出原因:

```
package mainimport (
 "fmt"
)func main() {
 importantVariableValue := getImportantVariableValue()
 fmt.Println(importantVariableValue)
}func getImportantVariableValue() string {
 importantVariable := "Default Value"
 if functionThatChecksShouldWeUseANoneDefaultValue() {
  importantVariable := "Non-Default Value"
  fmt.Printf("Important Variable updated to: %s\n", importantVariable)
 }
 return importantVariable
}func functionThatChecksShouldWeUseANoneDefaultValue() bool {
 return true
}
```

这将打印:

```
Important Variable updated to: Non-Default Value
Default Value
```

原因是“importantVariable”在 if 语句中被重新声明，在这一行:

```
importantVariable := "Non-Default Value"
```

这意味着当 if 语句结束时，它是一个被丢弃的全新变量。

这是一个简单的例子，所以问题很容易发现和解决。然而，在更复杂的代码中，这可能更难发现。由于影子变量导致的错误丢失也很常见，因为 Go 编码人员到处都使用变量名“err”。这可能会导致您在调试时误入歧途，并使其更加痛苦。好的一面是 Go 中的一些 linters 可以自动为你找到影子变量。

# 3.方法上的类型接收器接收类型的副本

Go 中的“方法”是与特定类型相关联的函数。例如:

```
type MyStruct struct {
 Name string
}func (ms MyStruct) PrintNameMethod() {
 fmt.Println(ms.Name)
}
```

在这段代码中，“PrintNameMethod”函数与“MyStruct”类型相关联。它只能作为“MyStruct”实例的一部分被调用。在该方法的开始，您可以看到它在一个名为“ms”的“MyStruct”类型变量中接收。这允许您从方法内访问 MyStruct 实例的值。但是，如果您想要更新 MyStruct 数据，就像下面的例子一样，您希望会发生什么呢？

```
package mainimport "fmt"type MyStruct struct {
 Name string
}func (ms MyStruct) PrintNameMethod() {
 fmt.Println(ms.Name)
}func (ms MyStruct) UpdateNameMethod(newName string) {
 ms.Name = newName
}func main() {
 mS := MyStruct{Name: "Joe"}
 mS.UpdateNameMethod("Jane")
 mS.PrintNameMethod()
}
```

该代码将输出:

```
Joe
```

原因是“UpdateNameMethod”函数收到了 MyStruct 的副本，然后更新了它。然而，最初的 MyStruct 保持不变。您需要更新接收器以接收指向该结构的指针，如下所示:

```
func (ms *MyStruct) UpdateName(newName string) {
 ms.Name = newName
}
```

这个小小的改变意味着你可以直接修改这个结构。这样，原始的 MyStruct 被修改，代码输出我们期望的结果。

这是众所周知的，但是它很容易被忽略，就像本文中的其他例子一样，在尝试调试时，它可能会让您陷入徒劳的境地。

# 4.您可以在 nil 结构上调用方法

这导致了痛苦，因为当你试图调试时，它会让你分心。在下面的示例代码中，您尝试创建一个 DB 结构，但是出现了一些错误。这显然是一个过于简化的例子，但它确实发生了。出错了，你错过了一个错误，留下了一个指向结构体的零指针。在这种情况下，您认为示例代码会在哪里死机？

```
package mainimport "fmt"type DBStruct struct {
 DBConnectionName string
}func (ms *DBStruct) PrintDBConnectionName() {
 fmt.Println(ms.DBConnectionName)
}func main() {
 s, _ := getDBStruct()
 s.PrintDBConnectionName()
}func getDBStruct() (*DBStruct, error) {
 return nil, fmt.Errorf("oh noes, couldn't create the DB connection")
}
```

对我来说，我预计它会在这一行惊慌失措:

```
s.PrintDBConnectionName()
```

在这一行，我们忽略了错误，s 是零。所以它肯定会在那里恐慌吗？没有。它不会惊慌，直到这一行:

```
fmt.Println(ms.DBConnectionName)
```

这是因为 Go 会很乐意调用 nil 结构上的函数。为什么这是一个陷阱，是因为它经常在调试时把你送上错误的道路。函数被调用 ok，所以结构显然是好的，所以一定是别的什么导致了这个问题！最好的解决方案是牢记这一点，并确保在尝试使用 nil 指针之前检查它们。

# 结论

这些陷阱都不是特别复杂，大多数都很容易理解。问题来自他们的微妙之处。它们很容易被编译器和代码审查遗漏。然后，当出现问题时，你可能会追着自己的尾巴跑，因为代码没有像你预期的那样工作，所以它们值得记住，以防万一。