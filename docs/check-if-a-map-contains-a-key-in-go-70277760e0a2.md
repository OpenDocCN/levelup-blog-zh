# 检查地图是否包含 Go 中的关键字

> 原文：<https://levelup.gitconnected.com/check-if-a-map-contains-a-key-in-go-70277760e0a2>

![](img/d5be2a9eb23a52f1c6fa406abfbb268b.png)

由 [Pacto Visual](https://unsplash.com/es/@pactovisual?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

当有人试图使用 Go 编写软件时，这是一个非常流行的问题。

> ***“如何检查给定的键是否存在于映射中？”***

对于那些急着寻找答案的人来说，这就是答案:)

```
if val, ok := dict["apple"]; ok {
    //do something here
}
```

映射是强大的内置数据结构，它将一种类型的值(*键*)与另一种类型的值(*元素*或*值*)相关联。

这个内置的“键”可以是为其定义了等式运算符的任何类型，例如整数、浮点、复数、字符串、指针、接口、结构和数组。

可以使用常用的复合文字语法和冒号分隔的键值对来构造映射，例如:

```
var timeZone = map[string]int{
    "UTC":  0*60*60,
    "EST": -5*60*60,
    "CST": -6*60*60,
    "MST": -7*60*60,
    "PST": -8*60*60,
}
```

当使用映射中不存在的键提取映射值时，将为该类型返回零值。

例如，如果映射包含整数，查找一个不存在的键将返回`0`。在下面的代码中，当搜索一个现有的人和一个不存在的人时，返回相同的 map 值。

```
func main() {
 persons := map[string]int{
  "ram":   0,
  "rahim": 1,
 }
 fmt.Println(persons["ram"])
 fmt.Println(persons["abby"])
}

//output 
0
0
```

有时，您需要区分丢失的条目和零值。因此，有一个很好的习语叫做“逗号 ok ”,这将非常有用。

在本例中，如果存在`ram"`，则`score`将被适当设置，`ok`将为真；否则，`score`将被设置为零，并且`ok`将为假。这里有一个函数将它与一个漂亮的错误报告放在一起:

```
func main() {
 persons := map[string]int{
  "ram":   0,
  "rahim": 1,
 }

 if score, ok := persons["ram"]; ok {
  fmt.Println("ram has a score", score)
 } else {
  fmt.Println("ram does not exists")
 }

 if score, ok := persons["abby"]; ok {
  fmt.Println("abby has a score", score)
 } else {
  fmt.Println("abby does not exists")
 }
}

//output
ram has a score 0
abby does not exists
```

为了在不担心实际值的情况下测试在地图中的存在，我们可以使用[空白标识符](https://go.dev/doc/effective_go#blank) ( `_`)来代替通常的值变量。

```
 _, present := persons["abby"]
 fmt.Println("abby is present ?", present)

//output 
abby is present ? false
```

附加阅读:

[https://go.dev/doc/effective_go#maps](https://go.dev/doc/effective_go#maps)