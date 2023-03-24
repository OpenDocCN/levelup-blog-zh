# 如何用反应测试库编写单元测试

> 原文：<https://levelup.gitconnected.com/how-to-write-unit-tests-with-react-testing-library-d9624fd2b707>

使用 testing-library/react v10 进行简单的隔离测试。

![](img/62241561c8963237745d0ca41f31007d.png)

[疾控中心](https://unsplash.com/@cdc?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

因此，您开发了一个 React 应用程序，但是您仍然对其功能和可预测性没有 100%的信心。

或者你实现了一个华丽的功能，但随后 bug 已经开始出现。你不知道可能是什么原因。很沮丧，对吧？

这两种情况是单元测试重要的原因。

# 设置我们的应用程序

我想在 Countries 应用程序上演示单元测试，你可以[从 GitHub](https://github.com/Dromediansk/countries-app-blog/tree/testing) 中克隆它，然后一起编码。

为了获得相同的结果，我建议您使用以下依赖版本(或更新版本):

*   反应— 16.13.1
*   反应脚本— 3.4.1

或者如果你想完全从头开始开发这个应用程序，你可以从阅读下面的文章开始:

[](https://medium.com/better-programming/some-best-practices-for-building-a-react-app-with-hooks-d6157494f5c1) [## 用钩子构建 React 应用程序的一些最佳实践

### 从头开始构建应用程序时，要记住一些方便的提示

medium.com](https://medium.com/better-programming/some-best-practices-for-building-a-react-app-with-hooks-d6157494f5c1) 

## 安装 RTL

首先，我们需要安装 React-testing-library (RTL)和 jest-dom，以便使用[自定义 jest 匹配器](https://github.com/testing-library/jest-dom):

```
npm install --save-dev @testing-library/react @testing-library/jest-dom
```

为了实际使用定制匹配器，我们需要导入它们。在`src`文件夹中，我们新建一个文件`setupTests.js`:

```
import "@testing-library/jest-dom/extend-expect";
```

每个安装文件将在每个测试文件中运行一次。你可以在 [jest 文档](https://jestjs.io/docs/en/configuration.html#setupfiles-array)中读到更多。

# 快照测试

每当您希望确保您的 UI 或代码不会意外更改时，快照测试非常有用。

快照测试的工作原理是，它将组件与生成的蓝图(快照)进行比较。如果它们不匹配，测试将失败。

例如，让我们有一个简单的组件`CountryCard.js`，里面没有任何逻辑:

CountryCard.js

在`CountryCard`文件夹中，我们创建一个新文件`CountryCard.test.js`，并编写我们的第一个测试:

*   在运行任何测试之前，我们需要定义一个具有所需属性的国家对象，在我们的例子中是`stubbedCountry`。属性值应该具有与预期相同的属性类型。
*   在第 15 行，我们用`render()`渲染我们的组件，并将 stubbed props 传递给组件。
*   我们期望匹配快照(如果第一次运行，将会生成快照)。

## 真正的好处是什么？

基于这个例子，看起来快照测试不是很有用，对吗？我们模仿道具，传递给组件，然后期望它总是匹配。为什么他们不应该，无论如何？

事情是这样的，`CountryCard`期望只有一个道具，它将显示内容。这个组件没有其他可能的版本。

因此，快照测试只有在以下情况下才有意义:

*   由于道具不同，组件的内容也在变化。在这种情况下，我们可以为组件的所有可能版本编写一个快照测试。
*   我们希望在开发过程中识别组件的变化。

# 测试表格

开发 web 应用程序时，表单是最重要的主题之一，让表单可预测是至关重要的。

`AdvancedFilter.js`由基本输入组成，其值由上层组件传递。此外，事件处理程序作为 prop — `handleChangeValue`传递给组件。

那么我们应该在这里测试什么呢？

如上所述，单元测试应该尽可能地隔离。重要的是测试组件的行为并得到预期的结果。

以下是我们想要测试的内容:

1.  基于给定的属性，它应该显示相应的输入值
2.  当用户开始输入时，它应该调用一个事件处理程序并显示更新后的值

这才有资格入住两次测试！让我们测试第一种情况:

*   我们可以在`describe`块中分组多个测试(从第 5 行开始)。
*   在测试开始时创建模拟的功能和道具是一个很好的实践。在我们的例子中，我们需要模仿所需的道具`searchValue`和`handleChangeValue`。
*   在测试中，我们首先需要用适当的道具来呈现我们测试的组件。在 RTL 我们使用`render()`功能。
*   使用对象析构，我们可以提取我们将要使用的方法——比如选择器方法`getByTestId`。
*   最后，我们(最初)期望输入值是空字符串。在`expect`中，我们需要传递一些选择器，例如通过文本、标签文本、测试 id 等。您可以在 RTL 的[查询中了解所有可用选项。](https://testing-library.com/docs/dom-testing-library/api-queries)

## 模拟事件

当然，在表单中测试初始值是不够的。我们可以使用 RTL 的`fireEvent`来模拟用户行为:

在这个测试中发生了几件事:

*   我们首先定义新更新的`searchValue` prop，当用户在输入中写东西时，它将被传递给组件(第 5 行)。
*   我们用初始值来呈现组件。
*   onChange 事件可以根据 [RTL 文档](https://testing-library.com/docs/dom-testing-library/api-events)用`fireEvent.change`模拟。当我们对组件进行更新时，不要忘记将它包装到`act()`块中。
*   更改输入的值后，我们需要模拟 r [e-rendering 同一个组件用不同的道具](https://testing-library.com/docs/marko-testing-library/api#rerender)(第 19 行)。
*   在断言中，我们检查输入的值以及被调用的事件处理程序。

**注意:**请记住，我们没有测试事件处理程序本身。这必须在定义组件的地方完成——在我们的例子中是在`CountriesContainer.js`中。

# 测试获取服务

在我们的应用中，我们使用一个定制的钩子来获取国家，但是在通常的场景中，你会有一些调用服务:

获取数据通常被嘲笑，我们应该隔离测试我们服务的行为。

## 1.状态代码为 200 的场景

*   重要的是在开始时模拟 fetch，在这里我们用给定的状态和数据返回一个承诺(第 17–25 行)。
*   断言结果。

## 2.状态代码为 500 的场景

*   与前面的测试类似，我们需要模拟状态为 500 的 fetch。
*   我们期望数据不存在

# 测试提取的数据

真正的魔力发生在`CountriesContainer`中，我们获取数据，声明表单的事件处理程序，并显示国家。我认为这个组件是最重要的测试。

以下是我们实际需要检查的内容:

1.  获取数据时是否显示加载微调器？
2.  提取后数据是否显示在视图中？
3.  如果数据提取不成功，是否显示错误通知？
4.  国家是否按输入值过滤？

## 测试准备

首先，我们实际上需要模拟定制钩子来获取数据和数据本身。在`CountriesContainer.test.js`我们可以这样做:

*   我们可以使用 [Jest manual mocks](https://jestjs.io/docs/en/manual-mocks) (第 6 行)模仿任何模块。
*   `stubbedCountries`是(模拟)提取后将显示的数据。

现在，让我们深入编写测试本身！

## 1.检查装载旋转器

有趣的是，我们首先需要模拟数据获取。因为我们使用自定义钩子来实现这个目的，所以我们必须模拟函数的返回值(第 2–6 行)。

如果您直接使用 Axios 或 fetchAPI，您需要模拟这些模块。检查 RTL 的[异步公用程序。](https://testing-library.com/docs/dom-testing-library/api-async)

之后，我们可以渲染组件，并期望显示加载微调器。

## 2.正在显示检查数据

这其实很简单。我们模拟钩子的返回值，其中已经存在数据。

我们可以查询标签，它们在子组件中。有时候是必然的。例如，通过`data-testid`选择国家卡:

```
// CountryCard.js<div className="country-card" **data-testid={`${country.name}-card`}**>
...
```

## 3.检查错误通知

这里我们只是改变钩子的返回值并检查错误信息是否存在:

## 4.检查过滤国家

在获取数据之后，我们需要在输入上模拟 onChange 事件。我将演示如何根据国家名称进行过滤，但是可以随意添加更多的测试。

我们正在模拟在姓名输入中键入“Slov ”,预计视图中将只显示斯洛伐克国家。在这个测试中，我们还覆盖了组件中声明的事件处理程序。

# 接下来去哪里

您可以在 [GitHub repo](https://github.com/Dromediansk/countries-app-blog/tree/unit-testing) 中检查所有测试。

有了这几个测试例子，我们几乎涵盖了单元测试的核心。我鼓励你写更多的测试。这是一个需要练习的话题，所以你可以决定*实际测试什么*。

如果你对学习自动化/E2E 测试感兴趣，我写了一篇关于如何用 Cypress 编写测试的指南:

[](https://medium.com/better-programming/react-testing-get-started-with-cypress-io-a19b6eb6332a) [## React 测试:Cypress.io 入门

### 用最少的努力编写有意义的端到端测试

medium.com](https://medium.com/better-programming/react-testing-get-started-with-cypress-io-a19b6eb6332a) 

感谢阅读！