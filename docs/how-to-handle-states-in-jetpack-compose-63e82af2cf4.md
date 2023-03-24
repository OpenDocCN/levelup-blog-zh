# 如何在 Jetpack 撰写中处理状态

> 原文：<https://levelup.gitconnected.com/how-to-handle-states-in-jetpack-compose-63e82af2cf4>

## 有效地使用状态

![](img/d80aab97d1466b71a5bc140487e102e4.png)

照片由 [Michael Dziedzic](https://unsplash.com/@lazycreekimages?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Jetpack compose 成为 Android 家族的重要组成部分之一。这是一个用于构建原生 UI 的现代工具包。它简化并加速了 UI 开发，用更少的代码让您的应用变得生动。

# 什么是国家？

根据官方文件:

> 应用程序中的状态是可以随时间变化的任何值。这是一个非常宽泛的定义，涵盖了从房间数据库到类变量的所有内容。

向用户显示状态的基本示例是 Snackbar，它显示网络错误何时发生。

# 行动状态

在 Jetpack compose 中，像`TextField`这样的东西不会像在命令式的基于 XML 的视图中那样自动更新。为了让可组合组件更新，必须明确地告诉它新的状态。例如，如果您运行下面的代码，什么也不会发生。

因为我们没有更新`TextField`的`value`参数。

# 如何处理状态

我们可以通过使用`remember` composable 来保存值。`remember`将在初始化期间存储值，并在重组期间返回值。

```
var name by *remember* **{** *mutableStateOf*("") **}**
```

`by`关键字表示 Kotlin 的属性委托语法。它将允许你把可变的状态字符串展开成一个常规的字符串。因此，名称变量的类型将是字符串。如果我们现在运行下面的代码，我们可以看到值将会改变。

这是因为我们正在更新`value`参数。

我们也可以通过用`=`符号替换`by`关键字来使用`remember` composable。在这种情况下，名称变量的类型将是`MutableState<String>`。现在，如果我们想要访问`name`名称变量的值，我们必须使用`name.value`。

```
var name = *remember* **{** *mutableStateOf*("") **}**
```

我个人喜欢用`by`关键词。所以我需要写`name.value`来得到这个值。

`remember`帮助您在重组过程中保持状态，但是它不会在配置改变时改变状态。为此，必须使用`rememberSaveable`。`rememberSaveable`将自动保存任何可以保存在`Bundle`中的值。

# 有状态 VS 无状态

顾名思义，Stateful 意味着 composable 内部有一个状态(就像上面的例子)。与此相反的是无状态。这意味着 composable 中没有状态。有状态可组合总是难以重用和测试。所以你应该总是尝试使用无状态。为了使它无状态，我们将使用状态提升。

# 状态提升

状态提升是一种编程模式，在这种模式下，您可以将可组合的状态移动到该可组合的调用者，从而使可组合成为无状态的。状态提升有很多好处，比如单一的真理源**、**封装、解耦等。实现这一点的简单方法是用一个参数替换状态。现在让我们修改上面的例子。

你可以看到在我们的`Content`函数中没有任何状态。我们已经在可组合组件的调用者中移动了状态。

今天到此为止。我希望你学到了新的东西。你的建议对我很有价值。如果你分享你的建议，我将不胜感激。直到我们再次见面…干杯！

```
**Want to Connect?** If you want to, you can connect with me on [**Twitter**](https://twitter.com/FarhanT99598254)or [**LinkedIn**](https://www.linkedin.com/in/farhan-tanvir-b08520151/)
```