# Go 1.18 Beta 1:节日通用礼物

> 原文：<https://levelup.gitconnected.com/go-1-18-beta-1-a-generic-gift-for-the-holidays-be9e7461362>

![](img/d402d37872048677a1d68633953b3b2d.png)

我一直在急切地等待 Go 中的泛型，并在可能的情况下与它们一起玩，但现在 [Go 1.18 Beta 1](https://go.dev/blog/go1.18beta1) 可用了！所以我立刻通过 [asdf](https://github.com/kennyp/asdf-golang) 抓取了它，并实现了一个 [Kotlin Sequences](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/) 启发的围棋子包。

## GenFuncs

你可以在这里看到完整的 genfuncs 包。我用最少的努力实现了所有我喜欢的通用函数:

```
func All[T any](slice []T, predicate Predicate[T]) bool
func Any[T any](slice []T, predicate Predicate[T]) bool
func Associate[T any, K comparable, V any](slice []T, transform TransformKV[T, K, V]) map[K]V
func AssociateWith[K comparable, V any](slice []K, valueSelector ValueSelector[K, V]) map[K]V
func Contains[T comparable](slice []T, element T) bool
func Distinct[T comparable](slice []T) []T
func Filter[T any](slice []T, predicate Predicate[T]) []T
func Find[T any](slice []T, predicate Predicate[T]) (T, bool)
func FindLast[T any](slice []T, predicate Predicate[T]) (T, bool)
func FlatMap[T, R any](slice []T, transform Transform[T, []R]) []R
func Fold[T, R any](slice []T, initial R, operation Operation[T, R]) R
func GroupBy[T any, K comparable](slice []T, keySelector KeySelector[T, K]) map[K][]T
func JoinToString[T any](slice []T, stringer Stringer[T], separator string, prefix string, postfix string) string
func Map[T, R any](slice []T, transform Transform[T, R]) []R
```

并附有所有用法的例子。

## 去蝙蝠战车，我们走！

想要从切片中提取不同的集合？没有更多的*用于/range/map* 样板代码！

```
values := []int{1, 2, 2, 3, 1, 3}
fmt.Println(genfuncs.Distinct(values)) // [1 2 3]
```

或者您可能想测试一个切片中的所有值是否都是正的？

```
numbers := []int{1, 2, 3, 4}
positive := func(i int) bool { return i > 0 }
fmt.Println(genfuncs.All(numbers, positive)) // true
```

这些都是通用类型函数，所以不仅仅是 *[]int* 它可以与任何可比较的类型一起工作，下面是与 floats 相同的 *All* :

```
numbers := []float32{1, 2.2, 3.0, 4}
positive := *func*(i float32) bool { *return* i > 0 }
fmt.Println(genfuncs.All(numbers, positive)) *// true*
```

想要将一部分数字按奇偶分组吗？

```
oddEven := *func*(i int) string {
   *if* i%2 == 0 {
      *return* "EVEN"
   }
   *return* "ODD"
}
numbers := []int{1, 2, 3, 4}
grouped := genfuncs.GroupBy(numbers, oddEven)
fmt.Println(grouped["ODD"]) *// [1 3]*
```

创建一个连接除字符串之外的*片段的字符串？*

```
values := []bool{true, false, true}
fmt.Println(genfuncs.JoinToString(
   values,
   strconv.FormatBool,
   ", ",
   "{",
   "}",
)) *// {true, false, true}*
```

我就讲到这里，因为回购协议有各种例子。要知道，使用泛型，你可能会编写更少的样板代码，使用的 *range* 关键字也会更少。我相信你很伤心。

## 好人

*   语法清晰，易于理解。
*   类型约束按预期工作，很容易编写干净的代码，适当地约束类型，但却是通用的。
*   没碰上什么我想不通的挠头情况。

## 坏事

*   我的 IDE 确实有 1.18 的实验支持，但它并不完全工作，我不得不忽略各种错误的警告并运行代码。
*   他们没有完全实现提议的*契约*和使用类似 *fmt 的接口。纵梁*作为一种类型不是替代品。
*   随着时间的推移，仿制药的提议发生了实质性的变化，所以甚至一些官方文件使用了错误的语法和/或误导。

## 和丑陋的

我没有遇到任何真正丑陋的东西:-)