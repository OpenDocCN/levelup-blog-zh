# 在 Go 中使用地图时避免三个错误

> 原文：<https://levelup.gitconnected.com/avoid-the-three-mistakes-when-using-a-map-in-go-699435db226c>

![](img/c8f4baaf86bf6ff85848212ac2827cfa.png)

[廷杰伤害律师事务所](https://unsplash.com/@tingeyinjurylawfirm?utm_source=medium&utm_medium=referral)在[律师事务所](https://unsplash.com?utm_source=medium&utm_medium=referral)拍摄的照片

编程中的地图是一种令人信服的数据结构。为了弄清楚什么是地图，我建议阅读以下文章:[地图实现内部](https://www.youtube.com/watch?v=Tl7mi9QmLns&t=1319s)。

在这篇文章中，我将讨论我们使用地图时的三个错误。

我们开始吧！

## 错误 1:分配给零地图中的一个条目

```
func main() { var ages map[string]int    // nil
    ages["John"] = 21       // panic: assignment to entry in nil map // correct way
    if ages == nil {
        ages = make(map[string]int)
    }
    ages["joe"] = 42 }
```

映射类型的零值是 *nil* ，表示完全没有哈希表。请记住，不允许在 *nil 中添加新条目。*

然而，在*无*地图上执行其他地图操作，包括*查找*、*删除、长度、范围循环、*是安全的，因为它的行为就像一个空地图。

```
func main() {var ages map[string]int // lookup
   fmt.Println("age of Bob:", ages["Bob"]) // 0

   // delete
   delete(ages, "Bob")

   // len
   fmt.Println("length:", len(ages)) // 0

   // range
  for k, v := range ages {
    fmt.Println(k, v)
  }}
```

## 错误 2:获取地图元素的地址

```
func main() {ages := map[string]int{
  "Alice": 20,
  "Bob":   25,
 } // address of a Map
  c := &ages
  fmt.Println((*c)["Alice"]) _ = &ages["Alice"]  // error: cannot take the address of a map key
}
```

因为 Go 中定义的每个变量都占据一个唯一的内存位置，所以我们可以获取该变量的地址。地图变量在这里也不例外。

但是在大多数情况下，不需要获取地图变量的地址，因为地图是一种引用类型，地图的地址与地图本身的地址相同。

与地图变量不同，地图元素不是变量。请记住，我们不能获取地图元素的地址。

## 错误 3: **比较地图**

```
ages1 := map[string]int{
  "Alice": 20,
  "Bob":   25,
 }ages2 := map[string]int{
  "Alice": 40,
  "Bob":   55,
 }fmt.Println(ages1 == ages2)
```

我们不能只通过比较运算来比较两幅地图；唯一合法的比较是与*无。*

因此，为了测试两个映射是否包含相同的键和相同的关联值，我们必须编写一个循环:

```
func equal(x, y map[string]int) bool {
     if len(x) != len(y) {
        return false
     }
    for k, xv := range x {
        if yv, ok := y[k]; !ok || yv != xv {
            return false
         } 
    }
    return true
}
```

我希望你喜欢读这个。如果你想支持我成为一名作家，考虑注册[成为](https://jerryan.medium.com/membership)媒体会员。您还可以无限制地访问 Medium 上的每个故事。