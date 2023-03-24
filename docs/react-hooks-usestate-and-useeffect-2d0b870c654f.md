# React 挂钩:使用状态和使用效果

> 原文：<https://levelup.gitconnected.com/react-hooks-usestate-and-useeffect-2d0b870c654f>

## 在 React 应用程序中使用 useState 和 useEffect 挂钩以及将类组件转换为函数组件的指南

![](img/d50193aec8939db2cf2c2c58f7f7f909.png)

归功于 Keyhole 软件

React 16.8 中增加了 React 挂钩。简而言之，它们允许您在没有类的情况下使用状态、生命周期方法和其他 React 特性。这意味着它们允许您仅使用功能组件构建完整的 React 应用程序。关于为什么 React 钩子被实现的信息，你可以参考这个[文档](https://reactjs.org/docs/hooks-intro.html#motivation)。使用类组件有两个重要原因:

1.  状态管理
2.  生命周期方法

让我们看看现在这些是如何用钩子实现的。对于本指南，我将参考我的存储库，它将组件设置为类，但在本文中会被转换为带挂钩的功能组件:

[](https://github.com/reireynoso/react-hooks-practice) [## reireynoso/react-hooks-练习

### 这个项目是用 Create React App 引导的。在项目目录中，您可以运行:在…中运行应用程序

github.com](https://github.com/reireynoso/react-hooks-practice) 

# 状态管理

让我们首先检查应用程序的功能。

![](img/9edc2743a2ba23f9f6fee9bf09b300b1.png)

反应-钩子-练习 gif

如 gif 中所示，有一个选择按钮，选项从 1 到 5。如果选择了一个，则显示与该号码相关联的名称。现在让我们探索 App.js，它实现了一种管理状态的经典方法。

基于类的组件 App.js

下面是如何用钩子将它转换成一个功能组件。

基于功能的组件 App.js 使用状态

从类到功能组件的语法有一些变化。我们必须从 React 导入 **useState** 钩子。

## 使用状态()

通过`useState()`，你可以在初始状态，这里是`[1,2,3,4,5]`，传递你想要的数据。它返回一个包含两个元素的数组，`[characterChoiceNumbers, setCharacterChoiceNumbers]`，也就是`[the current state, set state function]`。状态可以用第一个元素访问，也可以用第二个元素设置，第二个元素是一个函数。请记住，数组析构语法经常与 React 挂钩一起使用，就像我们上面所做的那样。

## 状态合并附注

除了语法之外，`useState()` 在基于类的组件中与`state`和`setState`的工作方式不同。当你用 React 钩子设置一个新的状态，`setCharacterChoiceNumbers`对我们来说，旧的状态会被替换。因此，如果我们在状态中有多个部分，最好使用多个`useState()`调用，就像我们在`characterChoiceNumbers`和`chosenChoiceNumber`中所做的那样。这样，每个州都将独立于其他州，并且冲突最小。使用一个对象只使用一个状态调用是可能的，但是需要使用 spread 操作符复制前一个状态对象的内容。

注意`useState`不需要设置为对象。在这种情况下，它要么被设置为数组，要么被设置为整数。

# 生命周期方法

现在让我们观察另一个类组件`CharacterInfoPage`。

基于类的组件特征图

该组件利用了以下生命周期方法:`componentDidMount`和`componentDidUpdate`。如前面`App`组件所示，`CharacterInfoPage`正在拿道具`chosenChoiceNumber`。基于该属性的改变，该组件进行提取以改变其状态。让我们将这个基于类的组件转换成一个可以支持生命周期方法的函数。

基于功能的组件特征图像使用效果

允许我们管理副作用(比如 API 调用)的钩子是`useEffect`钩子。它接受两个参数，一个函数和一个数组，并且不返回任何内容。它所采用的函数将在每个渲染周期后执行**。这可能是一个问题，因为如果处理不当，可能会导致无限循环。**

例如，在第一次页面加载时，`useEffect`中的函数运行，状态改变，导致组件再次呈现。因此，`useEffect`将被再次调用以在永无止境的循环中再次改变状态。

然而，有一个有效的解决方案。`useEffect`钩子接受第二个参数，它控制函数是否应该被执行。它是一个值的数组，只有当数组中的一个值改变时，才会执行`useEffect`函数。

## componentDidMount 当量

参考`CharacterInfoPage`的函数组件中的`componentDidMount`等价函数，`useEffect`在第二个参数中接受一个空数组。本质上，它是说每当数组中的值改变时就执行函数。在这种情况下，由于数组中没有值，因此不会再次调用该函数。

## componentDidUpdate 等效项

在我们的例子中，由于`chosenChoiceNumber`的道具会改变，我们希望函数在它改变时执行效果。在`useEffect`的`componentDidUpdate`中，第二个参数中的数组将接受一个值`props.chosenChoiceNumber`。在这种情况下，只要该特定值发生变化，就会进行提取。该数组可以获取何时执行该函数的依赖关系列表。

# 结束语

在实现 React 挂钩时，需要遵循一些规则。最重要的规则是**只调用顶层的钩子**。这意味着不要在循环、条件或嵌套函数中调用钩子。通过遵循这条规则，您可以确保每次组件呈现时都以相同的顺序调用钩子。更多信息，请参考吊钩的[规则。](https://reactjs.org/docs/hooks-rules.html)

本指南仅涵盖让 React 应用程序运行的基本 React 挂钩。[自定义钩子](https://reactjs.org/docs/hooks-custom.html)也可以实现。

下面是代码转换后实现了 React 挂钩的存储库:

[](https://github.com/reireynoso/react-hooks-practice/tree/with-hooks) [## reireynoso/react-hooks-练习

### 这个项目是用 Create React App 引导的。在项目目录中，您可以运行:在…中运行应用程序

github.com](https://github.com/reireynoso/react-hooks-practice/tree/with-hooks) 

感谢您的阅读！