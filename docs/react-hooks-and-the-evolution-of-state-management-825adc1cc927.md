# React Hooks 和状态管理的发展

> 原文：<https://levelup.gitconnected.com/react-hooks-and-the-evolution-of-state-management-825adc1cc927>

![](img/756450676bdccc5b3b9f583830df5c00.png)

德国巴伐利亚州 teufelstättkopf-2019 年 12 月

在这篇文章中，我想分享 ReactJS 的简史和它对国家管理的解决方案。你可能已经知道了这些，但是我认为对于那些刚接触这个框架的人来说，它会让你一瞥 React 是如何发展的，或者如果你是一个老手，它会让你快速更新。

# 组件的两个阵营

React 有两种类型的组件——基于类的和功能性的。顾名思义，类组件继承了`React.Component`并实现了诸如`constructor`和`render`之类的方法用于 React 调用，而函数组件则是字面上的“函数”,它接受一些输入“道具”并返回要呈现的组件。

在 [React v16.8](https://reactjs.org/blog/2018/12/19/react-v-16-7.html) 之前，只有**类组件**可以维护状态并公开一个处理程序来更新状态。此外，在整个组件生命周期的不同时间点，有一整套方法来执行状态管理、副作用和计算。类组件可以完成渲染单页面应用程序所需的一切工作，甚至可以通过`PureComponent`进行性能优化，这也是完成这些工作的唯一方法。

**功能组件**则是不能有状态和副作用的纯功能，只是渲染。我们可以称它们为“哑组件”，与包含更多业务逻辑、副作用、状态操作和子组件控制的“智能组件”相对应。

# 为什么我们不能用类组件来解决？

因此，类组件似乎足够强大，可以解决我们的日常问题，构建我们需要的各种应用程序。然而，使用类组件有各种缺点:

*   在`constructor`中调用`super(props)`的样板文件
*   与`this`关键字的绑定问题(即声明类方法时必须在`constructor`中执行`this.someMethod = this.someMethod.bind(this)`，除非用数组函数语法将其声明为类字段，这需要 babel 插件进行编译)
*   跨多个生命周期方法传播业务逻辑和数据操作
*   有状态逻辑的可重用性——像`this.state`和`this.setState`这样的状态管理方法附加在类组件上，很难与具有类似需求的其他组件共享
*   命令式与声明式——函数式和声明式代码更适合具有更高可读性和可测试性的 UI 表示

因此，React 社区倾向于函数组件，它本质上只是类组件的`render`方法，放弃了其他生命周期方法。然后其他工具和模式进来，为功能组件“修补”缺失的状态管理特性，比如 Redux 带来了全局存储和缩减器，recompose 给了我们`withState`和`withHandlers`等。

这些库中的大多数使用高阶组件(HOC)模式，这种模式包含函数组合，这是函数式编程中非常核心的模式。这可能会使我们避免类组件的缺点，并充分发挥函数组件的能力，但包装器地狱问题是 HOC 的致命弱点，它使虚拟 dom 树非常复杂，并损害了渲染性能。

# 功能组件的官方“扩展”

在开源社区的所有这些尝试之后，React 团队最终[提出了](https://www.youtube.com/watch?v=V-QO-KO90iQ)2018 年的官方解决方案 React hooks。钩子试图用不同的模式解决同样的问题，功能组件中的状态管理。[钩子提案中提到的](https://reactjs.org/docs/hooks-intro.html#classes-confuse-both-people-and-machines):

> 钩子让你不用类就可以使用更多的 React 特性。

带有钩子的函数组件结束了类组件的时代，以及所有用于状态管理的第三方库。

如果像`recompose`和`redux`这样的第三方包用 HOC 杀死了类组件，那么很可能 hooks 和其他 React 16+新 API 杀死了那些第三方，把那些特性带回了“官方 React”。

*   `useState`可以代替`withState`
*   `useEffect`可以代替`lifecycle`
*   `useMemo`和内联函数可以代替 `withHandlers`
*   `useReducer`可以替换`redux`中的减速器
*   React 上下文 API 可以替换`redux`中的全局存储
*   `React.memo`可以代替`recompose`中的`PureComponent`或`pure`

在大规模应用中，仍然有有效的用例使用`redux`和`redux-saga`，但是`recompose`已经完全被钩子[取代了](https://github.com/acdlite/recompose/tree/3db12ce7121a050b533476958ff3d66ded1c4bb8#a-note-from-the-author-acdlite-oct-25-2018)。

展示从类组件到钩子的转换的动画

对我来说，hooks 的机制太强大了，它带来了新的概念，我怀疑是否在我的项目中采用它。例如，我花了一些时间去理解`useEffect`钩子和它的依赖数组。我还觉得在渲染函数中声明副作用有点奇怪。

在小规模项目的一些新组件上进行试验后，我发现在功能组件中进行状态管理既简单又清晰。我不再需要追踪道具从哪里来，在生命周期方法和多层 HOC 之间跳来跳去寻找业务逻辑，深入研究 HOC 以找出注入了哪些道具。开发和调试体验大大改善，更不用说渲染性能的提升。

# 这都是关于取舍的

当然，我并不是说类组件消失了，我们应该将所有组件迁移到函数式风格。事实上，React 团队没有计划弃用类组件，也不建议进行大分解来重写所有现有的组件。

毕竟，凡事都有取舍，选择解决正确问题的正确模式是一个品味问题。

我希望您现在对状态管理的发展有了更多的了解，并找到适合您的用例的最佳模式。