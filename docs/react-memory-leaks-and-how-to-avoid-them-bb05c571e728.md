# React —内存泄漏以及如何避免它们

> 原文：<https://levelup.gitconnected.com/react-memory-leaks-and-how-to-avoid-them-bb05c571e728>

简单地说，每当内存中存在不可访问或未被引用的数据时，就发生了内存泄漏。

![](img/2c8866375242cd3eb9ada63b90426467.png)

根据[维基百科](https://en.wikipedia.org/wiki/Memory_leak)的说法，内存泄漏是一种资源泄漏，当计算机程序错误地管理内存分配，没有释放不再需要的内存时就会发生。当一个对象存储在内存中，但不能被运行的代码访问时，也可能发生内存泄漏。

React 应用程序中的内存泄漏主要是由于在卸载组件之前没有取消组件安装时所做的订阅。这些订阅可以是:

*   DOM 事件侦听器
*   WebSocket 订阅
*   对 API 的请求

在本文中，我们将重点关注如何避免 DOM 事件侦听器上的内存泄漏，但是这个过程与 WebSocket 订阅非常相似。

基本上，当我们在 React 中添加一个事件侦听器时，为了避免内存泄漏，我们需要删除它们。

# **使用类组件**

让我们看看如何使用类组件来实现这一点:

```
class MyComponent extends React.Component {
  constructor(){
    super(props) this.handleClick = this.handleClick.bind(this)
  } handleClick = () => {
     //do something when we clicked
  }  componentDidMount() {
    document.addEventListener("mousedown", this.handleClick);
  } componentWillUnMount() {
    document.removeEventListener("mousedown", this.handleClick);
  } render() {
    renturn ...
  }
}export default MyComponent
```

如上所示，我们在`componentDidMount`中创建了事件监听器，并在`componentWillUnMount`中移除了它。

# 使用功能组件

我们可以使用 useEffect 挂钩来添加和删除事件侦听器，从而在功能组件中实现相同的功能，如下所示:

```
const MyComponent = () => {
  useEffect(() => {
    const handleClick = () => {
      //do something when we click
    };

    document.addEventListener('mousedown', handleClick); return() => {
      document.removeEventListener('mousedown', handleClick);
    };
  }, []); return ...
};export default MyComponent;
```

空的`[ ]`依赖列表确保我们的 useEffect 在第一次渲染后只被调用一次。

为了让我们清理东西，useEffect 允许我们从回调函数中返回一个清理函数。React 将在下一次调用我们的 useEffect 回调之前调用这个清理回调，比如当依赖关系改变时。

此外，当组件卸载时，React 将调用此清理。因此，我们在清理回调中删除了事件侦听器。

# 结论

在本文中，我们研究了使用 DOM 事件侦听器时发生的内存泄漏以及如何避免它们。我们还指出，对于由于 WebSocket 订阅导致的内存泄漏，这个过程非常相似。

然而，对于由于“对 API 的请求”而导致的内存泄漏，这个过程有一点不同，需要一个叫做“AbortControllers”的东西。你可以在这里了解更多信息[。](https://dev.to/jeremiahjacinth13/memory-leaks-how-to-avoid-them-in-a-react-app-1g5e#:~:text=Causes%20of%20Memory%20Leaks%20in,a%20request%20to%20an%20API.)

— -

如果你喜欢这篇文章，请留下几个(或很多)掌声，如果你还没有，请确保在 Medium 上关注我。您也可以订阅，以便在我发布时收到电子邮件。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)