# 是时候取代谷歌移动分析了

> 原文：<https://levelup.gitconnected.com/it-is-time-to-replace-google-analytics-mobile-5333c6a5189>

> 为什么？我喜欢！

![](img/1a0cc17adfd9df423345513f421852a6.png)

这可能是你看到标题时的反应。虽然许多人可能会反对这一点，但我倾向于认为这很棒。

不幸的是，谷歌移动分析[即将停产](https://support.google.com/firebase/answer/9167112?hl=en)。没错。是时候转向一些新工具了。在这篇博文中，我将介绍一些你现在可以使用的替代方法。向新工具的过渡也应该很容易。唯一的问题是数据保留，因为谷歌分析将在 10 月底停止接受数据，你的统计数据将在 1 月消失。所以现在是时候采取行动，赶在关闭之前。我们都知道，让所有用户使用新版本的应用程序需要一段时间，所以这也给了他们时间进行迁移。

## 面向未来的应用

如果这还不能很好地说明为什么你应该让你的应用程序经得起未来考验，我不知道什么才是。

谷歌分析一直是图书馆的经典例子之一，人们将它直接集成到每个屏幕的应用程序中。然而，任何曾经和我谈论它的人，或者问我如何实现它的人，我总是建议把它放在一个库(或“lib”)文件中，只有一个特定的原因:当迁移到另一个库或不同的 Google Analytics 模块时，你会很高兴所有的集成都在一个地方。

所以我现在的建议是，无论你最终选择什么库，在`lib`文件夹中创建一个文件，并在你的应用程序中的任何地方使用这个文件。并尝试“通用化”这个库，以便它可以以多种方式使用。

这是您的`/app/lib/analytics.js`文件可能的样子，或者至少包含

```
const module = require('analytics.module');
const lastScreen = false;function logScreen(screenName) {
  // ignore if screenName is the same as the last
  if (screenName == lastScreen) return;   
  lastScreen = screenName;
  module.logScreenView({name: screenName});
}module.exports = {
  logScreen: logScreen
}
```

现在，无论你想在你的应用程序中的什么地方记录屏幕视图，你只需打电话给`require('/analytics').logScreen("screenName");`

非常容易实现！并且非常容易迁移到不同的库，或者挂接一个崩溃分析模块来记录面包屑。在这种情况下，您只需要更改分析库。

我在上面的例子中已经内置的另一个好处是，不再有双重日志记录。有时，在某些情况下，由于某种原因，事件或屏幕视图被记录了两次，这可能是一种痛苦。不需要建立任何依赖于实现的定制解决方案，这将完全根除它。

当然，当存在双日志有效的情况时，只需添加一个“重置”功能来删除最后一个日志，或者在`function`中添加一个`boolean`来忽略`lastScreen`变量。

但是让我们看看你现在可以使用的一些替代方案

## 选项 1: Firebase

Firebase 是一个很好的迁移选择。谷歌正在将所有与移动相关的东西迁移到那里，坚持使用生态系统是有意义的。社区成员 Hans Knö chel 为 Titanium 创建了一个模块:[https://github.com/hansemannn/titanium-firebase](https://github.com/hansemannn/titanium-firebase)

实现真的很容易。只需按照上述模块的指导，安装核心模块，然后是分析模块，你应该可以开始了。在 Firebase 上创建一个项目也非常容易！

**额外的好处**:有更多的 firebase 组件可以与上述模块一起使用，如推送通知、崩溃分析等。

不利方面:你仍然被锁定在谷歌的生态系统中，他们也不赞成谷歌分析。虽然我不指望他们在未来几年内放弃 Firebase，但也许他们会再次这么做？

## 选项 2:钛分析

[Titanium Analytics](https://docs.appcelerator.com/platform/latest/#!/api/Titanium.Analytics) 非常适合事件跟踪。在 Axway 仪表盘中，您可以看到显示的所有分析，您还可以根据日期和版本进行过滤，以及配置事件漏斗来准确分析您的用户正在做什么

使用 Titanium Analytics 的好处是它已经是内置的，并且可以很好地跟踪会话、安装、更新和其他开箱即用的内容，除了在`tiapp.xml`中启用分析之外，不需要配置任何其他内容，这在新应用程序中是默认启用的。

如果你考虑在一个更大的应用程序中使用 Titanium Analytics，我建议你将帐户升级到 Pro，并在捆绑包中添加 Crash Analytics。这将使你有足够的空间来分析事件，并让你了解应用程序崩溃的频率。当然，这里的缺点是它不是免费的。但在我看来，如果你是一个严肃的钛开发者，pro 是值得的，因为它将提供更多的好处，而不仅仅是分析。

查看文档了解更多信息:[https://docs.appcelerator.com/platform/latest/#!/API/钛。分析](https://docs.appcelerator.com/platform/latest/#!/api/Titanium.Analytics)

## **选项 3:自己卷**

有很多方法可以跟踪统计数据，如果您想让一切都在您的控制之下，一个好方法是使用开源解决方案，或自定义(取决于您的需求)。这将允许您完全访问所有原始数据，并获得您想要的确切功能。

您可以使用开源分析解决方案，并通过 http 调用将它们连接起来。这都是你的选择！但是这里有一个很大的缺点，你需要为自己维护它！

## 结论

拜拜谷歌分析！你好新工具！我上面说的三个选项都很棒。只有当你真的需要它的时候，你才值得拥有它。使用钛分析是最直接的解决方案，因为它已经内置，不需要任何配置。只要记住将逻辑放在博客文章开头提到的 lib 文件中。

如果你现在已经在使用谷歌分析或者已经在使用 Firebase，Firebase 可能是最好的解决方案。

无论你选择什么，谷歌分析都不能再用了，所以请记住这一点