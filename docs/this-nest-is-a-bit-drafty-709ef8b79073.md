# 这个嵌套有点通风——利用了 Draft.js 中的嵌套列表

> 原文：<https://levelup.gitconnected.com/this-nest-is-a-bit-drafty-709ef8b79073>

![](img/b52e12e2b80ab6d1dc2f5f2c53da10b3.png)

[Draft.js](https://draftjs.org/) 是在 React 应用程序中创建富文本编辑器的一个非常好的工具，但是理解如何释放其全部潜力是一项具有挑战性的任务。

我最近设计了一个应用程序，可以帮助用户引用研究项目的参考资料，并允许他们在这些参考资料上做笔记和存储笔记。我想让笔记功能尽可能无缝，我知道允许用户使用嵌套列表是这样做的关键。然而，事实证明这项任务比我预想的要困难得多。虽然 Draft.js 的文档说明了[它提供了对嵌套列表的支持](https://draftjs.org/docs/advanced-topics-nested-lists)，但是对于如何实现这一点却没有给出多少提示。

在本文中，我希望通过演示如何为 Draft.js 文本编辑器创建有序和无序列表切换，以及如何在按下 Tab 键时赋予这些列表嵌套的能力，来阐明 Draft.js 的这一方面。

我们将从设置一个基本的 Draft.js 编辑器开始。由于 [Draft.js 文档很好地解释了如何进行这个](https://draftjs.org/docs/getting-started)，所以我不会详细介绍这一步。下面是我们的初始设置:

下一步是为有序列表和无序列表创建切换。为此，我们将创建另一个名为`EditorToolbar`的组件，并将其呈现在`TextEditor`组件中。接下来，我们将在工具栏中添加按钮来切换列表。由于`EditorToolbar`将会更换`editorState`，我们需要将`editorState`和`setEditorState`作为道具传递给`EditorToolbar`。完成后，我们的应用程序将如下所示:

现在我们的按钮需要切换列表样式的能力。为了帮助完成这项任务，Draft.js 有一个名为`RichUtils`的模块，它提供了向编辑器添加文本样式的功能。它提供的一个名为`toggleBlockType`的函数将当前的`editorState`更改为指定的 html 元素块类型(`<ul>` `<ol>` `<h1>` `<blockquote>`等)。).让我们在`EditorToolbar`中导入`RichUtils`。

之后，我们将构建一个名为`toggleBlockType`的函数，并将`onMouseDown`事件处理程序添加到按钮中。这些事件处理器将调用`toggleBlockType`，并向其传递事件和指定的块类型(或者`'ordered-list-item'`或者`'unordered-list-item'`)。

默认情况下，当按下按钮时，文本编辑器中的光标将从文本编辑器中消失，按钮将被选中。这就是为什么我们对按钮使用`onMouseDown`而不是`onClick`事件。`onMouseDown`允许使用`preventDefault`，通过调用`preventDefault`光标将不再选择按钮。相反，它将保留在文本编辑器中的同一位置。

现在`EditorToolbar`看起来是这样的:

如果单击按钮，可以看到编辑器将显示指定的块类型。

现在我们需要添加当 Tab 键被按下时嵌套列表的功能。

Draft.js `Editor`有一个名为`onTab`的属性，当 Tab 键被按下时，它将允许我们执行指定的功能。我们将以与任何 React 事件处理程序相同的方式使用它。我们将在名为`handleTab`的`TextEditor`组件中创建一个事件处理函数，并将其传递给`onTab` prop。

默认情况下，当按下 Tab 键时，光标将选择下一个交互式元素。在填写表单时，这是一个很有用的默认设置，但是对于做笔记来说，这一点帮助都没有。这就是为什么我们必须在我们的`handleTab`函数中使用`preventDefault`。

接下来，我们将把`RichUtils`导入到我们的`TextEditor`中。`RichUtils`还有一个`[onTab](https://draftjs.org/docs/api-reference-rich-utils#ontab)`函数，它接受事件`editorState`和一个间隔深度的整数。我们将把`editorState`设置为这个函数的返回值，也就是当前的`editorState`，在已经显示的列表中嵌套了一个添加的列表。然后我们需要返回字符串`'handled'`，这样编辑器就知道这个键盘命令已经被处理了，不需要做其他任何事情。

最后，为了在按下 Tab 键时显示嵌套，我们需要导入 Draft.js 默认 CSS 库。完成所有这些后，我们的`TextEditor`看起来会像这样:

就是这样！我们的文本编辑器具有嵌套列表功能。您可以通过单击其中一个列表开关并按 Tab 键来测试它。您也可以通过按 Shift + Tab 来删除嵌套。

如果您感兴趣，您可以在此处查看该应用程序的存储库:

 [## 李哥-w/draft js-列表

### Draft.js 嵌套列表

github.com](https://github.com/Lige-w/draftjs-lists)