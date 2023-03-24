# 你认为你不能对散列图进行排序吗？再想想！

> 原文：<https://levelup.gitconnected.com/think-you-cant-sort-a-hash-map-think-again-5ee4da2f0aa2>

## 使用 Golang 中的接口对任何东西进行排序。

![](img/d71aee04dee94d0fc56b0c79b940c8aa.png)

ash 映射是一种非常有用的数据结构，它将唯一键映射到潜在的非唯一值。它们具有恒定的 O(1)查找时间，在各种情况下都很方便。然而，哈希表的一个普遍缺点是你通常不能对它们进行排序。现代编程语言正试图以某种方式使散列图可排序，例如 Python 版允许通过 lambda 函数对散列图进行排序。

在 Go 中，我们利用接口和`sort`包的常见用法，通过几个简单的步骤对我们自己的哈希表进行优雅的排序。有许多可能的方法来完成这项任务，但是这是我最喜欢的方法之一，将`sort`接口扩展到不同的数据结构，比如散列映射。

## 1.创建自定义类型来表示配对和配对列表。

为了能够存储排序后的值，我们必须创建两个自定义数据类型来表示给定哈希映射的键和值。`Pair`结构有属性`Key`和`Value`，它们应该是你的哈希表包含的底层类型。例如，初始化为`map[string]bool{}`的哈希映射将有一个底层类型为`string`的`Key`和一个底层类型为`bool`的`Value`。在我们的例子中，`Key`和`Value`都是底层类型`int`。

然后你会想要定义`type` `PairList`，它只是一个`Pair`结构的数组。

## 2.实现排序。对列表数据结构上的接口。

既然我们已经定义了自定义结构，现在我们可以在`PairList` `struct`上实现`sort.Interface`。我们可以在 GoLang 文档中看到，`sort.Interface`有三个要实现的公共方法— `Len()`、`Less()`和`Swap()`。

[https://pkg.go.dev/sort#Interface](https://pkg.go.dev/sort#Interface)

我们所要做的就是在我们想要添加排序的任何对象上实现这三个方法，并且我们将能够在那个对象上调用`sort.Sort()`！下面，我们在`PairList` `struct`上实现这三种方法。实现很简单，但是如果你愿意，你可以定制它们。

如果你想按`Key`而不是`Value`排序，你可以在`Less()`方法的实现上做些改变。如果你想按降序排序，你也可以交换`i`和`j`。感谢 Go 的接口，权力在你手中！

## 3.将地图转换为配对列表，然后排序并返回配对列表。

这里我们初始化`someMap`来展示排序示例。我们将`pairList`定义为`PairList`的一个新实例，其长度与`someMap`相等。现在，我们可以遍历`someMap`，并为`someMap`中的每个键(`k`)、值(`v`)对将每个索引分配给一个新的`Pair`实例。

这样做了之后，我们现在可以在`pairList`上调用`sort.Sort()`，嘣！

如果在函数中运行这段代码，输出将是:

```
map[0:2 1:5 3:7 7:6 9:3]
Unsorted PairList: [{1 5} {7 6} {0 2} {3 7} {9 3}]
Sorted PairList: [{0 2} {9 3} {1 5} {7 6} {3 7}]
```

您现在已经学会了如何在任何数据结构上实现`sort.Sort`接口，就像真正的 Gopher 一样！:)