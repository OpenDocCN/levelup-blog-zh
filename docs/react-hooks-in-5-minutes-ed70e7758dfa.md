# 5 分钟后反应钩子

> 原文：<https://levelup.gitconnected.com/react-hooks-in-5-minutes-ed70e7758dfa>

![](img/a3092ceab39f08acccb39c4cd462cc8b.png)

[卡梅隆·科比](https://unsplash.com/photos/Lu2pfy_8VKg?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/hooks?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

当 React Hooks 在 [React Conf](https://www.youtube.com/watch?v=dpw9EHDh2bM&t=1687s) 上发布时，有很多关于它能做什么的讨论。Hooks 解决了一个问题，自从我尝试向一些实习生教授 React 以来，当他们问我 React 中类组件和函数组件的区别时，这个问题就一直困扰着我。简单地为状态和生命周期挂钩创建一个类只会使 React 组件变得不必要的大，更难维护，并将问题传递给其他开发人员。

React Hooks 提供了一个很好的途径来构建 React 组件，这也是它最好的部分— **如果你想加入，这完全取决于你自己！**因此，让我们通过一些例子来深入研究 React 挂钩。下面，我通过 [CRA](https://github.com/facebook/create-react-app) 快速建立一个项目，创建了一个应用组件。我还创建了一个`Form`组件，它接受一个`submitHandler`属性(这将是来自状态项‘user’的更新处理程序/ setState)。

我真正喜欢 React Hooks 的地方是，它允许您创建紧凑的、可重用的、可读性和可维护性非常好的组件。这里有一个使用我前面提到的`Form`组件的小例子:

每个表单元素都有一个`onSubmit`和要跟踪的值。然而，您甚至可以通过创建一个可重用的输入组件来使它变得更细粒度。

最重要的是，你甚至可以使用不同的组件生命周期方法，这些方法最初只存在于类组件中。有了`useEffect`钩子，你可以使用像`componentDidMount`、`componentDidUpdate`、`componentWillUnmount`这样的生命周期，所有这些都只有一个功能！

你可以把挂钩做得简单或详细。作为开发人员，它为您提供了许多漂亮的工具，可以为您的应用程序增加一些更好的性能。声明你不想每次都重新渲染，你可以保存在钩子的‘依赖项’中。如果依赖关系没有改变，**你的钩子不会运行！**

当然，你可以用钩子走得更远。你可以自定义和建立自己的！这里我从 Reddit API 获取并返回一个帖子列表。

# 结论

我在这里没有提到的一些钩子包括`useReducer`(像 Redux 一样管理状态)`useContext`(返回当前上下文值)和`useRef`(当试图通过 ref 属性引用一个孩子时)也非常有用，可以在[官方钩子文档](https://reactjs.org/docs/hooks-intro.html)中找到。

总之，有这么多的可能性，这是很难保持在 5 分钟之内，没有漫谈关于钩子的牛逼。去做些很棒的东西吧！

## 在你“迷上”钩子之前，看看我写的其他一些文章，或者在 Github 上关注我！

[](https://medium.com/@ftangastani/mobx-state-tree-a-step-by-step-guide-for-react-apps-e65716a219d2) [## MobX-state-tree:React 应用的逐步指南

### 熟悉 Redux 的开发人员知道拥有一个存储和良好的控制是多么重要和有用…

medium.com](https://medium.com/@ftangastani/mobx-state-tree-a-step-by-step-guide-for-react-apps-e65716a219d2) [](https://medium.com/@ftangastani/introducing-redux-into-existing-react-projects-771f24fa4b9f) [## 将 Redux 引入现有的 React 项目。

### 从哪里开始。这是每个人在试图将某样东西引入一个…

medium.com](https://medium.com/@ftangastani/introducing-redux-into-existing-react-projects-771f24fa4b9f)