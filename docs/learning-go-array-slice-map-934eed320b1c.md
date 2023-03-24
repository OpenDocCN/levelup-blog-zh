# 学习围棋—数组、切片、地图

> 原文：<https://levelup.gitconnected.com/learning-go-array-slice-map-934eed320b1c>

![](img/2d3ae06690c3807d8b3042e9afcb2329.png)

图片提供:[google.com](https://www.google.com/search?sa=X&sxsrf=ALeKk01IMcufXyYru_UgGh5vSm0SJTaZGw:1589215435126&source=univ&tbm=isch&q=Learning-Go+images&ved=2ahUKEwiBsu3roKzpAhUBXSsKHfFpDKEQ7Al6BAgLECc&biw=1792&bih=986#imgrc=xHsYWTL_SDGn7M)

我们已经[见过](/go-working-with-primitive-data-types-a06245778957)如何创建和使用变量、指针和常量。在本文中，我们将看到语言中内置的四种重要的集合类型。

我们从简单的阵列开始，然后我们继续讨论 Slice，这是阵列的一个发展版本。从 Slice 开始，我们讨论映射一个键值对集合。让我们从数组开始。

# 排列

就像任何其他编程语言一样，Go 中的数组是相似数据类型的固定大小集合。

数组声明和其他语言一样非常简单，但是有一个有趣的变化，

```
 **other language syntax          |            Go syntax**
  int[] arr = new int[10]         **|**           arr := [10]int
```

如果您来自 C#或 Java 之类的编程背景，那么声明的左侧可能看起来很熟悉。Go 中唯一的变化是我们在数组的数据类型前指定了带有 max-limit 的方括号。

像任何其他变量声明一样，数组也可以用不同的方式声明初始化。

```
//Declaration 1
var arr = [5]int{1, 2, 3, 4, 5}//Declaration 2
var arr [5]int
arr = [5]int{1, 2, 3, 4, 5}//Declaration
arr := [5]int{1,2,3,4,5}
```

Go 中的数组是从零开始索引的，所以我们可以从“0”开始访问数组元素。

```
var arr [5]int
arr[0] = 1
arr[1] = 2
fmt.Printf(arr[1]) //prints: 2 
```

此外，Go 中的数组是有界的。如果您试图访问超出数组限制的项，Go 将抛出运行时错误。

```
var arr [5]int
arr[0] = 1
arr[1] = 2
fmt.Printf(arr[6])//Throws below compilation error
**invalid array index 6 (out of bounds for 5-element array)**
```

Go 还支持多维数组:

```
//Multi dimensional array
var arrTwoD [2][3]int
for i := 0; i < 2; i++ {
  for j := 0; j < 3; j++ {
   arrTwoD[i][j] = i + j
   }
 }
 fmt.Println("2d: ", arrTwoD) // prints: [[0 1 2] [1 2 3]]
```

固定长度的数组总是很难处理。数组的内存分配只在初始化时进行一次，因此很难根据数据大小来增加或减少数组的大小。与动态内存分配数据结构相比，数组具有更好的速度和性能。由于数组的刚性本质，在代码库中切片比数组使用得更频繁是很常见的。接下来让我们看看切片。

# 薄片

切片是构建在数组之上的数组的进化版本。与数组相反，切片的大小可以根据数据的大小而增长和收缩。切片的灵活性使其值得选择阵列，即使后者与前者相比具有更好的速度和性能。

**宣言**

```
 mySlice := []int{1, 2, 3}
```

片的声明非常简单，类似于数组声明，唯一的区别是我们没有指定大小。这足以让编译器理解数组和切片之间的区别。

**动态调整大小**

在幕后，编译器在看到上面的声明时创建了一个大小为 3 的数组，将元素`1,2,3`添加到数组中，并将变量`mySlice`指向数组。我们不必自己处理底层数组。相反，我们只处理 slice 变量。这可能会引发一个问题:如果要与数组一起工作，切片与数组有何不同。

如前所述，切片可以动态改变大小。让我们看看内置方法`append()`来更好地理解这一点。

```
 demoSlice := []int{1,2}
 fmt.Println(demoSlice) // [1,2] demoSlice = append(demoSlice,3)
 fmt.Println(demoSlice) // [1,2,3]
```

因此，我们可以用`append()`方法生成切片。Go 管理底层数组，它让开发人员不用担心调整大小的开销。

**切片中的切片**

Go 中的冒号操作符`:`有助于创建切片中的切片。

`s1 := demoSlice[0:2]` —创建一个新数组，其索引`0`处的元素一直到索引`2`之前的元素。`s1`保存一个`[1,2]`的数组。

`s2 := demoSlice[0:]` —创建一个新数组，其元素位于索引`0`处，直到切片的末尾。`s2`保存一个`[1,2,3]`的数组。

`s3 := demoSlice[:2]` —创建一个新数组，其元素从片的开始处一直到索引`2`之前的元素。`s3`保存一个`[1,2]`的数组。

切片是 Go 的强大特性之一，当我们需要处理更小的数据集时，它提供了动态调整切片大小和创建子切片的能力。

# 地图

映射是一个集合，类似于其他编程语言中的字典或堆。地图是无序的集合，数据存储为**键值**对。映射用于快速检索、基于键的值查找，并且是其性能常用的数据结构之一。

映射是可变的数据类型，可以添加、修改、删除和清除项目。

请注意，地图是一个无序的集合，这一点非常重要。

**申报**

```
var m **map[--KeyType--]--ValueType--**// Example declaration
var demoMap1 map[int]stringdemoMap2 := map[string]bool{}
```

**从地图分配和检索**

```
employeeMap := map[int]string{}//Assignment
employeeMap[1] = "Employee 1"
employeeMap[2] = "Employee 2"
fmt.Println(employee) // map[1:Employee 1 2:Employee 2]//Retrieving data
name := employeeMap[2]fmt.Println(employee[1]) //Employee 1
fmt.Println(name) //Employee 2
```

与一些编程语言不同，Go 没有任何方便的函数来列出映射的键或值。然而，它允许通过使用`range`操作符进行迭代。

```
for key, value := range employeeMap {
    fmt.Printf("%d is the key for the value %q\n", key, value)
}**Output:**
2 is the key for the value "Employee 2"
1 is the key for the value "Employee 1"
```

**检查映射中是否存在关键字**

检查 map 中是否存在键值对是非常方便的操作之一。使用键访问地图中的元素会返回两个值—值和布尔值。bool 返回值将有助于理解一个键在映射中是否可用。

```
name, ok := employeeMap[1]// Employee 1, true
name, ok := employees[1001]// "",false
```

**删除**

为了从映射中删除一个值，我们将使用内置方法`**delete**`。删除会把 Map 作为第一个参数，把需要删除的值的 key 作为第二个参数。

```
employeeMap := map[int]string{}//Assignment
employeeMap[1] = "Employee 1"
employeeMap[2] = "Employee 2"delete(employeeMap, 2)
fmt.Println(employeeMap)**Output:** map[1:Employee 1]
```

没有从地图上移除所有项目的便捷功能。

```
employeeMap = map[int]string{}
fmt.Println(employeeMap)**Output** map[]
```

映射由键值对组成，并提供了一种不依赖索引来存储数据的方法。这允许我们根据值的含义和与其他数据类型的关系来检索值。

# 结论

我们首先讨论了数组，以及数组是同一类型实体的固定大小的集合。所以你可以有整数数组，也可以有结构类型的数组。我们在 Go 中可以使用的任何数据类型，我们都可以创建一个。

然后我们继续讨论切片，以及切片在许多方面如何表现为数组，只是切片不是固定大小的实体。切片可以根据需要调整大小，因此通常用来代替数组，因为改变它们大小的能力使它们更加灵活，并为它们开辟了更多的用例。

然后，我们讨论了如何在 Go 中的一个名为 map 的集合类型中将键和值关联在一起，以及如何创建该映射，如何向该映射添加实体，以及如何从中提取实体。

接下来我们将看到 Go 中的结构。在围棋中我们没有阶级概念。我们有方法，也有字段，但是在这门语言中，这些是以一种更加动态和灵活的方式联系在一起的。让我们在下一篇文章中看到。

参考资料和进一步阅读:

[](https://blog.golang.org/slices-intro) [## Go 博客

### Andrew Gerrand 2011 年 1 月 5 日简介 Go 的切片类型提供了一种方便有效的工作方式…

blog.golang.org](https://blog.golang.org/slices-intro) [](https://medium.com/rungo/the-anatomy-of-slices-in-go-6450e3bb2b94) [## 围棋中的切片剖析

### 切片类似于数组，但它们的长度可以不同。

medium.com](https://medium.com/rungo/the-anatomy-of-slices-in-go-6450e3bb2b94) [](https://medium.com/rungo/the-anatomy-of-maps-in-go-79b82836838b) [## 围棋中的地图剖析

### 映射是一种复合数据类型，可以保存由键:值对表示的数据。

medium.com](https://medium.com/rungo/the-anatomy-of-maps-in-go-79b82836838b)