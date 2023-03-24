# 用 JavaScript 创建可观察主题的正确方法

> 原文：<https://levelup.gitconnected.com/the-right-way-to-create-observable-subjects-in-javascript-2c372cc22b2b>

## 提示和技巧

## 关于如何在 JavaScript 对象中定义主题的最佳实践。

![](img/44225991f6ec6d4b10d0e3e24ce462cc.png)

罗伯特·鲁杰罗在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在处理您的酷 JavaScript 项目时，您可能需要实现一个管理数据流的服务。在这种情况下，你首先想到的是使用**观察对象**或**主题**。

您可以使用像 Rxjs 这样的库来实现它，或者您甚至可以自己实现它，就像我之前在我的文章 [**如何使用普通 JavaScript 中的 Observables 中展示的那样。没有使用框架，只是纯粹的普通 JavaScript**](https://javascript.plainenglish.io/how-to-use-observables-with-vanilla-javascript-aca40a7590ff?sk=21af550f3c383951c87c208da33226b6) 。

无论哪种方式，最后你都需要在你的服务对象中包装一个**主题**。然而，这样做的方法不止一种。

在本文中，我们将了解如何实现这一点的最佳实践。

[](https://medium.com/subscribe/@eng_ahmed.tarek) [## 订阅艾哈迈德的时事通讯？

### 订阅艾哈迈德的时事通讯📰直接获得最佳实践、教程、提示、技巧和许多其他很酷的东西…

medium.com](https://medium.com/subscribe/@eng_ahmed.tarek) ![](img/5c63acf00277c662fb96b2ff7fafea0e.png)

克里斯·里德在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 编码时间到了

我们来举个简单的例子。在我们的解决方案中，我们有一个**认证服务**，它处理登录、注销、获取当前登录用户等。

我们还有一些模块**和**，它们做一些有趣的事情，需要在某个时候了解登录的用户。

我们将通过简单的步骤来实现这个解决方案，并看看它会走向何方。

![](img/f0e72bdef0106eb02e33b29eeb16b4c6.png)

林赛·亨伍德在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在我们的解决方案中，我将使用我自己的**订阅**和**主题**对象的实现，正如我在我的另一篇文章 中所解释的那样。

因此，为了简洁起见，我将把代码放在这里作为快速参考。

## 认证服务

这是我们在这里可以注意到的:

1.  在`AuthenticationService`里面，我们有一个`loggedInUser`，它是一个`Subject`。
2.  使用这个`loggedInUser`，其他模块可以订阅应用于登录用户的变更流。
3.  我们还有`logIn`函数，它执行一些 API 调用，最终设置登录用户并触发`loggedInUser`主题。

## 正在初始化身份验证服务

在主应用程序的某个地方，就这么简单。

`const authenticationService = new AuthenticationService();`

## 一些模块

这是我们在这里可以注意到的:

1.  该模块订阅`authenticationService.loggedInUser` **主题**来获取关于登录用户的更新。
2.  每当更新发生时，就会设置一个名为`loggedInUser`的局部变量。
3.  在`DoSomeStuff`函数中，我们记录局部变量`loggedInUser`的值，该值应该与 **AuthenticationService** 上的最新更新保持同步…还是不同步？
4.  其实不是。问题是，当我们创建一个 **SomeModule** 的实例时， **AuthenticationService** 已经被创建和初始化了。
5.  这意味着在上面代码的第 15 行，局部变量`loggedInUser`仍然是`null`，因为到那时为止，它从未被设置为另一个值。

![](img/d49a4ebc45e100126c7c8f56ba98b5b0.png)

由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 正确的做法

实际上并没有那么复杂。在这种情况下有一种模式，我相信你以前见过。它甚至应用于不同的客户端框架。

说够了，让我们看看代码。

## 增强型认证服务

这是我们在这里可以注意到的:

1.  我们添加了一个名为`snapshot`的新成员，它表示一个对象，其中有一个登录用户作为成员。
2.  我们还更新了`logIn`函数的实现，它首先更新`snapshot`对象，然后做它以前做的事情。

## 增强了一些模块

这是我们在这里可以注意到的:

1.  我们更新了本地变量`loggedInUser`的初始化，以便从`authenticationService.snapshot.loggedInUser`中获取它的值。
2.  因此，在上面代码的第 15 行，本地变量`loggedInUser`的值不会为 null，它将保存登录用户的最新值。

# 希望这些内容对你有用。如果您想支持:

如果您还不是**媒介**会员，您可以使用 [**我的推荐链接**](https://medium.com/@eng_ahmed.tarek/membership) ，这样我可以从**媒介**中获得您的一部分费用，您无需支付任何额外费用。
▎订阅 [**我的简讯**](https://medium.com/subscribe/@eng_ahmed.tarek) 将最佳实践、教程、提示、技巧和许多其他很酷的东西直接发送到您的收件箱。

![](img/209d99e4bb363b45f81e070cb1f35eeb.png)

照片由[格雷瓜尔·贝托](https://unsplash.com/@sirtook?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 其他资源

这些是您可能会喜欢其他资源。

[](/paging-partitioning-main-equations-to-make-it-easy-44fe89d5290b) [## 分页/分区—简化这一过程的主要等式

### 最后，这是您理解分页/分区主要等式并学习如何在代码中应用它们的机会。

levelup.gitconnected.com](/paging-partitioning-main-equations-to-make-it-easy-44fe89d5290b) [](https://javascript.plainenglish.io/how-to-customize-webpages-ui-behavior-using-javascript-userscripts-7b6a090e0135) [## 使用 JavaScript 用户脚本定制网页用户界面/行为

### 即使你不拥有这个网页，你仍然可以附上你的 JavaScript 用户脚本。

javascript.plainenglish.io](https://javascript.plainenglish.io/how-to-customize-webpages-ui-behavior-using-javascript-userscripts-7b6a090e0135) 

最后，希望你觉得读这个故事和我写它一样有趣。