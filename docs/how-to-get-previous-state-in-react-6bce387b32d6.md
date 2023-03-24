# 如何在 React 中获得以前的状态

> 原文：<https://levelup.gitconnected.com/how-to-get-previous-state-in-react-6bce387b32d6>

## 如何定制一个`usePrevious`钩子来实现它

![](img/f29e8d4d85b38181a550d4ff3a4be5c8.png)

Tetiana SHYSHKINA 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

React 16.8.x 推出的钩子给我们带来了全新的开发体验，但也带来了一些编码上的麻烦。我们类组件中一个常见的逻辑是通过生命周期法比较前后道具来处理一些逻辑，那么我们在函数组件中是怎么做的呢？

让我们从一个类似的`React.memo`开始，它与`React.PureComponent`类组件的效果类似，但它实际上是一个更高阶的组件。它的第二个参数可以得到上一个道具和下一个道具。

```
function MyComponent(props) {
  /* render using props */
}function areEqual(prevProps, nextProps) {
  /*
  return true if passing nextProps to render would return
  the same result as passing prevProps to render,
  otherwise return false
  */
}export default React.memo(MyComponent, areEqual);
```

`areEqual`返回`true`时`MyComponent`不重新渲染。而`areEqual`是可选的，默认使用 React 的浅层比较算法。要深入了解:

[](/how-to-get-a-perfect-deep-equal-in-javascript-b849fe30e54f) [## 如何在 JavaScript 中得到一个完美的深度相等？

### 在反应浅相等的基础上实现几乎完美的深相等。

levelup.gitconnected.com](/how-to-get-a-perfect-deep-equal-in-javascript-b849fe30e54f) 

但是如果我需要获得组件中的前一个`props`呢？

我定义了一个`usePrevious`，这样这个逻辑可以在多个组件中重用。下面是一个使用案例:

```
import usePrevious from './usePrevious';function MyComponent(props) {
  const { time } = props;
  const previousTime = usePrevious(time); if (time != previousTime) {
    // Do something
  }
}
```

仔细看`usePrevious`，其实是综合了`useRef`和`useEffect`的特点。看起来不错，它有助于避免一些样板代码。

钩子真的很受欢迎，我也阐述了我对它的一些看法，欢迎大家来看看！

[](/why-are-hooks-so-popular-77e032b34129) [## 为什么挂钩这么受欢迎？

### React 和 Vue 中的钩子有什么异同？

levelup.gitconnected.com](/why-are-hooks-so-popular-77e032b34129) 

*感谢阅读。如果你喜欢这样的故事，想支持我，请考虑成为* [*中等会员*](https://medium.com/@islizeqiang/membership) *。每月 5 美元，你可以无限制地访问媒体内容。如果你通过* [*我的链接*](https://medium.com/@islizeqiang/membership) *报名，我会得到一点佣金。*

你的支持对我来说非常重要——谢谢。