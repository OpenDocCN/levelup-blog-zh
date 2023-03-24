# web3 和 NFTs 的第一次苗条开发

> 原文：<https://levelup.gitconnected.com/svelte-development-bc08fda343e8>

![](img/eb4e1a1cd542e0b7f223d479c0cfe7a0.png)

我最近偶然发现了这种编程语言，Svelte，用于开发 web3 的前端应用程序。我的目标是能够读取令牌和上传 NFT 文件

![](img/f58b29563a35ec8d873b8ee5cab4298b.png)

当我比较这种反应时，设置要快得多。其他前端框架允许您创建声明性的状态驱动代码，使用这些，浏览器必须做很多额外的工作来将这些声明性结构转换成 DOM 活动。苗条的语言将会节省你很多时间，并且将会帮助你在你的应用上得到高性能。

由于我是从 React 背景开始开发的，状态的处理方式可能会有点混乱。我还在学习，但是我已经能够很容易地用道具和回调函数组装一个模型了。

他们的在线文档非常容易阅读和使用。我发现浏览例子并理解它们非常容易。它也很容易与函数联系起来，这是我在示例中寻找的特性。例如，在这里你可以拼凑不同类型的模态示例[https://svelte.dev/repl/033e824fad0a4e34907666e7196caec4?版本=3.46.2](https://svelte.dev/repl/033e824fad0a4e34907666e7196caec4?version=3.46.2) 我能够将一个带有关闭函数的定制模型作为参数，如下所示。

![](img/ff6d3d16f80278685348d5ecdb58bb49.png)

您可能会在第 10 行看到以下内容，这就是如何使用键来检索状态

```
const { open } = getContext(‘simple-modal’);
```

我会采取以下功能，并根据我的需要重写它

```
showPopupCustom
```

我打算将一个动态标题、一些内容和要处理的函数传递到这个模型中，所以最终结果将是

```
const showPopupCustom = () => { open(Popup, { title: “Some title”, content: “body content”, button_text: “Pending…”, callback: close, }); };
```

函数 close 是从上下文中提取的，如下所示

```
const { open, close } = getContext(‘simple-modal’);
```

这两个函数可以在 Svelte 提供的示例 repo 中找到

```
const close = (callback = {}) => {
 if (!Component) return;
 onClose = callback.onClose || onClose;
 onClosed = callback.onClosed || onClosed;
 Component = null;
 enableScroll();
 };const open = (NewComponent, newProps = {}, options = {}, callback = {}) => {
    Component = bind(NewComponent, newProps);
    state = { ...defaultState, ...options };
    updateStyleTransition();
    disableScroll();
    onOpen = (event) => {
      if (callback.onOpen) callback.onOpen(event);
      dispatch('open');
      dispatch('opening'); // Deprecated. Do not use!
    };
    onClose = (event) => {
      if (callback.onClose) callback.onClose(event);
      dispatch('close');
      dispatch('closing'); // Deprecated. Do not use!
    };
    onOpened = (event) => {
      if (callback.onOpened) callback.onOpened(event);
      dispatch('opened');
    };
    onClosed = (event) => {
      if (callback.onClosed) callback.onClosed(event);
      dispatch('closed');
    };
  };
```