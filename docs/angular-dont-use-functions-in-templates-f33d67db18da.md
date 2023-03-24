# 不要在 Angular 中绑定方法！

> 原文：<https://levelup.gitconnected.com/angular-dont-use-functions-in-templates-f33d67db18da>

在这篇简短的博文中，我将向您展示 Angular 的一个核心概念，每个 Angular 开发人员都应该牢记这一概念，以便生成良好的高性能代码。

![](img/070f7a1e019a8c108ccc5509f8b10471.png)

罗斯·芬登在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

如果您刚开始使用 Angular，并且还不太熟悉变化检测等概念，您的代码可能看起来有点像这样:

你已经发现这段代码的问题了吗？它是模板中的函数。尽管它们没有任何副作用并且非常简单，但是它们仍然是不好的，仅仅因为它们被用在了模板中。其原因被称为**变化检测**。

## 变化检测

> 角度变化检测是一个内置的框架特性，它可以确保组件数据与其 HTML 模板视图之间的自动同步。

因此，换句话说，变化检测是一种机制，它决定组件的模板是否必须重新呈现，因为某些东西发生了变化。这可能是鼠标事件、HTTP 请求或应用程序中任何其他类型的状态变化。因此，变化检测是一件神圣的事情，它允许我们绑定数据，并在组件中发生变化时自动更新模板中的数据。

但是要注意一个陷阱:**函数**。

默认情况下，Angular 中的函数不是纯函数，因此每当触发变化检测时，变化检测必须检查函数输出是否发生了变化。这意味着，如果您更改了根组件中的一个值，实际上并不会影响子组件——并且在该子组件中有一个函数绑定——更改检测仍然会检查该函数。如果你经常绑定到一个函数或者只是做了很多修改，那显然会导致性能的大幅下降。

## 重构

让我们从重构 *getSomeText()* 函数开始。当然，我们可以直接绑定到变量 *someText* 上，但是我们必须以某种方式追加额外的文本。这是一个使用**纯管道**的好场景。这个管道将转换 *someText* ，但是只有当 *someText* 实际上被改变时，因为 Angular 知道这个管道只依赖于它的输入，因此如果输入相同，它将总是产生相同的输出(这基本上就是纯函数的意思)。注意，在管道装饰器中指定 *pure: true* 参数很重要。

下一个重构: *getCurrentTime()* 。这太简单了。只需移除函数，直接绑定到变量即可。

但我所做的有点不同，因为我想得更远。我为 *currentTime* 创建了一个 *getter/setter* ，并在 setter 中调用了 *getClass()* 函数，该函数仅在 currentTime 被设置时确定 div 的类。

或者，您也可以实现 *ngOnChanges()* 函数，但我是 setter 函数的拥护者，因为我相信它们更干净，因为您不会最终编写一个真正大的 *ngOnChanges()* 函数。

## 结论

只是不要在你的模板中使用函数——没有一个用例是好的。如果可能的话，只使用变量，如果你有一个更复杂的场景，我相信你可以使用 RxJs 和管道来实现。您可以在我的 GitHub 上找到完整的样本:

[https://github . com/HaasStefan/Angular _ Refactoring _ Change _ Detection](https://github.com/HaasStefan/Angular_Refactoring_Change_Detection)