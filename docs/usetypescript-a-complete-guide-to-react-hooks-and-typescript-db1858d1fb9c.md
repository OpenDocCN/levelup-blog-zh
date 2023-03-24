# 使用类型脚本——反应钩子和类型脚本的完整指南

> 原文：<https://levelup.gitconnected.com/usetypescript-a-complete-guide-to-react-hooks-and-typescript-db1858d1fb9c>

![](img/e60eb3c8f38113b371e21f1866677064.png)

React v16.8 引入了钩子，它提供了将状态和 React 生命周期提取到函数中的能力，这些函数可以在应用程序中的组件之间使用，这使得共享逻辑变得很容易。钩子令人兴奋并被迅速采用，React 团队甚至想象它们最终会取代类组件。

之前在 React 中，共享逻辑的方式是通过[高阶组件](/understanding-react-higher-order-components-by-example-95e8c47c8006)和[渲染道具](/understanding-react-render-props-by-example-71f2162fd0f2)。钩子提供了一种更简单的方法来重用代码，并使我们的组件变得干燥。

**本文将展示 TypeScript 与 React 集成的变化，以及如何将类型添加到钩子和您自己的定制钩子中。**

> 类型定义来自于 [@types/react](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/react) 。有些例子是从[react-typescript-cheat sheet](https://github.com/sw-yx/react-typescript-cheatsheet)中派生出来的，所以请参考它以获得 React 和 TypeScript 的全面概述。其他示例和挂钩的定义摘自[官方文档](https://reactjs.org/docs/hooks-intro.html)。

![](img/aced8a676f6e35c5f7a618a71ff5fd7b.png)

我建了一个课程帮你 [**掌握编码面试>**](https://skilled.dev)

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev) ![](img/aced8a676f6e35c5f7a618a71ff5fd7b.png)

# 使用 TypeScript 更改功能组件

以前 React 中的函数组件被称为“无状态函数组件”，这意味着它们是纯函数。随着钩子的引入，功能组件现在可以包含状态并访问 React 生命周期。为了适应这一变化，我们现在将组件输入为`React.FC`或`React.FunctionComponent`，而不是`React.SFC`。

通过将组件类型化为`FC`，React 类型脚本类型允许我们正确处理`children`和`defaultProps`。此外，还提供了`context`、`propTypes`、`contextTypes`、`defaultProps`、`displayName`的类型。

对应`FC` / `FunctionComponent`的这些道具:

> **注:**React 团队正在讨论[从功能组件](https://github.com/reactjs/rfcs/blob/createlement-rfc/text/0000-create-element-changes.md#deprecate-defaultprops-on-function-components)中移除 `[defaultProps](https://github.com/reactjs/rfcs/blob/createlement-rfc/text/0000-create-element-changes.md#deprecate-defaultprops-on-function-components)` [。这增加了不必要的复杂性，因为默认函数参数也同样有效，不需要引入标准 JavaScript 之外的新概念。](https://github.com/reactjs/rfcs/blob/createlement-rfc/text/0000-create-element-changes.md#deprecate-defaultprops-on-function-components)

# 钩子快速入门

钩子只是允许我们管理状态、利用 React 生命周期和连接到 React 内部(如上下文)的函数。钩子是从`react`库中导入的，只能在函数组件中使用:

```
import * as React from 'react'const FunctionComponent: React.FC = () => {
  const [count, setCount] = React.useState(0) // The useState hook
}
```

默认情况下，React 包含 10 个钩子。其中 3 个钩子被认为是你最常用的“基本”或核心钩子。还有 7 个额外的“高级”挂钩，最常用于边缘情况。根据 React 官方文件，钩子:

**基本挂钩**

*   `[useState](https://reactjs.org/docs/hooks-reference.html#usestate)`
*   `[useEffect](https://reactjs.org/docs/hooks-reference.html#useeffect)`
*   `[useContext](https://reactjs.org/docs/hooks-reference.html#usecontext)`

**高级挂钩**

*   `[useReducer](https://reactjs.org/docs/hooks-reference.html#usereducer)`
*   `[useCallback](https://reactjs.org/docs/hooks-reference.html#usecallback)`
*   `[useMemo](https://reactjs.org/docs/hooks-reference.html#usememo)`
*   `[useRef](https://reactjs.org/docs/hooks-reference.html#useref)`
*   `[useImperativeHandle](https://reactjs.org/docs/hooks-reference.html#useimperativehandle)`
*   `[useLayoutEffect](https://reactjs.org/docs/hooks-reference.html#uselayouteffect)`
*   `[useDebugValue](https://reactjs.org/docs/hooks-reference.html#usedebugvalue)`

虽然钩子本身是有用的，但是如果将它们组合成[自定义钩子函数](https://reactjs.org/docs/hooks-custom.html)，它们会变得更加强大。通过构建自己的钩子，您能够将 React 逻辑提取到可重用的函数中，这些函数可以简单地导入到我们的组件中。对钩子的唯一警告是你必须遵循一些基本规则。

# 将 State 与 TypeScript 一起使用

`useState`是一个钩子，允许我们从类组件中替换`this.state`。我们执行钩子返回一个包含当前状态值的数组和一个更新状态的函数。当状态被更新时，它导致组件的重新呈现。下面的代码显示了一个简单的`useState`钩子:

状态可以是任何 JavaScript 类型，上面我们把它变成了一个`number`。set state 函数是一个纯函数，它将指定如何更新状态，并且将始终返回相同类型的值。

对于简单的函数，`useState`可以从初始值和基于提供给函数的值的返回值中推断出类型。对于更复杂的状态，`useState<T>`是一个泛型，我们可以在其中指定类型。下面的例子显示了一个可以是`null`的`user`对象。

官方打字如下:

# 对 TypeScript 使用效果

`useEffect`是我们如何管理 API 调用等副作用，以及如何在功能组件中利用 React 生命周期。`useEffect`以一个回调函数为参数，回调可以返回一个清理函数。回调将在`componentDidMount`和`componentDidUpdate`执行，清理功能将在`componentWillUnmount`执行。

默认情况下，`useEffect`将在每次渲染时被调用，但是你也可以传递一个可选的第二个参数，该参数允许你仅在值改变时或者仅在初始渲染时执行`useEffect`函数。第二个可选参数是一个值数组，只有其中一个值发生变化时，它才会重新执行效果。如果数组为空，它将只在初始渲染时调用`useEffect`。要更全面地了解它是如何工作的，请参考官方[文档](https://reactjs.org/docs/hooks-reference.html#useeffect)。

使用钩子时，应该只返回`undefined`或者一个`function`。如果返回任何其他值，React 和 TypeScript 都会给你一个错误。如果使用箭头函数作为回调函数，必须小心确保没有隐式返回值。例如，`setTimeout`在浏览器中返回一个整数:

`useEffect`的第二个参数是一个只读数组，可以包含`any`值— `any[]`。

由于`useEffect`将一个`function`作为参数，并且只返回一个`function`或`undefined`，这些类型已经被 React 类型系统声明了:

# 将上下文与 TypeScript 一起使用

`useContext`允许您使用 React `context`，这是管理应用程序状态的全局方法，可以在任何组件内部访问，而无需将值作为`props`传递。

`useContext`函数接受一个`Context`对象并返回当前的上下文值。当提供者更新时，这个钩子将触发用最新的上下文值重新呈现。

我们使用构建上下文对象的`createContext`函数初始化一个上下文。`Context`对象包含一个`Provider`组件，所有想要访问其上下文的组件都必须在`Provider`的子树中。如果您使用过 Redux，这相当于通过上下文访问全局存储的`<Provider store={store} />`组件。关于上下文的进一步解释，请参考[正式文件](https://reactjs.org/docs/context.html)。

`useContext`的类型如下:

# 使用 TypeScript 的 useReducer

对于更复杂的状态，您可以选择使用`useReducer`功能作为`useState`的替代。

如果你之前用过 Redux，这个应该很熟悉。`useReducer`接受 3 个参数并返回一个`state`和`dispatch`函数。`reducer`是表单`(state, action) => newState`的一个函数。`initialState`很可能是一个 JavaScript 对象，而`init`参数是一个函数，它允许你延迟加载初始状态并将执行`init(initialState)`。

这个有点密集，让我们看一个实际的例子。我们将重做来自`useState`部分的反例，但是用一个`useReducer`替换它，然后我们将在它后面加上一个包含类型定义的要点。

`useReducer`功能可以利用以下类型:

# 将回调与 TypeScript 一起使用

`useCallback`钩子返回一个内存化的回调。这个钩子函数有两个参数:第一个参数是一个内联回调函数，第二个参数是一个值数组。值的数组将在回调函数中被引用，并按照它们在数组中的顺序被访问。

`useCallback`将返回回调的记忆化版本，只有当输入数组中的一个输入发生变化时，该版本才会发生变化。当您将回调函数传递给子组件时，会用到这个钩子。它将防止不必要的渲染，因为回调将只在值改变时执行，允许您优化您的组件。这个挂钩可以被视为与`shouldComponentUpdate`生命周期方法类似的概念。

`useCallback`的类型脚本定义如下:

# 将备忘录与类型脚本一起使用

`useMemo`钩子将一个“create”函数作为它的第一个参数，将一个值数组作为第二个参数。它返回一个记忆值，只有当输入值改变时才会重新计算。这使您可以避免为计算值进行渲染时的昂贵计算。

`useMemo`允许你计算任何类型的值。下面的例子显示了如何创建一个输入的记忆数字。

`useMemo`的打字稿定义如下:

`DependencyList`允许包含`any`类型的值，对内置类型或与`T`的关系没有严格要求。

# 带有 TypeScript 的 useRef

`useRef`钩子允许你创建一个`ref`，它可以让你访问一个[底层 DOM 节点](https://reactjs.org/docs/refs-and-the-dom.html)的属性。当您需要从元素中提取值或获取与 DOM 相关的信息(如滚动位置)时，可以使用这种方法。

> 以前我们用`createRef()`。这个函数总是在函数组件中的每一次渲染返回一个新的`ref`。现在，`useRef`在被创建后将总是返回相同的 ref，这将导致性能的提高。

钩子返回一个`ref`对象(类型为`MutableRefObject`)，其`.current`属性被初始化为`initialValue`的传递参数。返回的对象将在组件的整个生存期内保持不变。可以通过使用 React 元素上的`ref`属性设置来更新`ref`的值。

`useRef`的类型脚本定义如下:

您作为泛型`T`传递的泛型类型将对应于您将在`current`中访问的 HTML 元素的类型。

# 将 ImperativeHandle 与 TypeScript 一起使用

`useImperativeHandle`钩子函数有 3 个参数——1。一反应过来`ref`，2。一个`createHandle`功能，和 3。一个可选的`deps`数组，用于显示给`createHandle`的参数。

`useImperativeHandle`是一个**很少使用的函数**，在大多数情况下应避免使用参考。这个钩子用于定制一个可变的`ref`对象，该对象暴露给父组件。`useImperativeHandle`应与`forwardRef`配合使用:

> 组件的第二个`ref`argument(`FancyInput(props, **ref**)`)只在使用`[forwardRef](https://reactjs.org/docs/forwarding-refs.html)` [函数](https://reactjs.org/docs/forwarding-refs.html)时存在，它允许您更容易地将引用传递给子组件。

在这个例子中，呈现`<FancyInput ref={fancyInputRef} />`的父组件将能够调用`fancyInputRef.current.focus()`。

`useImperativeHandle`的类型脚本定义如下:

# 使用 TypeScript 的 useLayoutEffect

`useLayoutEffect`与`useEffect`相似，不同之处在于它仅用于与 DOM 相关的副作用。这个钩子允许你从 DOM 中读取值，并在浏览器有机会重画之前同步重画。

**尽可能使用** `**useEffect**` **钩子，只有在绝对必要时才避免** `**useLayoutEffect**` **钩子，以避免阻塞可视化更新。**

`useLayoutEffect`的打字稿定义与`useEffect`几乎相同:

# 将 DebugValue 与 TypeScript 一起使用

`useDebugValue`是一个用来调试你自定义钩子的工具。它允许你在 React Dev 工具中为你的定制钩子函数显示一个标签。

我们将在下面构建一个自定义钩子，但是这个例子向我们展示了如何使用`useDebugValue`钩子在它们内部进行调试。

# 定制挂钩

建立你自己的钩子的能力是这次更新如此有影响力的真正原因。之前我们在 React 应用中使用[高阶组件](/understanding-react-higher-order-components-by-example-95e8c47c8006)和[渲染道具](/understanding-react-render-props-by-example-71f2162fd0f2)共享组件。这导致我们的组件树变得笨拙，并产生难以阅读和推理的代码。此外，这些通常是使用类组件实现的，这可能[导致许多问题并阻止优化](https://reactjs.org/docs/hooks-intro.html#complex-components-become-hard-to-understand)。

自定义挂钩允许您将核心 React 挂钩结合到自己的函数中，并提取组件逻辑。这个定制钩子函数可以像任何其他 JavaScript 函数一样容易地共享和导入，它的行为与核心 React 钩子相同，并且必须遵循相同的规则。

为了构建我们的定制钩子，我们将使用来自 [React 文档](https://reactjs.org/docs/hooks-custom.html)的例子并添加我们的 TypeScript 类型。这个定制钩子将利用`useState`和`useEffect`，并管理用户的在线状态。我们将假设我们可以访问一个`ChatAPI`，我们可以订阅它并接收我们朋友的在线状态。

对于自定义钩子，我们应该遵循 React 钩子的模式，在我们的函数前面加上单词`use`，表示该函数是一个钩子。我们将把我们的钩子命名为`useFriendStatus`。代码如下，后面是解释。

`useFriendStatus`挂钩带有一个`friendID`，它允许我们订阅这个用户的在线状态。我们利用`useState`函数并将其初始化为`null`。我们将状态值命名为`isOnline`，并有一个函数`setIsOnline`来切换这个布尔值。由于我们有一个简单的状态，TypeScript 可以推断出我们的状态值和 updater 函数的类型。

接下来我们有一个`handleStatusChange`函数。这个函数接受一个包含一个`isOnline`值的`status`参数。我们调用`setIsOnline`函数来更新状态值。`status`无法推断，所以我们为它创建一个 TypeScript 接口(我们还将任意假设它包含用户 ID)。

`useEffect`钩子的回调订阅 API 来检查一个朋友的状态，并在组件卸载时返回一个清理函数来取消订阅 API。当在线状态改变时，我们的订阅执行`handleStatusChange`功能。因为这更新了我们的状态，所以它会将结果传播给使用钩子的组件，并强制重新呈现。

钩子可以导入到任何功能组件中。由于我们向自定义挂钩添加了类型，默认情况下，使用的组件将获得类型定义。

这个逻辑现在可以扩展到任何需要知道用户在线状态的组件

[](https://gitconnected.com/learn/react) [## 学习 React -最佳 React 教程(2019) | gitconnected

### 排名前 49 的 React 教程-免费学习 React。课程由开发人员提交并投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/react)