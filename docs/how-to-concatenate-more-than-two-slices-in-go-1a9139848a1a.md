# 如何在 Go 中连接两个以上的切片

> 原文：<https://levelup.gitconnected.com/how-to-concatenate-more-than-two-slices-in-go-1a9139848a1a>

![](img/76504a4ca411c93771522eacadc5874b.png)

照片由 [Krys Amon](https://unsplash.com/@krysamon?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在这篇文章中，你可以看到以下在 go 中连接切片的方法

1.  通常采用两片拼接
2.  连接两个以上切片的简单解决方案
3.  连接两个以上切片的有效解决方案

# **一般练习两片拼接**

```
package main

import "fmt"

func main() {
	slice1 := []string{"apple", "banana", "peach" }
	slice2 := []string{"avacado", "kiwi", "pineapple"}

	slice3 := append(slice1, slice2...)
	fmt.Println(slice3)
}
```

Go 中的切片拼接可以使用标准库中的内置`*append()*`函数来完成。

在上面的例子中，切片(`*slice1*`)作为第一个参数，第二个切片(`*slice2*`)中的所有元素作为第二个参数。使用 **append()** 函数，当我们将这些切片作为参数传递时，它返回一个包含所有元素的更新切片。

`*append()*`中第二个参数后面的三点运算符(`*...*`是因为它是一个 [*变量函数*](https://en.wikipedia.org/wiki/Variadic_function) 并且接受无限数量的参数。

# **连接两个以上切片的简单解决方案**

您可以传递给 append 函数的切片数量(2)是有限制的，假设您发现需要追加两个以上的切片，下面是合并方法的一个版本。

```
package main

import "fmt"

func main() {

 slices := [][]string{{"apple", "banana", "peach"},
  {"orange", "grape", "mango"},
  {"strawberry", "blueberry", "raspberry"}}

 fmt.Println(concatAppend(slices))

}
func concatAppend(slices [][]string) []string {
 var tmp []string
 for _, s := range slices {
  tmp = append(tmp, s...)
 }
 return tmp
}
```

我们在这里所做的实际上是循环遍历**切片**并执行一个循环追加到 **tmp** 数组以保持每次迭代。

# **连接两个以上切片的高效解决方案(速度快 2 倍)**

有些人发现这种创建一个空片然后追加的方法会导致许多不必要的分配，这些分配可以避免，并通过下面的方法将代码性能提高 2 倍

```
package main

import "fmt"

func main() {

 slices := [][]string{{"apple", "banana", "peach"},
  {"orange", "grape", "mango"},
  {"strawberry", "blueberry", "raspberry"}}

 fmt.Println(concatCopyPreAllocate(slices))

}

func concatCopyPreAllocate(slices [][]string) []string {
 var totalLen int
 for _, s := range slices {
  totalLen += len(s)
 }
 tmp := make([]string, totalLen)
 var i int
 for _, s := range slices {
  i += copy(tmp[i:], s)
 }
 return tmp
}
```

下面是 Cameron Sparr 关于 StackOverflow 的回答的链接，并附有基准测试示例。

# 使用`append()`进行切片拼接的副作用

`*append()*`函数并不总是为返回的切片创建新的底层数组。如果**片 1** 的容量足够容纳来自**片 2** 的元素，那么产生的片将与**片 1** 共享相同的底层数组，这可能会产生意想不到的副作用。

**举例:**

```
package main

import "fmt"

func main() {

 slice1 := make([]int, 3, 6)               // s1 has capacity of 5
 slice1[0], slice1[1], slice1[2] = 1, 2, 3 // s1 has length of 3
 slice2 := []int{4, 5, 6}

 slice3 := append(slice1, slice2...)

 fmt.Println("Before adding element to slice 3")
 fmt.Println("slice1:", slice1)
 fmt.Println("slice2:", slice2)
 fmt.Println("slice3:", slice3)
 //fmt.Println(slice1, slice3)

 slice3[0] = 7

 fmt.Println("After adding element to slice 3")
 fmt.Println("slice1:", slice1)
 fmt.Println("slice2:", slice2)
 fmt.Println("slice3:", slice3)
}

//output 
Before adding element to slice 3
slice1: [1 2 3]
slice2: [4 5 6]
slice3: [1 2 3 4 5 6]
After adding element to slice 3
slice1: [7 2 3]
slice2: [4 5 6]
slice3: [7 2 3 4 5 6]
```

改变**切片 3** 中的元素值会导致**切片 1** 也发生改变。发生这种情况是因为它们共享相同的底层数组，这可能会导致错误。可以通过确保由`*append()*`返回的片由新的底层数组支持来防止这个问题，而不管**片 1** 的容量如何。

**方法如下:**

```
package main

import "fmt"

func main() {

 slice1 := make([]int, 3, 6)               // s1 has capacity of 5
 slice1[0], slice1[1], slice1[2] = 1, 2, 3 // s1 has length of 3
 slice2 := []int{4, 5, 6}

 slice3 := append(slice1[:len(slice1):len(slice1)], slice2...)

 fmt.Println("Before adding element to slice 3")
 fmt.Println("slice1:", slice1)
 fmt.Println("slice2:", slice2)
 fmt.Println("slice3:", slice3)
 //fmt.Println(slice1, slice3)

 slice3[0] = 7

 fmt.Println("After adding element to slice 3")
 fmt.Println("slice1:", slice1)
 fmt.Println("slice2:", slice2)
 fmt.Println("slice3:", slice3)
}

//output 
Before adding element to slice 3
slice1: [1 2 3]
slice2: [4 5 6]
slice3: [1 2 3 4 5 6]
After adding element to slice 3
slice1: [1 2 3]
slice2: [4 5 6]
slice3: [7 2 3 4 5 6]
```

将附加功能调整为

```
append(slice1[:len(slice1):len(slice1)], slice2...)

//output 
Before adding element to slice 3
slice1: [1 2 3]
slice2: [4 5 6]
slice3: [1 2 3 4 5 6]
After adding element to slice 3
slice1: [1 2 3]
slice2: [4 5 6]
slice3: [7 2 3 4 5 6]
```

# 结论

我们看到了在 Go 中连接两个或更多切片的两种方法，以及如何避免使用`*append()*`函数的副作用。如果您对 Go 中的切片连接有任何进一步的见解，欢迎发表评论。

编码快乐！

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)