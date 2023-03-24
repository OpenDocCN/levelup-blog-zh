# 泛型方法和类型推理

> 原文：<https://levelup.gitconnected.com/generic-methods-and-type-inference-aaea27892df3>

![](img/50381e8b615369f8392c9c627bd69871.png)

[杰瑞米·托马斯](https://unsplash.com/@jeremythomasphoto)在 [Unsplash](https://unsplash.com/) 上拍照

当您调用泛型方法时，您不必在三角括号之间指定类型。编译器可以从参数中推断出类型。

即。你不必用这个:

`Display<String>(“Abc”);`

相反，你可以写:

`Display(“Abc”);`

既然是这样，下面的代码段会调用哪个方法呢？：

C#编译器总是选择比一般匹配更明确的匹配，因此将调用第一个方法。

这是一件好事，因为如果它执行泛型方法，将会有一个无限递归，因为`Display` 方法调用`Display()`。