# 反应模式—提高性能并避免反模式

> 原文：<https://levelup.gitconnected.com/react-patterns-improving-performance-and-avoid-antipatterns-3c4429d8f4cc>

![](img/19b3067833dfc98faa96847aa5b56634.png)

Marc-Olivier Jodoin 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

React 是一个用于创建 web 应用程序和移动应用程序的流行库。

在本文中，我们将研究如何在创建 React 应用程序时提高性能和避免反模式。

# 对账和密钥

我们必须给`key`道具添加一个独一无二的钥匙。

否则，React 无法正确跟踪列表项。

例如，我们可以写:

```
{items.map(item => <li key={item.id}>{item.name}</li>)}
```

我们必须为每个`items`条目准备一个唯一的`id`属性。

没有`key`道具中的唯一 ID，无法跟踪插入的物品。

# 使用效果

我们应该观察需要观察的项目，以更新派生值。

为此，我们使用`useEffect`钩。

例如，我们写道:

```
useEffect(() => {
  doSomething(foo);
}, [foo]);
```

现在我们只在`foo`更新的时候调用`doSomething`。

这是相当于`shouldComponentUpdate`和`componentWillReceiveProps`的挂钩。

它对道具和状态变化都有效。

# 无状态功能组件

无状态函数组件在性能方面没有任何好处。

它只是帮助我们制作仅用于表示的组件，以将逻辑与具有状态的组件分开。

# 使用备忘录

我们可以使用`useMemo`钩子来存储昂贵计算的结果。

例如，我们可以写:

```
const memoizedValue = useMemo(() => compute(a, b), [a, b]);
```

现在 React 不会再次计算该值，除非`a`或`b`改变。

# 重构和良好的设计

我们应该有带有描述性名称的状态。

`items`对于存储一些项目的州来说是个好名字。

如果我们有更具体的东西，那么我们应该用它来命名。

我们应该只在有变化的时候渲染。

如果道具和状态改变，那么我们渲染。

# 不变

我们应该尽可能让事情不变，

这样他们就不会意外变异了。

那么我们就可以避免这种可能很难追踪到的 bug。

为了避免变异，我们可以复制一个对象，然后改变它。

我们可以使用`Object.assign`或 spread 来做到这一点:

```
const newObj = Object.assign({}, obj, { foo: 'bar' });
```

或者:

```
const newObj = {...obj,  foo: 'bar' };
```

同样，我们可以对数组做同样的事情。

例如，我们可以写:

```
const newArr = [...arr, 1];
```

我们使用 spread 操作符将`arr`数组条目扩展到一个新的数组中，并给它加 1。

# 使用 Props 初始化状态

我们不应该使用道具来初始化状态。

相反，我们应该观察适当的变化，然后相应地更新状态。

这样，我们将确保我们的道具随着状态更新。

为此，我们使用如下的`useEffect`钩子:

```
useEffect(() => {
  setBar(foo);
}, [foo]);
```

如果`foo`是一个道具，那么我们可以通过用`foo`调用它来用`setBar`设置`bar`状态，这样当`foo`更新时`bar`状态也会更新。

# 改变状态

此外，我们不应该直接改变状态。

如果我们有一个状态，那么我们不应该直接给它赋值。

相反，我们调用函数来设置状态。

例如，我们可以写:

```
setCount(2);
```

top 设置状态，其中`setCount`是`useState`返回的设置`count`状态的函数。

或者我们可以写:

```
setCount(count => count + 1);
```

如果我们想基于前一个`count`状态的值设置一个状态。

为了提高性能，我们应该尽可能少地这样做。

# 使用索引作为键

如果可能的话，索引不应该用作键。

这是因为使用索引作为键无法正确地进行跟踪，如果我们修改元素或组件的数组，就会出现问题。

key prop 的最佳值是该项目的唯一 ID。

# 在 DOM 元素上散布道具

我们应该避免向 DOM 元素传播道具，以避免传播未知的 HTML 属性，这是一种不好的做法。

我们可能会从 React 得到错误，告诉我们删除未知属性。

相反，我们应该明确地写出它们，不像道具。

![](img/0149cff097be36377148e717a4e32a53.png)

照片由[马修·施瓦茨](https://unsplash.com/@cadop?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

当我们编写 React 组件时，我们应该考虑性能和反模式。

很难创建既快速又能做我们想做的一切的组件。

但是如果我们考虑一些好的做法，我们可以很容易地做到这一点。