# Golang 中的可选函数参数模式

> 原文：<https://levelup.gitconnected.com/optional-function-parameter-pattern-in-golang-c1acc829307b>

![](img/ea628860a1d6ab69f438f10313061c3f.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

Go 不支持可选的函数参数。然而，对可选参数的需求总是存在的。

在 Go 中有很多方法可以为函数提供可选参数，但是最优雅的方法是使用函数选项。

下面是它是什么以及如何实现它。

# 什么是可选参数函数

可选参数函数是至少有一个参数的函数，调用时不需要传入该参数。

在 python 中，这相当容易！我们来举个例子。

```
def example_function(param1, param2, param3=None, param4=None):
    pass# Doesn't work, param2 missing
example_function("hello")# Works
example_function("hello", "bye")# Works. Both the same
example_function("hello", "bye", "hey")
example_function("hello", "bye", param3="hey")# Works. Both the same
example_function("hello", "bye", "hey", "foo")
example_function("hello", "bye", param3="hey", param4="foo")
```

因为参数是可选的，所以可以传入也可以不传入。如果没有提供参数的默认值，函数将使用该值。否则，该函数将使用给定值。

## Golang 中的 Ogivenl 参数函数

与 C++或 Python 不同，如果没有指定，Go 不支持带有默认值的可选函数参数。

然而，在 Go 中有许多方法为函数提供可选参数。其中，功能选项模式是最优雅的方式。

# 功能选项实施

functional 选项是一种模式，在这种模式中，您声明一个不透明的`Option`类型，它在某个内部结构中记录信息。

您接受这些选项的可变数量，并根据内部结构上的选项记录的完整信息进行操作。

这里有一个例子。

在这个例子中，我们可以看到 *WithCounty* 函数是一个返回函数的函数，返回的函数接受一个指向 *Student* struct 的指针作为参数。

对于构造函数和其他预计需要扩展的公共 API 中的可选参数，使用这种模式，尤其是如果这些函数已经有三个或更多的参数。

这里还有一些关于 Go 中可选函数参数模式的好资源。

1.  [Go 中的可选功能参数](https://bojanz.github.io/optional-parameters-go/)
2.  [Go 中默认的参数:功能选项](https://charlesxu.io/go-opts/)
3.  [友好 API 的功能选项](https://dave.cheney.net/2014/10/17/functional-options-for-friendly-apis)
4.  [处理 go 中的可选参数](https://petomalina.medium.com/dealing-with-optional-parameters-in-go-9780f9bfbd1d)
5.  [了解 Golangs 函数类型](https://www.integralist.co.uk/posts/understanding-golangs-func-type/)
6.  [功能选项](https://github.com/uber-go/guide/blob/master/style.md#functional-options)