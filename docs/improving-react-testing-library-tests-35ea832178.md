# 改进 React 测试库测试

> 原文：<https://levelup.gitconnected.com/improving-react-testing-library-tests-35ea832178>

![](img/7f3437bc508d834e32629801c2387f32.png)

React 测试库 (RTL)成为测试 React 组件的事实上的标准。专注于从用户的角度进行测试，避免测试中的实现细节是其成功的主要原因。

正确编写的测试不仅可以帮助防止回归或错误代码，而且在 RTL 的情况下，还可以提高组件的可访问性和总体用户体验。在本帖中，我们将看到如何充分利用 RTL 测试。我将提供一个我认为的测试时的良好实践的集合，没有特定的重要性顺序。

# 编写烟雾测试

有时，我们希望进行基本的健全性测试，以确保组件在渲染时不会损坏。假设我们有这个简单的组件:

我们可以通过这样的测试来检查它的渲染是否没有问题:

这对我们的目的很好，但是，我们没有充分利用 RTL 的力量。相反，我们可以这样做:

虽然这是一个非常简单的例子，但是有了这个微小的变化，我们不仅测试了组件在渲染过程中不会中断，而且测试了它有一个名为`List of items`的 header 元素，可以被屏幕阅读器正确访问。

# 默认为`*ByRole`查询

RTL 的强大优势之一是，通过正确的查询，我们不仅可以确保组件按预期工作，还可以确保它们是可访问的。那么，如何确定哪个查询是最好的呢？规则很简单——默认使用`*ByRole`查询。像大多数规则一样，它也有例外，因为不是所有的 HTML 元素都有默认角色。HTML 元素的默认角色列表可以在[w3.org](https://www.w3.org/TR/html-aria/#docconformance)找到。

让我们考虑以下表单组件，改编自[之前的教程](https://medium.com/p/3beb88eb467d):

我们测试的方法是通过表单元素模拟输入数据，提交表单，然后验证`saveData` prop 是否收到了我们输入的数据。我们可以把它分成 3 个步骤:

1.  为我们要测试的字段输入文本(或单击复选框)
2.  点击`Sign up`按钮
3.  验证用我们输入的数据调用了`saveData`。

这个工作流正是用户与我们的表单交互的方式(除了他们可能不会以完全相同的方式检查保存的数据)。让我们从在第一个输入字段中输入姓名开始。我们看到它有一个`Enter your name`占位符，为什么不使用它呢？

这是可行的，但我们可以做得更好。首先，这可能会助长使用占位符文本作为标签的习惯，这不是它们的本意，也不被 W3C WAI 所鼓励。其次，我们在测试时并没有考虑到可访问性。相反，让我们尝试用`getByRole`替换我们的查询。正如文档所说，我们可以通过`textbox`角色匹配类型`text`的输入，但是由于我们在表单中有多个文本框，我们需要比这更具体。幸运的是，查询接受了第二个参数，这是一个 options 对象，在这里我们可以使用`name`属性缩小匹配范围。从[文档](https://testing-library.com/docs/queries/byrole)中我们可以看到，这里的`name`并不是指输入的`name`属性，而是它的[可访问名](https://www.tpgi.com/what-is-an-accessible-name/)。因此，对于输入，可访问名称通常是其标签的文本内容。在我们的表单中，名称输入有一个`Name`标签，所以让我们使用它。

运行测试时，我们得到一个错误:

```
TestingLibraryElementError: Unable to find an accessible element with the role "textbox" and name "Name"
```

下面的帮助文本显示我们的输入没有可访问的名称:

```
Here are the accessible roles:textbox: Name "": 
<input 
  id="name" 
  placeholder="Enter your name" 
/>
```

我们确实有一个输入的标签，那么为什么它不起作用呢？结果是标签需要与输入相关联。为此，标签应该有一个与相关输入的`id`相匹配的`for`属性。看起来我们的输入已经有了一个 id，所以我们只需要给它添加`for`(使用 React 时为`htmlFor`)属性:

现在，输入与其标签正确关联，测试通过。这也为可访问性带来了重大改进。首先，当点击/轻敲标签时，焦点将被传递到相关联的输入。其次，也是最重要的，当输入被聚焦时，屏幕阅读器将读出标签，从而向用户提供一些关于输入的额外信息。这是一个很好的例子，通过切换到`getByRole`，我们不仅提高了测试覆盖率，还为表单组件提供了有价值的可访问性改进。

如果我们再看一下测试，我们会看到`getByText`查询被用于提交按钮。在我看来，`*ByText`应该是最后一招(或者在`*ByTestId`之前倒数第二招)，因为它们最容易坏掉。在我们的测试中，`screen.getByText("Sign up")`会将元素与具有`Sign up`文本内容的文本节点进行匹配。如果我们稍后决定在同一页面上添加一个带有文本“注册”的段落，该元素也将被匹配，测试将中断，因为现在我们有不止一个匹配的元素。当我们使用一般的正则表达式而不是字符串`screen.getByText(/Sign up/i)`进行文本匹配时，情况会变得更糟。这将匹配任何出现的字符串“sign up ”,不管大小写，即使它是一个更长句子的一部分。当然，我们可以修改正则表达式以确保它只匹配这个特定的字符串，但是相反，我们可以使用一个更精确的查询，同时验证我们的表单在`getByRole`查询的帮助下是可访问的。在这种情况下，查询将是`screen.getByRole("button", { name: "Sign up" });`，这次可访问的名称是按钮的实际文本内容。注意，如果我们将`aria-label`添加到按钮上，那么可访问的名称将会是那个`aria-label`的文本内容。最终，更新后的测试如下所示:

# 输入元素的`*ByRole`与`*ByLabelText`

对输入元素使用`*ByRole`查询的目的是通过相关标签匹配输入。使用`*ByLabelText`查询不是更容易吗，因为它们最终实现了相同的目标，而且语法更简单。我不认为使用一个查询和使用另一个查询有很大的区别，然而，在匹配元素时，`*ByRole`比[更健壮](https://testing-library.com/docs/queries/bylabeltext#name)，如果你从`<label>`切换到`aria-label`，它仍然会工作。另一方面，并不是所有类型的输入元素都有默认角色，所以例如对于密码输入，我们将使用`*ByLabelText`查询。

# 使用`userEvent`代替`fireEvent`

在对`Form`组件的测试中，我们使用内置的`fireEvent`来调度 DOM 事件。虽然这在很多情况下都有效，`fireEvent`只是一个在`dispatchEvent` API 之上的轻量级包装器，并不模拟完整的用户交互。另一方面，`userEvent`像用户在浏览器中一样操作 DOM，提供了更可靠的测试体验。它的工作方式也更符合 RTL 的哲学，而且语法更清晰。比较用`userEvent`改写的`Form`的测试:

所有的方法都是异步的，所以我们需要稍微调整一下测试。此外，由于它是一个单独的包，需要通过`npm i -D @testing-library/user-event`安装。请注意，在未来版本中，您将需要通过`const user = userEvent.setup()`设置事件，并从`user`调用它们。关于`userEvent`更深入的介绍可以在 [RTL 文档](https://testing-library.com/docs/user-event/intro)中找到。

# 使用`find*`查询代替`waitFor`

经常有这样的情况，当我们试图匹配的元素在初始渲染时不可用，例如，当我们首先从 API 获取项目，然后显示它们。在这种情况下，我们需要组件在查询之前完成所有的呈现周期。作为一个例子，让我们修改`ListPage`组件来等待项目列表异步加载:

该组件的当前测试版本将不再工作，因为当调用`screen.getByRole`查询时，仅显示加载文本。为了等待组件完成加载，我们可以使用`waitFor`助手:

这是可行的，但是有一个内置异步行为的查询类型及其`findBy*`查询，它是`waitFor`之上的一个包装器。有了它，测试变得更具可读性:

应该注意的是，每个测试块一个`await`调用通常就足够了，因为那时所有的异步动作都已经解决了。所以在上面的例子中，如果我们想额外测试我们在`ItemList`中有 4 个项目，我们不需要使用异步`findBy*`，而是可以求助于`getBy*`。

# 测试元素的消失

这是一个非常极端的情况，但是有时我们想要测试一个元素，它之前存在，在一些异步操作之后已经从 DOM 中移除了。RTL 有一个方便的助手。例如，在`ListItem`组件中，我们可能希望等待`Loading...`文本被移除，而不是列表标题出现:

# 使用 RTL 游乐场

如果你对某些元素的正确查询有困难， [RTL 游乐场](https://testing-playground.com/)会给你很大的帮助。只需粘贴正在测试的组件的 HTML，它会提供方便的建议，告诉你哪些查询适合每个元素。这是非常有价值的，特别是对于复杂的组件，它可能并不总是很明显，哪个查询是最好的使用。

*原载于*[](https://claritydev.net/blog/improving-react-testing-library-tests/)**。**

# *分级编码*

*感谢您成为我们社区的一员！在你离开之前:*

*   *👏为故事鼓掌，跟着作者走👉*
*   *📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容*
*   *🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)*

*🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)*