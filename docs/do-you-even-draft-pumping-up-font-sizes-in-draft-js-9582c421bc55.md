# 你会征兵吗？—在 Draft.js 中提升字体大小

> 原文：<https://levelup.gitconnected.com/do-you-even-draft-pumping-up-font-sizes-in-draft-js-9582c421bc55>

![](img/1daeb4c9b214120c8bfec60930dcb681.png)

我最近发表了一篇名为 [This Nest Is A Bit Drafty](/this-nest-is-a-bit-drafty-709ef8b79073) 的文章，该文章介绍了如何在 Draft.js 文本编辑器中显示有序和无序列表以及嵌套列表。在本文中，我将继续讨论 Draft.js 的一些特性，我发现这些特性很难实现，我将演示如何设计一个下拉菜单，允许用户在文本编辑器中更改字体大小。

首先，我们将创建一个`TextEditor`组件，它将按照 Draft.js 文档概述的方式呈现 Draft.js `Editor`组件[。我们还将在`TextEditor`组件中呈现一个自定义的`EditorToolbar`组件，并将它作为道具传递给`editorState`和`setEditorState`:](https://draftjs.org/docs/getting-started#usage)

现在我们将向`EditorToolbar`添加一个下拉菜单，它将显示选择不同字体大小的选项。

需要记住的一件重要事情是，当选择不同的字体大小时，我们不希望光标在文本编辑器上失去焦点。正如我在上一篇文章中解释的，我们可以通过使用一个`onMouseDown`事件并在其处理程序中调用`preventDefault`来防止这种情况发生。出于这个原因，我们不能在下拉菜单中使用`select`元素，因为`preventDefault`会阻止菜单打开。相反，我们必须创建一个自定义下拉菜单。

我们的下拉菜单将由一个`button`和一个包含所有指定字体大小的菜单`div`组成。菜单将根据按钮是否被按下而有条件地呈现。为此，我们将把`isShowingFontSizeMenu`添加到组件状态，并将其值设置为`false`。然后，我们将向调用`preventDefault`的`button`添加一个`onMousedown`事件，然后将`isShowingFontSizeMenu`的值设置为其当前值的倒数。

接下来，我们将通过映射一个以像素为单位指定字体大小的数字数组，为下拉菜单中显示的每个字体大小创建`div`来为下拉菜单添加字体大小选项。

最后，我们需要为菜单添加一些自定义 CSS 来正确显示。

当所有这些都完成后，`EditorToolbar`将看起来像这样:

现在是时候让文本编辑器能够改变字体大小了。

当实现这个特性时，我发现为了保持一致性和避免错误，最好使用`[draft-js-custom-styles](https://github.com/webdeveloperpr/draft-js-custom-styles)`节点模块。让我们在终端中运行`npm i draft-js-custom-styles`，并将模块导入`TextEditor`。

`draft-js-custom-styles`的用法有些非传统。导入模块后，我们需要声明我们将在模块中使用的函数，并将一组 CSS 属性作为参数传递给模块。对于我们的应用程序，我们将使用`styles`和`customStyleFn`函数，因为我们只改变字体大小，所以我们将传入一个只包含`'font-size'`属性的数组。接下来，我们需要将[传](https://draftjs.org/docs/api-reference-editor.html#customstylefn) `[customStyleFn](https://draftjs.org/docs/api-reference-editor.html#customstylefn)` [到](https://draftjs.org/docs/api-reference-editor.html#customstylefn) `[Editor](https://draftjs.org/docs/api-reference-editor.html#customstylefn)`和`styles`到`EditorToolbar`作为道具。完成后，TextEditor 将如下所示:

现在让我们在`EditorToolbar`中构建一个名为`setFontSize`的函数。每当从字体大小下拉列表中选择一个字体大小选项时，都会调用这个函数。

被调用时，`setFontSize`会做几件事:

首先它将`preventDefault`以保持焦点在`Editor`内。

然后，它将声明一个编辑器状态，从当前选择中删除所有字体大小属性。这样做是因为我们不想覆盖当前的字体大小属性，而是想用一个新的字体大小来替换它，以确保在`Editor`中的任何一个地方都不会声明多个字体大小。

接下来，它将更新`editorState`以使用选定的字体大小。

最后，它将设置`isShowingFontSizeMenu`为假，这将关闭下拉菜单。

这个完成后，`EditorToolbar`会变成这样:

至此，我们已经成功地在 Draft.js 文本编辑器中实现了字体大小功能！

对于感兴趣的人，这里有一个指向本教程资源库的链接。

[](https://github.com/Lige-w/draft-js-font-sizes) [## 李哥-w/draft-js-font-size

### 如何在 Draft.js 中制作字体大小选择菜单

github.com](https://github.com/Lige-w/draft-js-font-sizes)