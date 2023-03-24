# React 高阶元件(HOC)的真实示例

> 原文：<https://levelup.gitconnected.com/real-world-examples-of-higher-order-components-hoc-for-react-871f0d8b39d8>

React 中最强大的模式之一是高阶组件(HOC)。特设的目的是用额外的功能来增强组件(通常是哑组件)。由于在现实生活的应用中，需要在各种*相似类型的组件中重用相同的功能，所以特设考虑到了可重用性。*

![](img/19405fe5f7be8d83dbc716d366e03e86.png)

hoc 非常强大，应该在每个项目中使用

一个非常常见的功能是切换。这可以用在不同的场景中，例如折叠/展开列表、显示/隐藏组件、突出显示/取消突出显示消息以引起用户的注意等。在本文中，我们将重点讨论两个例子，以更好地理解 HOC 是如何工作的，以及如何利用它们。

我们将使用的两个示例是:

1.  在段落和输入之间切换。这将允许用户在编辑和查看文章标题之间切换。
2.  在折叠/展开列表之间切换。

这两种情况都需要相同的切换功能。使用一个特设，我们可以编写一次切换的逻辑，并为相同的组件重用它。

为了实现这个功能，我们只需要保持一个`toggleStatus`键的状态，并有一个切换该状态的功能。因此，特设将如下所示:

(a)它接受组件作为输入，该组件将是需要增强切换功能的组件。

(b)它初始化一个状态，toggleStatus 等于 false，并且

(c)声明一个切换函数，该函数将把`toggleStatus`从假切换到真，反之亦然。

(HOC 将呈现传递的组件，但是它将向其添加两个道具。第一个是`toggle`功能，第二个是来自状态的`toggleStatus`。注意渲染方法中的`this.props`是我们打算以正常方式传递给`PassedComponent`的所有道具。

总的来说，特设委员会的工作非常简单。它以正常方式呈现组件，但用`toggleStatus`键和一个在真/假值之间切换该键的值的函数来赋予它状态。

现在，让我们看看如何在上面提到的两种情况下使用这个 HOC。

1.  ViewEditToggleExample:

然后我们可以通过以下方式调用这个组件:

```
<ViewEditToggleExample title='My first post' />
```

首先要注意的是，我们导出了带有`ViewEditToggleExample`组件作为参数的`withToggle`函数。这意味着当我们调用`ViewEditToggleExample`时，它将从特设中获得额外的道具。

然后我们可以使用这些道具来实现切换功能。正如我们看到的，当`toggleStatus`为真时，我们显示一个输入，如果为假，我们显示一个带有标题的`<p>`标签。

通过将`toggle`函数作为按钮的`onClick`道具，我们可以在单击按钮时在两种状态之间切换。例如，当更新文章标题的值时，可以使用上面的例子。用户将点击切换按钮并插入文章标题的新值。(注意，出于更新的目的，上面的例子是不完整的。我们需要处理输入的变化，还需要处理新值的提交。这可以通过另一个 HOC 来实现，因为处理表单提交在整个应用程序中是可重用的。)

2.CollapseExpandExample 示例

然后我们可以通过以下方式调用这个组件:

```
const list = [
  { id: 1, name: 'Eggs' },
  { id: 2, name: 'Bread' },
]<CollapseExpandExample list={list} />
```

和第一个例子一样，组件通过`toggle`函数和`toggleStatus`变量得到了增强，我们用它们来实现切换功能，在这个例子中是折叠和展开一个购物清单。

通过这两个例子，我们展示了高阶元件的强大功能。我们可以更进一步，向 HOC 添加更多的逻辑，以初始化`toggleStatus`值。如果我们想要初始化扩展的购物清单，这可能会很方便。为了实现这一点，我们需要考虑到特设的第二个参数，如下所示:

现在，如果我们向`withToggle`函数传递第二个参数，我们可以像这样初始化`toggleStatus`状态:

```
const list = [
  { id: 1, name: 'Eggs' },
  { id: 2, name: 'Bread' },
]<CollapseExpandExample list={list} initialToggleStatus={true} />
```

有一种替代方式来编写 HOC，您可能会喜欢:

为了获得相同的结果，我们需要替换组件的默认导出:

```
// CollapseExpandExample.js
export default withToggle(CollapseExpandExample)
```

收件人:

正如你所看到的，主要的区别是道具的过滤(如之前评论中提到的)现在是在这个导出函数上完成的。