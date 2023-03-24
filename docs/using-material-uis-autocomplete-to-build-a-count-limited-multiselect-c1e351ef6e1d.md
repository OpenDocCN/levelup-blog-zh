# 使用 Material UI 的自动完成功能构建计数受限的多选

> 原文：<https://levelup.gitconnected.com/using-material-uis-autocomplete-to-build-a-count-limited-multiselect-c1e351ef6e1d>

![](img/1402488e10b2c09efc2576f58504133d.png)

[粘土银行](https://unsplash.com/@claybanks?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

[Material UI 的 Autocomplete](https://material-ui.com/components/autocomplete/) 是一个有用的工具，它为应用程序的用户提供了从值列表中选择选项的能力。例如，如果您的应用程序处理股票市场，自动完成组件可以允许用户键入股票的名称/股票代号，同时该组件从可用选项中提供建议。这实际上是我在单选和多选表单中使用 Autocomplete 组件时的确切用例。我在使用该组件时遇到的一个问题是能够限制用户可以选择的数量。经过一番搜索和绞尽脑汁之后，我找到了一个相当不错的解决方案，我已经把它变成了一个独立的组件，可以很容易地在 React 应用程序中使用。在这篇文章中，我将详细介绍该组件的创建和使用。

# 项目受限的自动完成功能

该组件创建起来相当简单。组件需要来自父组件的一些信息才能开始:

正如这里所看到的，组件需要知道可用的选项(options)、当前或默认的选定项(selectedOptions)、最大选择数(maxSelections)、组件是否正在加载(loading)、是否有错误消息要显示(errorText)、TextInput 组件的标签(label)，并且还可以从父组件获得一个回调以处理被选择的选项(setSelectedOptions)。然后用它们来构建自动完成组件。

如上所示，允许用户进行“maxSelections”数量的选择。一旦达到此数量，组件的选项将被禁用。另外，请注意，选择选项后，它们将被禁用，因此不能再在下拉列表中单击它们。从 *getOptionDisabled* 回调中移除*" | | selected options . includes(option)"*逻辑将使其单击选中的选项将取消选中它们。

在您的使用案例中，可能不保证加载功能。在我的特定情况下，股票报价机是通过这个组件选择的，一旦用户完成选择，就会获取与报价机相关的数据。设置为 true 的 loading 属性在组件中显示一个循环加载图标，向用户显示正在获取数据。

# 全组件代码

# 使用组件

下面是一个在 React 应用中使用该组件的小例子，以及一些结果截图。

这将创建一个自动完成组件，允许用户从可用选项中选择多达 10 个股票报价机。

![](img/19a15232f4e11d8d668b0685cbedaced.png)

当所有 10 个项目都被选择时，显示一个错误

![](img/f960f9e7dd9f504b5ef7771089feb661.png)

发生这种情况时，不允许再进行选择，因为所有选项都被禁用。

![](img/4c7b9a63462da1f560036c3e60c03ef6.png)

# 结论

上面显示的是一个简单但在我看来非常有用的 React 应用的 Material UI 自动完成组件的扩展。该组件允许用户从预定的项目列表中进行选择，直到选择了一定数量的选项，此时这些选项被禁用。仅禁用选项允许用户继续与自动完成组件交互(例如，移除选择)，同时不允许他们选择太多项目。希望你们中的一些人和我一样觉得这很有用。