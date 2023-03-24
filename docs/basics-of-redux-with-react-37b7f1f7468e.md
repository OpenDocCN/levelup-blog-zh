# Redux with React 的基础知识，或支柱钻探的死亡

> 原文：<https://levelup.gitconnected.com/basics-of-redux-with-react-37b7f1f7468e>

第 1 部分——通过类组件设置和使用 Redux

![](img/b5c1ec3a2e3117d3f40a57e99f94be51.png)

照片由[朱利亚·梅](https://unsplash.com/@giuliamay?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

我听说你正在学习反应，并且厌倦了所有相关的道具训练？从子组件向父组件发送信息的痛苦是什么？

我也去过那里。但是不要害怕，Redux 是来帮助我们的。Redux 库允许我们用所有 React 组件可用的数据定义一个存储。让我们看看它是如何工作的，并用它建立一个简单的计数器。

# **设置冗余**

React，让我们看看如何在我们的项目中设置它。

(快速注意:无论您是否使用类组件，这些步骤都是相同的:-))

首先创建您的 React 项目，访问项目目录，输入`**npm install redux react-redux**`安装 redux。

打开您的项目，准备定义我们的*动作*和*减速器*。

在 Redux 文档中，动作被定义为将数据从应用程序发送到存储的信息负载。他们是商店的唯一信息来源。”动作基本上告诉我们的 Reducers 给 Redux 存储中的状态带来什么变化。

*reducer*将告诉 Redux 如何通过使用*动作*作为参数来修改我们的状态。

动作由*动作创建者*定义。我们的计数器只需要两个简单的动作，没有有效负载:一个增量和一个减量。让我们在一个新的 **index.js** 文件的 **actions** 文件夹中为这些写 *Action Creators* 。

说清楚一点，*动作*本身就是 Javascript 对象，而*动作创建者*是返回*动作*的函数。

现在让我们在**减速器**文件夹中创建一个 **counterReducer.js** 文件并定义减速器。

Redux 文档告诉我们:*‘reducer 是一个纯函数，它接受前一个状态和一个动作，并返回下一个状态。’。*

这里，*先前状态*首先由 initialState 对象定义，并作为函数的第一个参数传递。

*动作*是第二个参数，它将定义我们的状态会发生什么。再看看我们的*动作创建者*的:他们返回动作，这是一个有一个 key ( *type)* 的对象，有一个对应的值。
在我们的 reducer 中，action *type* 定义了状态发生了什么(关注 switch 语句:如果 type: 'ADD '，那么 counter+1。否则，如果类型:“减法”，然后计数器-1)。

很简单，是吗？

接下来，良好的实践告诉我们，在单独的文件中定义我们的单个减速器，并将它们分组到一个唯一的文件中。这对于我们的计数器来说不是强制性的，因为我们只有一个 reducer 文件，但是为了可重用性，让我们在我们的 **reducers** 文件夹中的一个新的 **index.js** 文件中完成它。

我们的行动和减速器现在设置。让我们在 Redux store 中对它们进行分组，并将其提供给我们的 React 应用程序。为此，我们访问 **src/index.js** 并定义商店。

我们首先从 Redux API 导入我们的 *combineReducers* 函数、 *createStore* 函数和来自 React-Redux API 的 *Provider* 组件。

我们用 *createStore* 函数和 *combineReducers* 作为唯一的参数来创建商店。最后，我们用 Provider 组件和新创建的 Redux Store 作为道具来包围我们的应用程序。

我们的应用程序现在可以访问 Redux 商店了！

# 在 React 中使用 Redux 和类组件

商店现在可用于我们的应用程序，让我们访问它。在 **App.js** 中，我们首先要检索将要显示的计数器值。

在我们的应用程序组件的最后一行，我们可以看到通常的**导出默认值**后跟一个 **connect()** 函数。这个从 react-redux API 导入的函数将我们的组件连接到商店。它最多可以接受四个参数，但我们只对其中两个感兴趣。

第一个回调按照惯例被称为 **mapStateToProps** ，它为我们的组件订阅存储更新(*)。这意味着任何时候存储更新，* `*mapStateToProps*` *都会被调用* — React Redux 文档)。 **mapStateToProps** 返回一个 JS 对象:这里我们的对象只有一个 key:value 对。你知道那一对。是的，这就是我们先前在**计数器减速器**减速器函数中定义的初始状态:

完成后，我们的类组件现在可以访问计数器值作为一个属性。

我们的应用程序组件显示从 Redux 商店获取的值！

现在让我们创建一个新组件，它将更新 Redux 存储中的计数器值。按照惯例，在 React 项目中，连接到 Redux 的组件存储在一个名为“container”的文件夹中。我们新的 **Counter.js** 组件如下所示:

好的。暂停。你可能想知道这些道具是从哪里来的。我们一个都没通过。我们甚至还没有将组件导入到 **App.js** 中。

好吧，让我们来看看这里发生了什么。

我们的新组件可视化地呈现了两个按钮，单击这两个按钮会触发一个作为 prop 传递的函数。

为了理解这些函数的来源，我们必须看一下我们代码的最后一行。

就像在 **App.js** 中一样，我们用 **connect()** 将组件连接到 Redux 商店。我们之前提到过 **connect()** 可以接受两个我们感兴趣的参数: **mapStateToProps** ，这次我们不调用它；以及**mapdispatctoprops**，我们在这里使用它**。**

**mapDispatchToProps** 允许我们让组件能够分派动作，这意味着它现在能够通过使用动作来更新 Redux 存储内部的状态。

如果使用了 **mapDispatchToProps** ，它也必须在组件中定义，作为函数或对象。我们在这里将其定义为一个函数，其中 **dispatch** 是唯一的参数。它返回一个包含我们用来修改状态的两个函数的对象。

*   *请注意，****connect()****函数带两个参数，如果希望使用****mapdispatctoprops****而不是****mapstattoprops****，只需调用* ***connect(null，mapdispatctoprops)****即可*

现在，如果您将我们的 **Counter.js** 导入到 **App.js** 并测试它，您会喜欢您所看到的！

每次使用+/-按钮更改计数器值时，都是通过 Redux store 从子组件( **Counter.js** )向其父组件( **App.js** )发送信息。

今天到此为止！我希望这有助于您更好地理解 Redux 如何与 React 一起工作。

在本系列的第 2 部分中，我们将看到如何将 Redux 与功能组件和 React 挂钩一起使用。

## 完整代码:

[https://codesandbox.io/s/solitary-https-eotwo](https://codesandbox.io/s/solitary-https-eotwo)

## 来源:

[](https://react-redux.js.org/introduction/quick-start) [## 快速启动反应还原

### React Redux 是 Redux 的官方 React 绑定。它让您的 React 组件从 Redux 存储中读取数据，并且…

react-redux.js.org](https://react-redux.js.org/introduction/quick-start) [](https://redux.js.org/introduction/getting-started) [## Redux

### Redux 是 JavaScript 应用程序的可预测状态容器。它帮助您编写行为一致的应用程序…

redux.js.org](https://redux.js.org/introduction/getting-started)