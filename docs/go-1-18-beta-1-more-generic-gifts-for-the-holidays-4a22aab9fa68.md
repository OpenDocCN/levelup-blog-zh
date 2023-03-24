# Go 1.18 Beta 1:更通用的节日礼物

> 原文：<https://levelup.gitconnected.com/go-1-18-beta-1-more-generic-gifts-for-the-holidays-4a22aab9fa68>

![](img/d402d37872048677a1d68633953b3b2d.png)

好了，我在这里放不下我说的[的](/go-1-18-beta-1-a-generic-gift-for-the-holidays-be9e7461362) [genfuncs](https://github.com/nwillc/genfuncs) 回购！我一直在添加更多的通用功能，清理，记录，和…

## 各种各样的！

我引入了自己的基于泛型的堆，并添加了插入排序、堆排序和快速排序。

为了实现所有这些，并避免今天在 Go 中排序的混乱，我显然使用了泛型。我从一个比较器*开始，而不是像`sort`包那样有一堆类型变化:*

```
type Comparator[T any] func(a, b T) ComparedOrder
```

其中*compare order*是传统的:

```
var (
    LessThan    ComparedOrder = -1
    EqualTo     ComparedOrder = 0
    GreaterThan ComparedOrder = 1
)
```

因此，给定一个*比较器*函数作为参数，堆和排序变得完全通用化:

```
func NewHeap[T any](comparator Comparator[T]) *Heap[T]
func HeapSort[T any](slice []T, comparator Comparator[T])
func InsertionSort[T any](slice []T, comparator Comparator[T])
func QuickSort[T any](slice []T, comparator Comparator[T])
```

现在，等一下你会问，你总是要实现一个比较器*吗*即使对于已知的类型 Go 说是`constrants.Ordered`？不，我抓住你了！

```
var numericOrdered = genfuncs.OrderedComparator[int]()
```

它再次使用泛型和`constraints.Ordered`约束为浮点类型、整型和字符串创建一个*比较器*。

所以一个基本的升序或降序排序应该是这样的:

```
alphaOrder := genfuncs.OrderedComparator[string]()
letters := strings.Split("example", "")

genfuncs.QuickSort(letters, alphaOrder)
fmt.Println(letters) // [a e e l m p x]
genfuncs.QuickSort(letters, genfuncs.ReverseComparator(alphaOrder))
fmt.Println(letters) // [x p m l e e a]
```

但是如果你有一个复杂的类型，有疯狂的词法规则，只需要为它实现一个特定的比较器。

看一看 Go 的`sort`包，你会对它为不同类型的人所做的体操感到不悦。Go 还在挑选合适的排序方面变了一些魔术，它的排序实现稍微优化了一些，但我敢打赌，如果我给它添加这些功能，它的大小会减少 50%，并且更加灵活和易于理解。

## 我真的很喜欢这个

到目前为止，我真的很喜欢 1.18 泛型。编写和使用函数，摒弃样板类型的特定代码是天堂。我甚至收到了人们的商业使用请求，这很好，但是我提醒他们，正如我在文档中所说的，我很确定 Go 将很快得到这个*的官方形式*。对他们来说，这是一场太容易、太大的胜利，不容错过。但是直到他们享受！