# 在 React 中创建一个定制的模型，与上下文、门户和挂钩进行交互

> 原文：<https://levelup.gitconnected.com/build-a-modal-using-react-context-portals-and-hooks-bd0c4e54537e>

![](img/658ea49fd3ef0ffd0823cfd35fae6cbb.png)

模态是在应用程序顶部显示信息的好方法，通常用于通知、警告或独立对话框，如注册和登录表单。在开始构建自定义模型之前，我建议搜索任何现有的解决方案，看看它们是否符合您的需求(两个 [Reach UI 的对话框](https://ui.reach.tech/dialog/)和 [react-modal](http://reactcommunity.org/react-modal/) 都是流行的社区选择)。如果做不到这一点，让我们在 React 中创建一个定制的模态组件。

让我们首先创建一个基本的模型，它根据一些本地状态有条件地呈现。单击应用程序根中的按钮应该会触发模式，单击模式中的按钮应该会关闭它。

示例一:基本模态实现

只有当您需要从`<App/>`内部触发模态时，这才真正有用，但是如果您想要从嵌套组件中获得相同的功能，该怎么办呢？一种选择是将 setState 动作`setIsModalOpen`作为一个 prop 传递，并在嵌套组件中的按钮被单击时触发 modal 作为一个回调。

示例二:通过回调属性触发模态

这适用于单级嵌套，但可能无法很好地伸缩。我们可以继续通过组件向下传递回调，但这很快变得费力、难以维护，并产生大量多余的代码。进入[反应上下文](https://reactjs.org/docs/context.html)。

简而言之，React 的上下文 API 允许您在一个[提供者](https://reactjs.org/docs/context.html#contextprovider)中存储一个值，可以通过一个[消费者](https://reactjs.org/docs/context.html#contextconsumer)从应用程序中的任何地方访问该值。每当声明一个消费者时，React 将在组件树中搜索第一个匹配消费者上下文的提供者，返回其值，然后订阅任何进一步的更改。让我们用一个提供者来包装前面的例子，设置`setIsModalOpen`回调作为它的值，然后利用`[useContext()](https://reactjs.org/docs/hooks-reference.html#usecontext)`钩子在一个嵌套组件中使用它。

示例三:通过 React 上下文触发模态

现在我们有了一个可以从应用程序的任何地方触发的模型，但目前只能呈现静态内容。为了让模型动态呈现，需要重构它来处理子模型。React 的单向数据流意味着向上传递数据被认为是一种反模式，因此我们还需要一种可行的方法将数据从嵌套组件向上传递回根上的模型。

Jenna Smith 是一位才华横溢的前端开发人员，也是我的前同事。她提议用 React 的门户网站作为解决方案，因为它们被明确地设计成将子节点传递给一个存在于父组件层次结构之外的 DOM 节点。创建门户需要两个参数:任何可呈现的 React 元素(我们的动态内容)和一个将内容注入其中的 DOM 元素(模型的容器)。

例子四: [Jenna Smith](https://medium.com/u/21b2f9237313?source=post_page-----bd0c4e54537e--------------------------------) 使用 React 的 createPortal 方法的解决方案

从沙箱中可以看出，她创建了两个功能组件来为模型提供动态内容。`<ModalProvider />`组件包含一个附加了 ref(`<div ref={modalRef}/>`)的 DOM 元素，以及一个包装整个应用程序并将 ref 的当前值分发给其中任何相关消费者的上下文提供者。第二个组成部分是模态本身。每当一个`<Modal/>`组件被渲染时，它将试图通过`useContext()`检索`modalRef`元素。如果存在一个 ref，它将创建一个 React Portal 并将模态的子元素注入 ref 元素，而不是将组件安装在 DOM 树中的预期位置。💥

现在，可以在 ModalProvider 中的任何地方使用模式组件，以便在应用程序上呈现动态内容。这种方法的一个小警告是，每当模态被装载时，主体将继续在 iOS 上滚动。我强烈推荐查看 [Will Po](https://medium.com/u/ec9acadcbea7?source=post_page-----bd0c4e54537e--------------------------------) 关于[车身卷轴锁](https://medium.com/jsdownunder/locking-body-scroll-for-all-devices-22def9615177)的文章以获得一些解决方案。

喜欢这篇文章吗？[在 Twitter 上关注我](https://twitter.com/phunkren)