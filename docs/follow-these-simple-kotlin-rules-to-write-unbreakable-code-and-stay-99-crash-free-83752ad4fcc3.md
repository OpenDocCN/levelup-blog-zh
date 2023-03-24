# 遵循这些简单的 Kotlin 规则来编写牢不可破的代码

> 原文：<https://levelup.gitconnected.com/follow-these-simple-kotlin-rules-to-write-unbreakable-code-and-stay-99-crash-free-83752ad4fcc3>

![](img/fd58019d65b34640f84d4bc391086473.png)

照片由[迈克尔](https://unsplash.com/@polygonglider?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

当 Kotlin 被介绍给 Android 开发者时，它与通常的 Java 世界有些不同。可空性——多么好的主意！很多句法糖之类的。然而，经过多年的使用，我看到打破设计是多么容易。

**如何避免拍腿，让自己的代码牢不可破？**

答案很简单— **以一种不可能(或很难)引入 bug 的方式设计代码**。

让我们来看几件可以做的事情。

# 可空性

在 2009 年的一次软件会议上，[东尼·霍尔](https://en.wikipedia.org/wiki/Tony_Hoare)为发明空引用道歉:“*我称之为我的十亿美元错误。*

科特林修复了它，但是它也允许忽略这个设计。

## ！！操作员

`!!`操作员是最危险的东西之一。我敦促读者——写`!!`——免费程序。即使你 100%确定*这个引用*不可能为空——事实上它可以为空。不是现在，而是在不久的将来，当你的同事(或你)更改代码时。

规则很简单。**偏执。如果有什么东西会坏掉——它会的。**

所以永远不要在本机 Kotlin 代码中使用`!!`操作符。

## lateinit 变量

起初，Kotlin 的这个特性看起来很方便，值得信赖。但现实是残酷的。

在我的第一个 Kotlin 项目中，我看到越来越多的[UninitializedPropertyAccessException](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-uninitialized-property-access-exception/)。当然，您可以检查属性是否已初始化。但是你可以忘记去做，对吗？或者不是你，你的低年级同事。

**偏执。如果有什么东西会坏掉——它会的。**

用`nullable var`代替。示例:

```
var signData: SignData? = null// ....signData.cost?.*let* **{** updateViewState*(*DomainViewState.DataReady*(***it**, domain*))* **}** ?: *run* **{** Timber.e*(*Exception*(*"SignData.Cost is null but it is required"*))* sendMessageEvent*(*R.string.*domain_error)* **}**
```

正如你所看到的，这里你*必须*使用一个空检查— `let`。作为奖励，您可以使用`?:`操作符和*编写一些东西来处理*可能出现的情况，即`signData.cost`将为空。即使你*从未*期望它为空，但通过设计，技术上*可以*为空。哦，永远不要。

# 施工时

## 陈述还是表达？

是一个奇妙的建筑，让我们的生活一天比一天好。但是你应该知道在某些情况下`when`是一个陈述，在某些情况下是一个表达式。

如果你需要使用`when`构造的结果，这意味着它是一个表达式:

```
val result = when *(*event*) {* is ViewEvent.One -> onOne*()* ViewEvent.Two -> onTwo*()
}*
```

如果使用`when`作为表达式，编译器会检查所有可能的分支。如果在上面的例子中`event`是可空的，代码将不会被编译。您必须将其更改为:

```
val result = when *(*event*) {* is ViewEvent.One -> onOne*()* ViewEvent.Two -> onTwo*()
*    null -> { /* something */ }
*}*
```

但是如果你不使用结果，编译器不会强制处理所有可能的情况:

```
when *(*event*) {* is ViewEvent.One -> onOne*()* ViewEvent.Two -> onTwo*()
}*
```

这是一个声明，结果没有用上**这就是**的脆弱之处。

假设你使用 [MVI](https://medium.com/@alexzaitsev/mvi-with-android-compose-on-a-real-example-f5d522707be5) 作为一种表现模式。您的`Composable`正在收听来自`ViewModel`的`ViewEvents`。

你可以这样写:

```
@Composable
private fun EventsProcessor*(* viewModel: SplashViewModel,
    onOpenDomainsList: *()* -> Unit,
    onStartWalletImport: *()* -> Unit
*) {* val event: SplashViewEvent? = viewModel.viewEvents.collectAsState*(*initial = null*)*.value
    when *(*event*) {* is SplashViewEvent.OpenDomainsList -> onOpenDomainsList*()* SplashViewEvent.StartWalletsImport -> onStartWalletImport*()
    }
}*
```

你已经看到问题了吗？

如果不是，请回答下一个问题:*如果将来您或您的同事添加了新的* `*ViewEvent*` *，会发生什么？*

正确答案是——代码作者将不得不手动控制它，并且不要忘记更改上面的`when`。这意味着很可能在某个时候这个问题会被提出来。

又来了。**偏执。如果有什么东西会坏掉——它会的。**

只需将语句转换为表达式，就可以轻松修复上述代码:

```
@Composable
private fun EventsProcessor*(* viewModel: SplashViewModel,
    onOpenDomainsList: *()* -> Unit,
    onStartWalletImport: *()* -> Unit
*) {* val event: SplashViewEvent? = viewModel.viewEvents.collectAsState*(*initial = null*)*.value
    return when *(*event*) {* null -> *{
        }* is SplashViewEvent.OpenDomainsList -> onOpenDomainsList*()* SplashViewEvent.StartWalletsImport -> onStartWalletImport*()
    }
}*
```

你要加的就是`return`。或将`when`结果赋给一个变量。你不必使用它。唯一的目的是强制编译器检查。使用这种方法，你*永远不会忘记改变`when`。*

## Else 分支

另一种拍腿的方法是用`else`分支换`when`。`Else`消耗其他分支中所有非手工处理的案例。

你看到了吗？同样，设想一种情况，将来有人更改了`sealed class`，但是编译器不会提醒您处理新的情况。就因为有`else`在那里。

所以，**永远不要用`else`分支代替`when`。你会安全的。**

# 结论

谢谢你的阅读，我的朋友。

我想你已经明白了:写你的代码的任何一行都要问一个问题——如果一个新人来到这个项目并开始写代码，会安全吗？想象一下，你要去度假，而你不在的时候，一个初级同事必须解决紧急问题。

记住。**多疑。如果有什么东西会坏掉——它会的。**

这就是为什么通过设计写出牢不可破的代码*如此重要。*不依赖*你或别人记忆的代码。只是因为记忆是个不好依赖的东西。尤其是当系统在增长的时候。*

编码快乐！**关注我** [**推特**](https://twitter.com/alexzaitsev_btc) **。**