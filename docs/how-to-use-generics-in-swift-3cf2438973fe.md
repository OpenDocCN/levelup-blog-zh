# 如何在 Swift 中使用泛型

> 原文：<https://levelup.gitconnected.com/how-to-use-generics-in-swift-3cf2438973fe>

## 让你的代码最小化

![](img/a24d88e52cb5f9a5b11e579f412a6368.png)

由[萨法尔·萨法罗夫](https://unsplash.com/@codestorm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

作为开发人员，我们的主要职责之一是尽可能保持代码简单，避免重复代码。泛型是提供很多好处的功能之一，比如更好的性能、更少的代码、可重用的代码等等。所以我认为每个开发人员都了解泛型是很重要的。

# 为什么我们需要泛型？

正如你所看到的，`SearchUtil`类将接受一个整数列表作为参数。`searchItem`函数将一个整数(需要搜索的)作为输入并返回结果。这似乎很好。不是吗？。那还有什么问题？

假设，现在你需要搜索一个字符串或对象的列表。你会怎么做？这个问题有两种可能的解决方案。

1.  创建另一个`SearchUtil`类和`searchItem`函数，它们将接受字符串或对象类型并返回结果。
2.  使用泛型

解决方案 1 显然不是一个好主意。因为我们要写很多代码，而且很多代码会重复。在这种情况下，泛型可以提供帮助。

# 什么是泛型？

根据官方文件，

> 泛型是类、结构、接口和方法，它们为自己存储或使用的一个或多个类型提供了占位符(类型参数)。泛型集合类可能使用类型参数作为其存储的对象类型的占位符；类型参数显示为其字段的类型和其方法的参数类型。泛型方法可能使用其类型参数作为其返回值的类型，或者作为其某个形参的类型。

简而言之，

> 泛型是一个灵活的系统，允许您处理任何数据类型。它把类型的负担从你身上转移到了编译器身上。

我觉得这是最简单的解释。

# 行动中的仿制药

现在我们将把上面的例子转换成泛型。要将`SearchUtil`类转换为泛型，我们需要如下修改它

```
class SearchUtil<T: Equatable>
```

这里`T`表示任何一种类型都可以作为实参使用。我使用`Equatable`协议，因为对象将被检查是否相等。

还有，我们需要修改`searchItem`函数。

```
func searchItem(element: T, foundItem: (T?)->())
```

现在我们可以使用任何我们想要的类型。

```
let searchUtil = SearchUtil*(*list : listOfObjects*)* let searchUtil = SearchUtil*(*list : listOfNumber*)*
```

上面的代码将没有任何问题。我们来看一个真实的例子。

这里我们创建了一个`Person`结构。我们正在对`listOfPerson`执行搜索操作。现在，如果您想在一个整数列表上执行搜索操作，您可以在不改变`SearchUtil`或`searchItem`的情况下完成

```
*let searchUtil = SearchUtil(list: [Int](arrayLiteral: 24,25,26))
searchUtil.searchItem(element: 26) {(result) -> () in**print(result ?? "Not found")
}*
```

太棒了，不是吗？。

今天到此为止。我希望你学到了新的有用的东西。

如果你有什么建议，请在评论中分享。直到我们再次见面…干杯！

```
**Want to Connect?**If you want to, you can connect with me on [Twitter](https://twitter.com/FarhanT99598254) or [LinkedIn](https://www.linkedin.com/in/farhan-tanvir-b08520151/).
```