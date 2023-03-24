# 中的策略设计模式。NET C#

> 原文：<https://levelup.gitconnected.com/strategy-design-pattern-in-net-c-b9dbd863c31e>

## 设计模式

## 了解中的策略设计模式。NET C#

![](img/e1da3d93c7bbb2b6e052d18ffc10d53b.png)

JESHOOTS.COM 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上[拍照](https://unsplash.com/@jeshoots?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 战略设计模式定义

**策略设计模式**是**行为设计模式**的一种。

它的主要目标是通过将行为建模到可能有多个实现的抽象中，将对象的行为从其状态中分离出来。

然后，这个抽象的行为可以由对象使用，它具有在运行时在行为的不同实现之间切换的能力，而不需要重新创建对象。

**策略设计模式**如今被广泛使用。很可能你遇到的每一个解决方案都会用到它。

![](img/dae42316c04548aad197e34b378e3bf1.png)[](https://medium.com/subscribe/@eng_ahmed.tarek) [## 🔥订阅艾哈迈德的时事通讯🔥

### 订阅艾哈迈德的时事通讯📰直接获得最佳实践、教程、提示、技巧和许多其他很酷的东西…

medium.com](https://medium.com/subscribe/@eng_ahmed.tarek) ![](img/dae42316c04548aad197e34b378e3bf1.png)

# 在战略设计模式之前

在**策略设计模式**出现之前，正常的工作方式是将**与对象相关的一切**封装在对象本身内部。通过**一切**，它真的意味着一切。

所以，我们曾经把一个对象的状态和行为包含在对象里面。例如，有这样一个 **Person** 类是完全合乎逻辑的:

然而，随着游戏编程时代的到来，事情变得更加复杂。

在游戏编程中，你不能假设你的游戏角色在不同的情况下总是有相同的行为方式。

例如，使用相同的`Person`示例，如果你的游戏角色是一个人，并且它是使用`Person`类建模的，那么当角色受伤时，你将面临一个问题来建模你的游戏角色的`Run`行为。

开发人员过去常常做的是创建一个以上的`Person`类模型，并实现从一个模型到另一个模型的状态处理方法。但是这对于任何开发人员来说都是一个噩梦，因为增加不同行为的数量，你会得到一个对象的一大堆模型。

另一种方法是在模型本身中包含所有参数，并根据这些参数开始切换行为。

例如，按照同一个`Person`示例，您将添加一个名为`IsInjured`的新布尔属性。然后，在`Run`行为中，您将检查`IsInjured`属性的值并采取相应的行动。

尽管如此，这不是一个好的解决方案，因为它带来了太多的复杂性。最后，您会发现您的模型被太多的细节所膨胀，而这些细节实际上不受它的控制或维护。

![](img/dae42316c04548aad197e34b378e3bf1.png)

# 应用策略设计模式

现在，让我们尝试应用**策略设计模式**，看看它是否会让我们的生活变得更轻松。

**这里值得一提的是**，我们将首先在这个例子中应用这个模式，因为它在许多在线教程中都有描述。然后，我会应用设计增强。

使用同样的`Person`例子，假设我们正在构建我们的游戏，而`Person`类代表我们的游戏角色。

首先，让我们分别确定我们的`Person`状态和行为。

**状态**将会是`Name`和`Age`。
行为**会是`Walk`和`Run`。为简单起见，我们只考虑`Walk`。**

然后，我们应该将行为抽象如下:

对于不同的实施，我们有:

然后，应该修改`Person`类，开始使用如下策略:

主模块应该控制我们的角色如下:

因此，如您所见，我们现在可以在运行时轻松地在不同的`IWalkStrategy`实现之间切换。

但是，有一点我不喜欢…

![](img/dae42316c04548aad197e34b378e3bf1.png)

# 更好的设计

我不喜欢的是我们的`Person`类仍然有一些行为是`Walk`。是的，我知道它只是一个虚拟的，因为在最后主模块决定了要使用的实际实现，但它仍然是一个行为。

有那么糟糕吗？实际上不是，但是当你的游戏开始成长时，你会发现你的实体被太多的虚拟行为所膨胀。

因此，我认为我们控制自己走路行为的方式应该是不同的。

我认为应该是这样的。

现在`Person`对`Walk`的行为一无所知。它只保持自己的状态。**这里值得一提的是**最好是让整个类不可变，这样它的状态就得到维护，不能被篡改。

而`IWalkStrategy`现在正在对传入的`Person`应用行为。也许把这个接口重新命名为`IPersonWalker`或者别的名字会更好。

那么实现将被修改如下:

并将添加一个新的接口，如下所示:

这个接口代表一个选择器，它能够根据健康状况选择正确的`IWalkStrategy`实现。

这里我们可能会争论`SelectWalkStrategy`方法是否应该期望 health 或`Person`对象作为输入参数。两者都可行，但这取决于逻辑有多复杂。

如果您发现决策将依赖于更多的属性，如`Age`和`Health`，那么使用`Person`作为参数，或者甚至为决策标准创建一个新的包装器可能是好的。

然后，为了实现，我们可以这样:

对于主模块，我们应该这样:

您可以注意到，现在`Walk`行为与`Person`状态完全分离。

此外，`IWalkStrategySelector`本身可以有多个实现，我们可以在它们之间轻松切换。

![](img/dae42316c04548aad197e34b378e3bf1.png)![](img/747863da04d6adb0a55e01c3a34fe99a.png)

照片由[艾米丽·莫特](https://unsplash.com/@emilymorter?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 下一步是什么

现在你明白**战略设计模式**是什么了。然而，这并不是故事的结尾。

你需要在互联网上搜索更多关于**战略设计模式**及其用法的文章和教程。这会帮助你更好地理解它。

最后，我希望你觉得读这个故事和我写它一样有趣。

![](img/dae42316c04548aad197e34b378e3bf1.png)

# 希望这些内容对你有用。如果您想支持:

如果您还不是**中型**会员，您可以使用 [**我的推荐链接**](https://medium.com/@eng_ahmed.tarek/membership) ，这样我就可以从**中型**中获得您的一部分费用，您无需支付任何额外费用。订阅
[**我的简讯**](https://medium.com/subscribe/@eng_ahmed.tarek) 将最佳实践、教程、提示、技巧和许多其他很酷的东西直接发送到您的收件箱。

![](img/dae42316c04548aad197e34b378e3bf1.png)

# 其他资源

这些是你可能会发现有用的其他资源。

[](/when-implementations-affect-abstractions-1bb2adc808d1) [## 当实现影响抽象时

### 关于实现的知识会影响抽象设计吗？

levelup.gitconnected.com](/when-implementations-affect-abstractions-1bb2adc808d1) [](/mediator-design-pattern-in-net-c-e1bfcc96789d) [## 中介设计模式。NET C#

### 中了解中介器设计模式。NET C#与代码示例。

levelup.gitconnected.com](/mediator-design-pattern-in-net-c-e1bfcc96789d) ![](img/dae42316c04548aad197e34b378e3bf1.png)