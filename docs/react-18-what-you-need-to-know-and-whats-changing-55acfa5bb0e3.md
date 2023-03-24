# 反应 18:你需要知道的和正在改变的

> 原文：<https://levelup.gitconnected.com/react-18-what-you-need-to-know-and-whats-changing-55acfa5bb0e3>

![](img/b55aba123e6491d8858f2258dbc13788.png)

对于开发者来说，这是一个激动人心的时刻。不断变化和不断更新是游戏的名字。这个悬崖笔记帖子之所以成为可能，是因为 React 拥有[令人敬畏的文档](https://reactjs.org/blog/2022/03/29/react-v18.html)。因此，让我们一起来了解一下新的和改进的 React。在你过于恐慌之前，要知道更新 React 通常不会破坏你现有的代码。

![](img/4b6e49554c0a1c173e559e064691705b.png)

# **如何更新？**

要安装 React 的最新版本:

```
npm install react react-dom
```

或者如果你用的是纱线:

```
yarn add react react-dom
```

当您第一次安装或更新 React 18 时，预计会在您的控制台中看到以下警告:

```
ReactDOM.render is no longer supported in React 18\. Use createRoot instead. Until you switch to the new API, your app will behave as if it’s running React 17\. Learn more: [https://reactjs.org/link/switch-to-createroot](https://reactjs.org/link/switch-to-createroot)
```

由于 React 18 引入了一个新的根 API，您需要做一个重要的改变来利用所有的新特性。
转到根入口文件(通常是 **index.js** )。在这个文件中，您需要进行以下更新。根据[文件](https://reactjs.org/blog/2022/03/08/react-18-upgrade-guide.html):

```
// Before
import { render } from 'react-dom';
const container = document.getElementById('app');
render(<App tab="home" />, container);// After
import { createRoot } from ‘react-dom/client’;
const container = document.getElementById(‘app’);
const root = createRoot(container);
root.render(<App tab=”home” />);
```

使用 React 18，需要调用 **createRoot** 并将根元素传递给 **createRoot** 。这将返回根对象，在其上您将调用 **render** 来呈现您的根组件。词根这个词用得太多了。

![](img/2867a9008af240a61dc3229593ee128e.png)

# **有什么变化？**

通过使用这种语法，您可以选择幕后的更新和新特性，最显著的是:*并发:*

> “这是一种新的幕后机制，使 React 能够同时准备多个版本的 UI。您可以将并发性视为一个实现细节——它的价值在于它所释放的特性。React 在其内部实现中使用了复杂的技术，如优先级队列和多重缓冲。”

在 React 18 之前，更新是通过同步呈现来处理的，因此是通过单个不间断的事务来处理的。

现在渲染是可中断的。尽管在用户看到页面上的最终结果之前不能中断更新，但 react 呈现可以暂停，稍后继续。但是关键是:用户界面看起来是无缝的和一致的。在幕后，react 基本上是在不阻塞任何用户输入或应用程序流的情况下准备屏幕。Concurrent React 可以实现可重用的状态来帮助用户体验。例如，如果用户跳开，然后返回到一个屏幕，React 将“从屏幕上删除 UI 的某些部分，然后在重用之前的状态时将它们添加回去。”

![](img/ff2917e5941ca19882b88c82fa9546cc.png)

一个重要的提示:
慢慢来，逐渐适应这些变化。文档称“React 18 中的新渲染行为仅在应用程序中使用新功能的部分启用。”目的是在不破坏现有代码的情况下运行 React 18，但是因为更新是可中断的，所以当启用并发渲染时，组件的行为会有轻微的变化。使用 **< StrictMode >** 也可能有所帮助，因为它将有助于在开发过程中捕获 bug，并记录额外的警告，这有望拦截错误。

**接下来还会有哪些新功能？**

1.  自动批处理或默认分组多个状态更新(相对于以前的版本，以前的版本只在事件处理程序中分组更新)到一个单独的重新呈现中。更多信息:[自动配料](https://github.com/reactwg/react-18/discussions/21)
2.  转换允许一种新的方式来区分更新，即它们是紧急的(与直接用户交互相关，例如点击或键入)还是非紧急的(例如转换更新，其中用户不期望看到立即的物理变化)。为了利用这一点，您将在转换 API 中包装更新，例如 **startTransition()** 和 **useTransition()** 。更多信息:[过渡文件](https://reactjs.org/docs/react-api.html#transitions)
3.  用于声明组件树加载状态的暂挂特性，当与转换 API 结合使用时效果很好。更多信息:[React 18 中的悬念](https://github.com/reactjs/rfcs/blob/main/text/0213-suspense-in-react-18.md)
4.  新的客户端和服务器渲染 API，它与新的方法 createRoot 有关，用于渲染(和卸载)，但请阅读更多关于 [React DOM 客户端](https://reactjs.org/docs/react-dom-client.html)和 [React DOM 服务器](https://reactjs.org/docs/react-dom-server.html)
5.  也许值得你花时间好好谷歌一下严格模式的行为变化。还有…新的钩子！我不兴奋，你才兴奋。
    [useId](https://reactjs.org/docs/hooks-reference.html#useid)
    use transition 和 start transition
    [useDeferredValue](https://reactjs.org/docs/hooks-reference.html#usedeferredvalue)
    [useSyncExternalStore](https://reactjs.org/docs/hooks-reference.html#usesyncexternalstore)
    [useInsertionEffect](https://reactjs.org/docs/hooks-reference.html#useinsertioneffect)

![](img/4b50388a2a2060647d6f9cece759322f.png)