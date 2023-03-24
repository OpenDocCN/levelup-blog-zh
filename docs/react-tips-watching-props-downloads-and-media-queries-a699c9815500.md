# 反应提示—观看道具、下载和媒体查询

> 原文：<https://levelup.gitconnected.com/react-tips-watching-props-downloads-and-media-queries-a699c9815500>

![](img/e24e3702b7df7e8e0ed19c86d030f0fb.png)

丹尼尔·罗伯茨在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

React 是一个用于创建 web 应用程序和移动应用程序的流行库。

在本文中，我们将了解一些编写更好的 React 应用程序的技巧。

# 用 React 钩子在状态更新时运行异步代码

我们可以用`useEffect`钩子观察状态的变化。

第二个参数接受一个数组，该数组可以保存我们想要监视的值。

当它们改变时，第一个参数中的回调将运行。

例如，我们可以写:

```
function App() {
  const [loading, setLoading] = useState(false); useEffect(() => {
    if (loading) {
      doSomething();
    }
  }, [loading]); return (
    <>
      <div>{loading}</div>
      <button onClick={() => setLoading(true)}>click Me</button>
    </>
  );
}
```

我们有监视`loading`状态的`useEffect`钩子。

如果它改变了，那么回调将被加载。

当我们点击“点击我”按钮时，它会改变。

它将`loading`状态设置为`true`。

# 如何将获取响应作为文件下载到 React 组件中

我们可以通过将结果转换成 blog 来下载 Fetch API 检索到的响应文件。

然后我们点击一个看不见的链接就可以下载了。

例如，我们可以写:

```
class App extends React.Component {
  constructor(props) {
    super(props);
    this.linkRef = React.createRef();
  } componentDidMount() {
    fetch('./file.pdf')
      .then(res => {
        return res.blob();
      }).then(blob => {
        const href = window.URL.createObjectURL(blob);
        const a = this.linkRef.current;
        a.download = 'file.pdf';
        a.href = href;
        a.click();
        a.href = '';
      })
      .catch(err => console.error(err));
  } render() {
    return (
      <a ref={this.linkRef}/>
    );
  }
}
```

我们有一个链接分配给我们的参考。

然后使用`fetch`函数，我们向`file.pdf`发出 GET 请求。

然后我们用`res.blob()`把它转换成一个斑点

一旦我们这样做了，我们就把 blob 传递给`createObjectURL`方法。

然后我们获取链接并设置`download`属性来设置文件名。

`href`设置为我们创建的对象 URL。

然后我们调用`click`开始下载。

我们通过将`href`设置为空字符串来清除链接。

# 使用 React 子组件上的属性更新状态

在子组件中，我们可以通过在`getDerivedStateFromProps`中返回一个状态来用 props 更新状态。

例如，我们可以写:

```
class Counter extends Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    }
  } static getDerivedStateFromProps(props) {
    return {
      count: props.count * 2
    }
  } // ...
}
```

我们从道具中得到`count`，然后我们将它加倍，并将其作为`count`状态的值返回。

与大多数其他类组件方法不同，它是一个静态方法。

无论更新与否，每次呈现组件时都会调用它。

它是异步的，所以它与其他方法一致，其他方法也是异步的。

我们不应该在方法上犯副作用。

如果我们需要副作用，我们应该在其他地方做。

# React 和媒体查询中的内联 CSS 样式

我们可以用 react-responsive 包以内联 JavaScrip 的形式添加 CSS 样式。

它支持添加媒体查询。

我们可以通过运行以下命令来安装它:

```
npm install react-responsive --save
```

然后我们可以写:

```
import React from 'react'
import { useMediaQuery } from 'react-responsive'const App  = () => {
  const isDesktopOrLaptop = useMediaQuery({
    query: '(min-device-width: 1224px)'
  })
  const isBigScreen = useMediaQuery({ query: '(min-device-width: 2000px)' })
  const isTablet = useMediaQuery({ query: '(max-width: 1500px)' })
  const isMobile = useMediaQuery({
    query: '(max-device-width: 1000px)'
  })
  const isPortrait = useMediaQuery({ query: '(orientation: portrait)' }) return (
    <div>
      <p>{isBigScreen && 'big screen'}</p>
      <p>{isTablet && 'tablet screen'}</p>
      <p>{isMobile && 'mobile screen'}</p>
      <p>{isPortrait && 'portrait screen'}</p>
    </div>
  )
}
```

它带有 th `useMediaQuery`钩子，可以让我们为给定宽度或方向的屏幕添加媒体查询。

我们有一个大屏幕，媒体查询，移动和肖像屏幕。

它们都根据屏幕大小和方向返回布尔值。

然后我们可以在 JSX 代码中使用它们来创建响应性布局。

![](img/358f8d5b8c8c0e347c6e821e3febff2a.png)

[金·格林](https://unsplash.com/@kgcre8tive?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

有一些用 JavaScript 添加媒体查询的包。

我们可以用`useEffect`运行异步代码。

我们还可以观察适当变化，并相应地进行更新。

我们可以下载 React 组件中的文件。