# 如何编写性能更好的 React 函数组件

> 原文：<https://levelup.gitconnected.com/how-to-write-better-performing-react-function-components-8e3216a788ff>

## 仔细编码可以节省更多的时间

![](img/570a92d406f063c588143dd30f364c6d.png)

照片由[劳塔罗·安德烈亚尼](https://unsplash.com/@lautaroandreani?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

本文将介绍如何编写性能更好的 React 功能组件。不包括一般意义上的 web 性能优化，如 HTTP 请求优化、缓存优化、浏览器优化等。

在开始之前，我们先来谈谈 React 框架的性能。有许多前端框架，React 并不是性能最好的，因为它包括额外的运行时、“优化的”diff 算法等等。总的来说，它肯定不如直接操作 DOM 有高性能，但使用它意味着拥有一个强大的 React 社区，许多库可以使我们的效率翻倍，并且它更容易维护，也更容易被其他程序员理解。这通常也意味着更容易招募。

但是这并不妨碍你成为一个更好的 React 用户，今天就开始编码吧！

# React.memo()

函数组件使用的`React.memo`和类组件使用的`React.PureComponent`效果类似。但是`React.memo`实际上是一个高阶成分。它支持第二个参数。没通过的时候会浅浅的对比一下前后道具的变化。如果没有变化，可以直接重用上次渲染的结果。

React 中使用的浅层比较算法将在以下文章中详细介绍:

[](/how-to-get-a-perfect-deep-equal-in-javascript-b849fe30e54f) [## 如何在 JavaScript 中得到一个完美的深度相等？

### 在反应浅相等的基础上实现几乎完美的深相等。

levelup.gitconnected.com](/how-to-get-a-perfect-deep-equal-in-javascript-b849fe30e54f) 

当我们传递第二个参数时，就可以得到前后两个道具，然后进行自定义对比。例如，在下面的组件中，我想将一个中心坐标点传递给 map 子组件，这是一个表示经度和纬度的两位数组:

```
// Parent Component
<Map center={[1, 1]} />;// Map Component
const Map = (props) => {
  /* render using props */
};export default React.memo(
  Map,
  (prevProps, nextProps) => prevProps.toString() === nextProps.toString()
);
```

如上面的代码，我用`Array.prototype.toString()`对比了前后的值变化，当然真实的情况可能没有这么简单。

那么我必须把它应用到所有的功能组件上吗？答案是否定的。例如，如果父组件中没有状态变化，并且传递给子组件的 props 是简单的原始值类型，那么在子组件上使用`React.memo`会增加不必要的计算。这是因为在这种情况下，父组件被重新渲染，并且使用相同道具的子组件必须被重新渲染。

# React.useCallback()

可以传递一个内联回调和一组依赖项。如果依赖关系没有改变，则返回回调的记忆版本。

每次呈现功能组件时(由状态变化等触发。)，如果我们不使用`useCallback`，将会创建一个新函数。这也意味着，如果您将此函数传递给另一个子组件，如果不使用 memo 自定义比较，该子组件将重新呈现。如下例所示:

```
// Parent Component
const callback = () => {
  doSomething(a, b);
};const memoizedCallback = React.useCallback(() => {
  doSomething(a, b);
}, [a, b]);<Map cb={callback} />;// Map Component
const Map = (props) => {
  /* render using props */
};
```

上面代码中的`memoizedCallback`只有在`a`和`b`改变时才会被重新创建，否则指向同一个引用。这与每次渲染时重新创建的`callback`不同。

# React.useMemo()

`React.useMemo`可以传递一个“创建”函数和一组依赖项。如果依赖关系没有改变，则返回先前计算的内存值。这避免了每次渲染时的昂贵计算。

开头提到的 memo 例子可以这样缓存坐标:

```
// Parent Component
const center = React.useMemo(() => [a, b], [a, b]);<Map center={center} />;// Map Component
const Map = (props) => {
  /* render using props */
};export default Map;
```

# 结论

除此之外，还有几个零散的点可以帮到我们，比如用`React.Fragment`避免多余的元素，用条件渲染代替重返回，用键识别组件元素的变化等等。

总之，优化 React 功能组件性能的主要思路是:

1.  减少每次渲染的计算量，可以通过`useMemo`、`memo`等来完成。
2.  减少重新渲染的次数，可以通过`useCallback`等完成。
3.  让 React 尽可能复用 DOM，尽量修改 DOM 而不是破坏再重新创建。

你觉得这篇文章怎么样？或者我遗漏了哪些优化点(仅供编写 React 功能组件使用)，请留下您的评论。

*感谢阅读。如果你喜欢这样的故事，想支持我，请考虑成为* [*中会员*](https://medium.com/@islizeqiang/membership) *。每月 5 美元，你可以无限制地访问媒体内容。如果你通过* [*我的链接*](https://medium.com/@islizeqiang/membership) *报名，我会得到一点佣金。*

你的支持对我很重要——谢谢。

# 了解更多信息

[](https://blog.devgenius.io/how-to-better-poll-apis-in-react-312bddc604a4) [## 如何在 React 中更好的轮询 API？

### setInterval 的替代方案，是一个更好的间隔调用异步方法的解决方案

blog.devgenius.io](https://blog.devgenius.io/how-to-better-poll-apis-in-react-312bddc604a4) [](https://betterprogramming.pub/react-18-has-been-released-implement-mini-react-in-400-lines-of-code-837559761758) [## React 18 已经发布。用 400 行代码实现迷你反应

### React 18 中实现异步可中断更新的最小模型！

better 编程. pub](https://betterprogramming.pub/react-18-has-been-released-implement-mini-react-in-400-lines-of-code-837559761758) [](/under-the-hood-react-vs-vue-vs-svelte-cef44d26c0bc) [## 引擎盖下:反应 vs. Vue vs .苗条

### 前端框架的权衡

levelup.gitconnected.com](/under-the-hood-react-vs-vue-vs-svelte-cef44d26c0bc)