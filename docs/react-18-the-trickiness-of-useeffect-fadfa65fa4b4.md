# 反应 18——使用效果的棘手之处

> 原文：<https://levelup.gitconnected.com/react-18-the-trickiness-of-useeffect-fadfa65fa4b4>

了解在 React 18 中使用严格模式时 useEffect hook 的棘手行为

![](img/fd02267e612c9b6bc3db8a82f5ed1846.png)

由[蒂姆·高](https://unsplash.com/@punttim?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

React 18 已经上市几周了。虽然它带来了一些改进，但是它给最常用的钩子之一引入了一个非常奇怪的行为。想知道那是什么吗？开始了…

应该只在初始挂载时运行的 useEffect()运行了两次！

是的，你没听错！如果你在严格模式下使用 React 18，下面这段代码:

会产生这样的输出:

```
Hello there!
Hello there!
```

**为什么会这样？**

如[文档](https://reactjs.org/docs/strict-mode.html#ensuring-reusable-state)中所述，React 18 引入了一个新的开发专用的严格模式检查。每当第一次装载组件时，这种新的检查自动**卸载**并且**重新装载**每个组件，在第二次装载时恢复先前的状态。

React 团队做出了这样的决定，因为在未来，他们希望添加一个特性，允许 React 在保留状态的同时添加和删除 UI 的部分。由于它要求组件对多次安装和破坏的影响具有弹性，因此引入了额外的检查。

**所以每个组件都被安装，然后卸载，最后再重新安装一次？🤔**

是的，现在就是这样。如果我们像这样使用`useEffect()`钩子:

刷新页面后，输出将是

```
Mounting...
Unmounting...
Mounting...
```

这可能真的令人困惑，因为它还没有在[使用效果文档](https://reactjs.org/docs/hooks-effect.html)中提及。幸运的是，丹·阿布拉莫夫已经[回复了 Github 的一个问题](https://github.com/facebook/react/issues/24502#issuecomment-1118846544)，这个棘手的行为将在 React 文档的新版本中描述。他还介绍了在使用`useEffect()`钩子获取数据时处理它的方法，这样它就不会获取两次。

**总结**

React 18 引入了一个新的开发专用的严格模式检查。该检查自动卸载和重新装载组件，使 useEffect 挂钩在初始装载时触发两次。