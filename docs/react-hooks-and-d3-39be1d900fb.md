# 反应钩和 D3

> 原文：<https://levelup.gitconnected.com/react-hooks-and-d3-39be1d900fb>

## React 钩子如何使 D3 数据可视化更具可伸缩性，更易于维护。

**TL；博士**

使用 React 作为 D3 的容器来管理 SVG 和其中的一切，应用程序级的管理应该由 React 来完成。有关最少的可行示例，请参见本文底部的代码清单。

# **走近**

已经有很多文章写了 React 和 D3 的世界是如何一起工作的。就我个人而言，我找到的最好的概述是由[这个演讲](https://medium.com/u/4607b4069d83#/4)中以及由[“制作:线条画教程”](https://medium.com/u/93931cb7e345#steps)中找到。它包含了为 D3 组件构建类的例子，以及如何将 D3 功能方法与 OOP 原则结合起来。

# **将元件接线在一起**

有了上面的 D3 组件，我们现在可以回到 React 世界了。

这里我们需要的是向 D3 组件提供渲染 SVG 所需的一切。通常这些至少是:我们已经拥有的容器元素引用(通过`useRef`钩子)，数据，宽度和高度可视化(通过`useState`钩子)。

为了连接所有这些状态，我们将使用`useEffect`钩子。看看下面的代码:

反应元件用钩子钩住 d3 元件。

对于每一个动作，我们都建立新的`useEffect`钩子。我们将把它们提取为独立的函数，这样代码会更容易阅读。

现在，我们很可能想要在 D3 和 React 之间建立双向连接。

# **对 D3 通信做出反应**

类似于处理 resize 事件。我们将创建另一个`useEffect`钩子来监听窗口调整事件，并更新 React 组件的宽度和高度状态(我已经添加了一些去抖动功能，所以更新不会太频繁)。

```
useEffect(() => { let resizeTimer; 
  const handleResize = () => {
    clearTimeout(resizeTimer); 
    resizeTimer = setTimeout(function() {
      setWidth(window.innerWidth);
      setHeight(window.innerHeight);
    }, 300);
  }; window.addEventListener(‘resize’, handleResize); return () => {
    window.removeEventListener(‘resize’, handleResize);
  };}, []);
```

注意，当组件将被卸载时，我们使用`return`语句来删除事件监听器。

接下来，为了让 D3 组件响应 React 状态更新，我们只需要在每个`width`或`height`状态改变时调用适当的方法:

```
useEffect(() => { vis && vis.resize(width, height);}, [ width, height ]);
```

我们在调用`vis.resize`之前使用`vis &&`，因为 resize 事件可能在`vis`被创建(数据被加载)之前发生。

# **D3 反应过来通讯**

假设我们的 D3 可视化有可点击的数据点，这些数据点触发了 React world 中的一些视图更新。

在 D3 组件中，我们可能有这样的东西:

```
d3.selectAll(‘.datapoint’)
  .on(‘mouseup’, d => this.setActiveDatapoint(d));
```

其中`setActiveDatapoint`为箭头函数:

```
setActiveDatapoint = (d) => {
  // some internal code here
  this.props.onDatapointClick(d);
}
```

为什么用箭头函数？类中的箭头函数自动绑定到该类的实例。这很重要，因为我们在 mouseup 回调中使用了`this`关键字。

如果我们在这里使用普通的类方法，那么`this.setActiveDatapoint(d)`中的`this`将引用`.datapoint`选择而不是我们的`vis`对象。

没有箭头函数([可能不如类方法](https://medium.com/@charpeni/arrow-functions-in-class-properties-might-not-be-as-great-as-we-think-3b3551c440b1))也能工作的另一种方法是使用类方法，并用(最常见的)表达式将其绑定在构造函数中:

`this.setActiveDataPoint = this.setActiveDataPoint.bind(this);`

因为我们可能只有一个 D3 组件的实例，所以我认为在这里使用箭头没有问题。

我们需要做的最后一件事是将适当的道具传递给 React 世界中的 D3 组件。参见下面的代码最终版本，`onDatapointClick`属性包含在 React 组件的`initVis`函数中。

# **代码清单**

下面是 D3 和 React 组件的代码。如上所述，对于 React 组件，我从 useEffect 钩子中提取了函数。

# 放弃

在写这篇文章的时候，React hooks 还是一个实验性的提议，请参考[官方文档](https://reactjs.org/hooks)任何可能发生的变化。

感谢您的阅读！