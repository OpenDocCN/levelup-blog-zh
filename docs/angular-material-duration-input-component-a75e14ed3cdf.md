# 角度材料持续时间输入组件

> 原文：<https://levelup.gitconnected.com/angular-material-duration-input-component-a75e14ed3cdf>

我创建了这个简单的组件来收集和格式化持续时间输入。我希望这篇教程对你有所帮助😄

![](img/03d037345f07e6815bd6d0c7ded04648.png)

照片由 [Aron 视觉效果](https://unsplash.com/@aronvisuals?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/time?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在进入代码之前，这里有一个组件的快速概述。稍后，我将一步一步地浏览代码。持续时间输入接受三个值:小时、分钟和秒，并将它们组合成一个字符串，格式如下:`hours:minutes:seconds`。

系统会自动检查这些值，以确保它们在有效范围内，例如，分钟和秒钟应该只在 0-59 之间。

该组件有两个输入可用，`durationString`和`disabled`。`durationString`输入双向绑定到组件。您可以传入格式化字符串来填充持续时间输入。输入将自动设置`durationString`变量为一个新的更新的格式化字符串，代表输入的持续时间。禁用输入用于在需要时禁用持续时间输入。现在来看一些代码。

## 持续时间输入 HTML。

持续时间输入由三个独立的输入组成:小时、分钟和秒。每当输入的值发生变化时，就会触发`ngModelChange`方法，该方法用于检查值的有效性。

您可能想知道为什么输入是文本类型而不是数字。这样做的主要原因是我们将在控制器中操纵小时、分钟和秒变量。`ngModelChange`有一个奇怪的行为，它阻止输入值被更新，除非检测到新的对象。因此，我们将在更改变量时将它们设置为新的 string 对象，您将在下一节看到这一点。

## 持续时间输入控制器

从顶部开始，我们首先初始化输入和输出。输出对于双向绑定的工作至关重要。输出的一个关键点是，它必须与您尝试双向绑定的输入同名，后跟“Change”。在我们的例子中，这是`durationStringChange`

因为我们能够将预先格式化的值传递到输入中，所以我们需要解析这个值，并相应地设置我们的小时、分钟和秒。这都是在`ngOnInit`方法中完成的。我们首先解析格式化的`durationString`值，然后设置小时、分钟和秒。

接下来，我们有`check`和`getValidValue`方法来检查小时、分钟和秒的输入值，并确保它们是有效的。`getValidValue`中的方法`/^\d+$/.test(value)`(第 46 行)用于确保传入的值中只有数字。正如我们在 HTML 部分所讨论的，我们使用字符串输入，因为我们需要创建新的字符串对象，以便新的值反映在输入中。

在创建新的字符串对象之前，我们使用`Math.min`和`Math.max`方法来确保我们的值在期望的范围内。使用`Math.max`和`Math.min`的好处是，如果超出范围，我们的值将自动设置为我们的最大值或最小值，从而获得更好的用户体验。

一旦我们有了有效值，我们就创建格式化字符串并更新`durationString`变量。双向绑定发生在第 41 行，我们将更新后的`durationString`值发送回传入的变量。

## 持续时间输入用法

在我们可以在我们的项目中使用新的持续时间输入之前，我们需要在我们的`app.module.ts`中声明它，如上所示。

一旦声明，我们就可以在我们的项目中自由使用它。

我们的持续时间输入可以像任何其他组件一样使用，只需添加它的 HTML 标签并传入我们的输入。同样，这两个输入是`durationString`和`disabled`，但是只需要`durationString`输入就能起作用。您可能还会注意到`durationString`是如何被`[]`和`()`包围的，这是因为它是双向绑定的。现在我们已经添加了持续时间输入，我们能够收集格式良好且有效的持续时间输入。😄

## 额外加分

虽然我在本教程中没有这样做，但是用这个输入创建一个 npm 包会很有趣。因此，如果你想要一个额外的挑战，请随意从你已经创建的一个很酷的组件创建你自己的 npm 包。

我希望本教程是有帮助的，并且你已经学会了一些关于在 angular 中创建组件的东西。您可以在此基础上创建各种令人惊叹的定制输入。