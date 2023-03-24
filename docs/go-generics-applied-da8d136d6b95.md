# 应用 Go Generics

> 原文：<https://levelup.gitconnected.com/go-generics-applied-da8d136d6b95>

![](img/b0e15e17baba2d239f1edacbe2231b59.png)

Go 1.18 测试版一出来，我就开始探索泛型。我最终得到的 [genfuncs](https://github.com/nwillc/genfuncs/#func-values) 包，受 [Kotlin](https://kotlinlang.org/) 的启发，包含了一系列关于切片、贴图和排序的有用操作。

## 它们有用吗？

自从我开始编写 Go，我就觉得我写了太多的样板代码。Go 码倾向于[湿](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)，即[干](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)的对立面。我觉得缺少泛型是问题的一个重要部分。所以，如果我相信 Go 是湿的，泛型会有帮助，我在 Go 中写了一个泛型包，让我们应用它看看。

## 应用的泛型

所以为了看看 genfuncs 是否有益，我选择了一个简单的围棋项目， [gotimer](https://github.com/nwillc/gotimer) ，看看应用 genfuncs 能做些什么。Gotimer 是一个 pomodoro time，它在内部接收 ASCII“字体”表示的文件，并将其转换为可以显示的编码格式。有很多收集旅行，所以它似乎是一个合适的。

## 示例 1

下面是处理一个代表单个字符的文件的代码:

这不是我写过的最优雅的围棋，但也相当一般。使用 genfuncs 看起来是这样的:

好多了，一半长度，容易扫描，干代码多。它确实需要一些助手，这些助手是共享的:

## 示例 2

这里是读取单一字体的所有字符位图的地方:

使用 genfuncs:

同样，大约一半的代码，更清晰，更枯燥。

## 再来几杯

下面是几个用于获取可用颜色和字体列表的字符串描述的函数:

使用 genfuncs:

这些字体很小，所以不会缩小很多，但是它们更清晰、更简洁，并且实际上改进了功能(更好的输出字符串和字体大小现在以正确的数字顺序排序)。

## 我对结果的看法

我认为应用 Go 的泛型实现了我所希望的:

*   较少代码
*   更容易理解
*   更干

我很高兴。现在如果我们能修复 Go 的错误处理…