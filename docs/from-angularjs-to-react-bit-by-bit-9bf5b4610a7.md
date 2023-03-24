# 从角度到一点一点的反应

> 原文：<https://levelup.gitconnected.com/from-angularjs-to-react-bit-by-bit-9bf5b4610a7>

![](img/eb2715d7da2bb09283b1b1a81928fe68.png)

> ***总结:*** *将你的应用从 AngularJS 迁移到 React(坦白地说，迁移到任何其他现代框架/库)肯定有好处。如果完全重写不适合你，在本文中，我将带你通过一个简单的方法将 React 引入到 AngularJS 应用程序中。即使您最终使用开源库来桥接 AngularJS 来作出反应，这篇文章也会让您理解它的基本原理。*

想跳过所有解释？直接跳到[完整的工作示例](https://dev.to/kaplona/angularjs-to-react-migration-184g#full-example)。

因此，您决定将应用程序从 AngularJS 切换到 React。很好！因为坦率地说，您应该从那个不再受支持的框架转向其他任何东西。任何现代的框架/库都更有性能，更容易使用，并且有更大的社区。

# 理由

在 [Awesense](https://www.awesense.com/) 我们有两个用例，用 AngularJS 很难实现，但用 React 却非常简单:

1.  **动态内容。**我们希望让用户能够定制他们的仪表板页面。React 元素及其属性只是 JS 类、函数和对象，您不需要做任何特殊的事情就可以将用户配置映射到正确的 UI。
2.  **地图覆盖。Awesense 客户端应用程序是以地图为中心的，我们需要从普通的 JavaScript 呈现各种 UI 元素。有了 React，你可以随时创建根组件，而 AngularJS 被设计成只需引导一次，就可以处理你的应用程序中的所有事情。从 AngularJS 的宇宙中跳进跳出是可能的，但肯定没有 React 中的一行代码优雅。**

完全重写很少是一个好的决定。逐渐迁移使我们能够在平静时期花更多时间在 AngularJS 的技术债务上，并在重要时加快功能开发以支持业务增长，这是一个每个人都满意的良好平衡。

你可以使用像[n react](https://github.com/ngReact/ngReact)、 [react2angular](https://github.com/coatue-oss/react2angular) 、 [angular2react](https://github.com/coatue-oss/angular2react) 这样的库来帮助你完成迁移，但是实现你自己的解决方案只需要很少的代码，而且完全理解它是如何工作的也很好。Awesense 解决方案的灵感来自这个小改进[博客文章](https://tech.small-improvements.com/how-to-migrate-an-angularjs-1-app-to-react/)和他们的开源[例子](https://github.com/sfroestl/angular-react-migration)。

# 初始步骤

为了使过渡更加顺利，你应该首先按照以下步骤准备好 AngularJS 代码库:

*   在同一个文件中定义您的控制器和组件模板，如果您还没有这样做的话。
*   开始使用 AngularJS [组件](https://docs.angularjs.org/guide/component)代替指令。组件提供了生命周期挂钩。虽然 React 和 AngularJS 生命周期方法在组件渲染周期的不同时间被调用，但熟悉这个概念是有益的。
*   将你的组件分成[容器和表示](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)组件。这种关注点的分离使得您的代码更容易管理和重用。
*   拥抱单向数据流架构:停止使用`=`双向绑定，转而使用`<`绑定将输入传递给子组件。将您的子组件视为不会改变传递参数的纯函数。相反，孩子应该通过调用作为输出传递给他们的回调来更新父母的状态。这将使您更好地了解数据如何流经您的应用程序，在哪里更新，以及谁拥有它。

# 成分

我们的策略是从“叶子”表示的组件开始迁移，向上迁移到有状态的组件，最终迁移到在路由中呈现的顶级组件。这样，您就不需要在 React 组件中加载 AngularJS 代码，也不需要在最后处理路由。

## 简单组件

首先，您需要一种在现有 AngularJS 代码中使用 React 组件的方法。我不会讨论如何在 React 组件中使用 AngularJS 组件，因为我们的策略不需要这样做，我们的最终目标是无论如何都不使用 AngularJS。

创建一个简单的 React 组件:

一个等效的 AngularJS 组件如下所示:

因此，我们需要一个 helper 函数，将我们的 React 组件包装到一个 AngularJS 组件中，该组件可以从我们的旧 AngularJS 代码库中使用:

这里我们的帮助函数`reactToAngularComponent`返回一个简单的 AngularJS 组件配置，没有模板。相反，该配置使用`$element[0]`访问底层父 DOM 元素，并使用`$onInit`和`$onDestroy` AngularJS [生命周期方法](https://docs.angularjs.org/guide/component)在创建时挂载`ReactExample`组件，在销毁`reactExampleBridge`组件时卸载它。

注意`reactExampleBridge`组件名称中的后缀“Bridge”。在迁移过程中，这种命名约定将使识别只剩下桥组件子组件的 AngularJS 组件变得容易(这意味着我们现在可以在 React 中重写父组件并删除所有桥)。

现在我们可以在另一个 AngularJS 组件模板中使用`reactExampleBridge`:

## 传递道具

让我们改变`ReactExample`组件，让它接受一些道具:

我们不需要对`reactExampleBridge`组件做任何修改，但是`reactToAngularComponent`助手函数需要一些调整:

如您所见，我们增加了两个辅助函数:

*   `**toBindings**` -从 React 组件`propTypes`中生成 AngularJS 组件绑定对象。我们只需要在注册 AngularJS 包装组件时使用它一次。
*   `**toProps**` -从 AngularJS 控制器值创建一个反应道具对象。每次控制器值改变时，我们都需要使用它，这就是为什么`$onInit`生命周期挂钩被替换为`$onChanges`。方便的是，同样的`ReactDOM`方法可以用于第一次将 React 元素装载到 DOM 中，以及用新的 props 有效地更新已经装载的 React 元素。

这对如何声明 React 组件以及如何在桥组件中使用它们施加了一些限制:

*   所有属性必须在`propTypes`对象中显式声明。我们的`ReactExample`组件不会收到任何未指定的道具。出于文档目的，在所有 React 组件上定义`propTypes`是一个好的实践。这也使得调试更加容易，因为当一个意外类型的属性被传递给一个组件时，React 会在控制台中输出警告。
*   传递给桥组件的所有输入必须是不可变的，否则`$onChanges`生命周期方法将不会被触发，并且`ReactExample`组件将不会接收到更新的值。
*   传递给`reactExampleBridge`的所有输入必须是表达式，因为`toBindings`助手函数只使用`[**<**](https://docs.angularjs.org/guide/component#component-based-application-architecture)`[类型的绑定](https://docs.angularjs.org/guide/component#component-based-application-architecture)。

现在我们可以将`example-text`输入传递给我们的`reactExampleBridge`组件:

## 不同类型的绑定

通常在定义 AngularJS 组件时，你会使用三种类型的绑定:`<`、`@`和`&`。一个简单的 todo list AngularJS 组件如下所示:

然而，我们的`reactToAngularComponent`助手只使用`<`类型的绑定。让我们将我们的`todoList` AngularJS 组件重写为一个 React 桥，看看如何向它传递不同类型的绑定。

`items`输入最初是用`<`绑定类型定义的，所以我们不需要对它做任何修改，但是对于`title`和`on-select`我们必须做如下调整:

*   最初`title`是用`@`绑定定义的，所以我们可以马上传递一个字符串。现在对于`todoListBridge`组件，AngularJS 将把传递的`title`输入作为一个表达式进行计算，所以我们需要用双引号将字符串括起来:

`**title="'Tasks For Tomorrow'"**`

*   最初`on-select`是用`&`绑定定义的，要求我们指定回调需要什么参数。现在我们不需要这样做，因为我们传递了底层函数本身:

`**on-select="::$ctrl.handleItemSelect"**`

由于`handleItemSelect`函数从不改变，我们可以通过使用`::` [一次性绑定](https://docs.angularjs.org/guide/expression#one-time-binding)语法来优化我们的父组件，告诉 AngularJS 不要注意`handleItemSelect`的变化。

## 不可变数据

让我们实现`handleItemSelect`逻辑:

我们通过使用 ES6[array . prototype . map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)将`todoItems`数组替换为它的副本。如果您简单地就地更新 todo 项，`todoBridge`组件的`$onChange`方法不会检测到这种变化。因此，底层的`TodoList` React 组件将不会被重新呈现，UI 将保持陈旧。

我强烈建议不要改变你的数据，这使得对你的应用程序状态的推理更加容易，并且防止了许多错误。拥有不可变的数据也将通过`shouldComponentUpdate`和`React.PureComponent`为 React 的进一步优化打开一扇门。

## 复试

由于我们将`handleItemSelect`回调作为表达式传递，当在`TodoList`组件中调用该函数时，它不知道它最初是在`AppController`上定义的。对于回调中指向控制器的`this`关键字，我们既可以用[function . prototype . bind()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind)方法将上下文绑定到函数，也可以用[胖箭头](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)函数将方法定义为[类实例字段](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/Class_fields)，所有这些都将绑定正确的`this`。

对于所有用`&`绑定声明的输出，每当调用回调时，AngularJS 将触发一个摘要循环。现在我们需要手动完成它，否则你会得到相当奇怪的行为:你的 UI 只会在下一个摘要周期更新。

# 服务和工厂

AngularJS 是一个大框架，它提供了很多现成的功能。你的最终目标是找到你使用的所有 AngularJS 服务的替代品。但是在此之前，您的 React 组件需要一种方法来访问这些服务。为此，我们需要另一个助手函数:

添加一些健全性检查以便于调试:

让我们向 React `TodoList`组件添加一个按钮，该按钮滚动到列表的顶部，并使用 AngularJS `$anchorScroll`服务来执行该滚动:

让您的迁移变得更容易的几个技巧:

*   如果一个服务没有任何 AngularJS 依赖，不要在你的应用模块上注册它。将它直接导入到使用它的文件中。
*   将每个 AngularJS 服务隐藏在一个只公开您需要的功能的包装器中。这样，当需要替换底层 AngularJS 服务时，您可以更容易地将其切换出来。

## 使用 AngularJS 之外的服务

选择 AngularJS 服务，例如`$http`。创建一个新的`myHttpService`类，并通过`getAngularService`助手函数获得 AngularJS 服务。仅添加您的应用程序需要的那些`$http`方法。此外，您可以隔离代码中经常重复使用的相关逻辑，例如在使用`$http`包装器的情况下的自定义服务器错误处理程序。

最后，实例化您的新服务:

只有当底层 AngularJS 服务已经向 AngularJS 注册时，才能导入这样的包装器。一种安全的方法是在组件初始化时。

这种方法的好处是，对于 React 和 AngularJS 组件，以相同的方式导入包装器。

# 完全码

让我们回忆一下。下面是一个完整的待办事项示例代码。

在 Awesense，我们遵循简单的规则来确保迁移顺利进行:

*   所有新功能都是用 React 编写的；
*   如果开发人员接触旧代码，他们会根据公司当时的业务优先级重写它或它的一部分。

在第一年，我们将 40%的前端代码转换为 React。两年后，我们超过三分之二的代码都是用 React 编写的。

我希望您知道 AngularJS-React 桥接是如何工作的后会感到更有力量，并且迁移到 React 的选项看起来不再那么令人生畏。

*原载于 2020 年 2 月 26 日*[*https://dev . to*](https://dev.to/kaplona/angularjs-to-react-migration-184g)*。*