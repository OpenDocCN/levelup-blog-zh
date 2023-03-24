# 自定义 React 挂钩 useClickAway 挂钩

> 原文：<https://levelup.gitconnected.com/custom-react-hooks-the-useclickaway-hook-95eca10b8075>

![](img/b9556c73735d4724d8355ea4ade1ed84.png)

## 如何定制一个隐藏组件的 react 钩子？

我们经常需要创建一个组件，比如下拉菜单，它会在用户点击时显示一些内容，并在用户离开组件时隐藏这些内容。让我们制作一个自定义挂钩来帮助我们处理这个特性，并提供可重用的代码，我们可以在不同的场景中使用，如上下文菜单或导航菜单。

我们的示例组件`Clicker`将非常短:

```
import useClickAway from "./useClickAway";function Clicker() { const { ref, active, toggle } = useClickAway();return (
    <div className='container' ref={ref}>
      <div className='button' onClick={toggle}>Click</div>
      {active && <div className="contents">contents</div>}
    </div>
  );
}
```

这看起来很整洁，因为我们将所有处理点击功能的代码都隐藏在自定义的`useClickAway`钩子中。

这个钩子提供了引用`ref`来与一个容器一起使用，这个容器将监听`mousedown`事件，一个`active`标志指示容器的内容是否应该可见，还有一个`toggle`函数将在用户点击一个按钮时改变`active`。

现在让我们看看如何实现我们的定制钩子。

```
import { useEffect, useState, useRef } from "react";export default function useClickAway() { const [ active, setActive ] = useState(false);

  const ref = useRef(null); function toggle() {
      setActive(!active);
    } function handleClick(e) { 
    if (!ref.current.contains(e.target)) setActive(false);
  }

  useEffect(() => { 
    *if (active) document.addEventListener('mousedown', handleClick);
    else document.removeEventListener('mousedown', handleClick);* return () => {
      document.removeEventListener('mousedown', *handleClick*);
    }; 
  }, [ active ]); return { ref, active, setActive, toggle };}
```

我们使用 React 提供的`useEffect`、`useState`和`useRef`钩子。

`useState`钩子用于存储`active`标志并提供其赋值函数`setActive`。

`useRef`钩子用于提供对一个元素的引用，该元素将作为我们组件的容器。容器元素的边框定义了单击是在组件的“内部”还是“外部”。

`useEffect`钩子订阅和取消订阅文档`mousedown`事件。多亏了它，每当这样的事件在页面上发生时，我们的组件就会知道它并使用`handleClick`函数。为了调整性能，我们可以只在组件的`active`标志为`true` 时订阅，当它为`false`时取消订阅。当`Clicker`组件被卸载(通过使用`return`定义)时，我们也取消订阅事件。

为了方便起见，我还添加了`toggle`函数，顾名思义，该函数切换`active`标志。

在`handleClick`函数中定义了钩子的主要特征——点击时停用。`ref.current`是一个`<div>`容器元素。我们在这里检查元素是否包含用户触发的`mousedown` 事件的目标。如果没有，那么我们将`active` 标志设置为`false`。

为了使用钩子我们用`ref`、`active`标记了`return`、`setActive`赋值函数和`toggle`辅助函数。

就是这样！我们可以在定制的组件中使用`useClickAway`钩子，避免不必要的代码重复。

下面是一个 codesandbox 的工作示例:

[](https://codesandbox.io/s/useclickaway-8qxh0) [## useClickAway - CodeSandbox

### 为 web 应用程序定制的在线代码编辑器

codesandbox.io](https://codesandbox.io/s/useclickaway-8qxh0) 

感谢阅读！如果你喜欢这篇文章——鼓掌并订阅更多！