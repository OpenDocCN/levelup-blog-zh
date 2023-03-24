# Golang 1.18 —泛型解释

> 原文：<https://levelup.gitconnected.com/golang-1-18-generics-explained-bc0feaeb5f0e>

![](img/22ae99d4c4bb86b65b9926071565bc1d.png)

照片由 [Tim Mossholder](https://unsplash.com/@timmossholder?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

多年来，golang 社区一直在等待仿制药。

好消息。随着新的 1.18 更新，我们现在可以访问泛型了。所以让我们深入了解一下泛型到底是什么。

这个例子的**源代码**可以在这篇博文的末尾找到。

# 什么是泛型？

**泛型**表示**参数化类型。这个想法是允许整型、字符串等类型。成为方法的参数。它们确实有广泛的应用，但是它对于框架开发尤其重要，因为它利用了开发人员的工作。**

# 例子

让我们想一个非常简单的特征。我们希望找到一组数字中的最大值。

如果没有泛型，我们会这样做:

```
func Max(array []int) int { }
func Max(array []float64) float64 { }
```

我们必须为每种类型声明一个函数。这是不好的，因为这会导致重复的代码，并且耗费时间。

现在我们有了期待已久的新泛型特性，我们可以这样做:

```
type Number interface {
	int | float64
}func Max[T Number](array []T) T { }
```

我们需要定义一个名为`Number`的新类型，以声明泛型可以拥有的所有可用类型。在我们的例子中，我们声明我们的泛型是一个`int`或者一个`float`类型。

现在我们可以使用我们的`Number`类型定义一个新的函数，就像这个`func Max [T Number](..)`。我们告诉编译器，我们希望我们的泛型类型`T`成为`Number`的类型。

一旦我们完成了这些，我们就可以在函数中重用通用参数`T`来定义输入和输出，就像这个`func ..(array []T) T`。

我们现在已经完成了函数签名的定义。现在让我们添加我们的业务逻辑。

```
func Max[T Number](array []T) T {
	var result T = 0
	for key, _ := range array {
		if array[key] > result {
			result = array[key]
		}
	}
	return result
}
```

如您所见，我们还可以在函数中重用泛型类型`T`，以存储结果值`var result T`。

代码本身在泛型数组中循环，如果结果变量中的数字高于当前存储的值，就将它存储在结果变量中。一旦我们循环了所有的值，我们只是返回最高的存储值。

最后但同样重要的是，我们只需要调用我们的函数

```
func main() {
	intValues := []int{2, 3, 15, 27, 11, 13}
	fmt.Println("Int values max: ", Max(intValues)) floatValues := []float64{2.1, 3.2, 15.4, 27.9, 11.1, 13.2}
	fmt.Println("Float values max: ", Max(floatValues))
}
```

当你查看 main 时，你可以看到我们现在有能力调用不同类型的`Max`函数。在我们的例子中是一个由`int`和`float64`组成的数组。

现在让我们看看运行应用程序后的输出:

```
➜  generics go run .Int values max:  27
Float values max:  27.9
```

# 概述

golang 社区已经等待这个通用特性很久了。随着 1.18 的更新，我们终于可以使用泛型了。这对于框架开发尤其有用。

我们写了一个简单的函数，它返回一个普通数组中的最大值。

**源代码**可以在[这里](https://github.com/jakob-fiegerl/golang-generics)找到。

详细的博文也可以在 golang 官方页面找到:[https://go.dev/doc/tutorial/generics](https://go.dev/doc/tutorial/generics)

# 喜欢这篇文章？

请务必在 ***媒体*** 和我的社交媒体上关注我。

➕ **推特** : [thejakeio](https://twitter.com/thejakeio)