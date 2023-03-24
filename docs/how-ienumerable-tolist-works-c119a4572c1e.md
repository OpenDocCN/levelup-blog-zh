# 多么可爱。ToList()有效

> 原文：<https://levelup.gitconnected.com/how-ienumerable-tolist-works-c119a4572c1e>

## 英寸 NET Framework 4.x

![](img/3ddba94fa4b663f9fa2293dd34207e48.png)

照片由[雷纳托·波齐](https://unsplash.com/@itsrennyman)在 [Unsplash](https://unsplash.com/) 上拍摄

这是讨论`[ToArray()](https://medium.com/@DavidKlempfner/how-ienumerable-toarray-works-4bb7a2cabada)`和`ToList()`如何在幕后工作，以及哪一个更有效的两部分系列的最后一篇文章。

点击[此处](https://medium.com/@DavidKlempfner/how-ienumerable-toarray-works-4bb7a2cabada)查看第 1 部分。

# ToList()源代码

ToList()源代码可以在[这里](https://referencesource.microsoft.com/#System.Core/System/Linq/Enumerable.cs,947)找到。

实现几乎和`IEnumerable<T>.ToArray()`一模一样。

`ToList()`简单地创建一个包含私有数组的新列表，该私有数组在每次迭代中被添加。

第一次迭代将使用`[defaultCapacity = 4](https://referencesource.microsoft.com/#mscorlib/system/collections/generic/list.cs,38)`创建一个新数组，就像在`ToArray()`中一样。

在 Capacity [setter](https://referencesource.microsoft.com/#mscorlib/system/collections/generic/list.cs,123) 中，当初始数组已满时，会创建一个大小加倍的新数组。容量设置器中设置的值在 [EnsureCapacity()](https://referencesource.microsoft.com/#mscorlib/system/collections/generic/list.cs,405) 中计算(在 [Add()](https://referencesource.microsoft.com/#mscorlib/system/collections/generic/list.cs,220) 中调用)。

# 与 ToArray()的差异

`ToList()`和`ToArray()`之间的唯一区别是，如果最终数组的长度与源数组的长度不一样，那么`ToArray()`将进行最后一次精确的数组分配，以存储源数组的元素。

这意味着在大多数情况下，`ToList()` **会更有效，因为它少了一个数组分配。**

# 当 ToArray()和 ToList()的性能相等时

就数组分配的数量而言，这些方法唯一相同的时候(假设源不是一个`ICollection<T>`)是当下列条件成立时:

`IEnumerable<T>` `x`中元素的数量是> = 4 并且是 2 的幂。这是因为临时数组的大小总是以前临时数组的两倍，这意味着它将从 4 到 8 到 16 等等，都是 2 的幂。

如果元素的数量大于或等于初始数组大小(4)，并且是 2 的幂，那么当调用`ToArray()`时，就没有必要创建一个完美大小的最终数组。

无论如何，当调用`ToList()`时，一个完美大小的数组永远不会被创建。

## 公式

`x >= 4`并且是 2 的幂可以用 C#写成这样:

`x >= 4 && ((x & (x — 1)) == 0)`

`&`是按位- `and`运算符。当你得到一个 2 的幂的数，比这个数少 1，你将总是得到 0:

## 示例:

`8 = 1000`

`8–1 = 7 = 0111`

`1000 & 0111 = 0`

不确定这什么时候有用，但是如果你知道从一个`IEnumerable<T>`流出的元素的数量，而它不是一个`ICollection<T>`，你就可以使用这个公式来决定是使用`ToArray()`还是`ToList()`。