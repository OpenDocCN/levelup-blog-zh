# 反应最佳实践—避免反模式

> 原文：<https://levelup.gitconnected.com/react-best-practices-avoiding-bad-practices-fefe6062787d>

![](img/230721f65d4925d957813943c2a735b7.png)

Tj·霍洛韦丘克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

像任何类型的应用程序一样，React 应用程序也必须写得很好。否则，我们以后会遇到各种各样的问题。

我们将查看 React 反模式以避免

# 不要使用 ReactDOM.render 的返回值

尽管`ReactDOM.render`返回了对呈现的 React 组件实例的引用，但是我们应该避免使用它，因为它是一个遗留特性。

未来的版本可能会异步呈现，所以我们不应该依赖它的返回值。

而不是写:

```
const inst = ReactDOM.render(<App />, document.body);
doSomething(inst);
```

我们写道:

```
ReactDOM.render(<App ref={doSomething} />, document.body);
```

# 尽量减少 setState 的使用

`setState`不宜过多使用。这样，我们得到了没有太多副作用或内部状态的纯组件。内部状态可能很难跟踪和测试。

# 不要使用字符串引用

我们不应该使用字符串引用。这是因为它已经被对象引用所取代。

所以与其写:

```
const Hello = createReactClass({
  render: function() {
    return <div ref="hello">Hello, world.</div>;
  }
});
```

我们应该写:

```
function Foo {
  const ref = useRef(); useEffect({} => {
    ref.current.scrollIntoView()
  }, []); return <div ref={ref} />
}
```

引用的`current`属性有 DOM 对象。

# 不要在无状态函数组件中使用它

无状态功能组件的一个很大的好处就是我们不用和`this`打交道。

所以我们不应该用它。

他们应该只访问道具并渲染它们/

因此，与其写:

```
function Foo(props) {
  return (
    <div>{this.props.bar}</div>
  );
}
```

我们写道:

```
function Foo(props) {
  return (
    <div>{props.bar}</div>
  );
}
```

# 我们应该避免的常见错别字

我们应该避免常见的错别字。

例如，我们应该注意这些属性名:

*   属性类型
*   上下文类型
*   儿童上下文类型
*   默认道具

此外，我们应该确保这些方法名称拼写正确:

*   `getDerivedStateFromProps`
*   `componentWillMount`
*   `componentDidMount`
*   `componentWillReceiveProps`
*   `shouldComponentUpdate`
*   `componentWillUpdate`
*   `getSnapshotBeforeUpdate`
*   `componentDidUpdate`
*   `componentDidCatch`
*   `componentWillUnmount`
*   `render`

有些单词很多，很容易出现大小写和拼写错误。

# 不要写无效字符

我们的标记中不应该有无效字符。

例如，我们不应该写:

```
<Foo
  name="bar"
  type="string"
  foo="bar">  
  x="y">
  Body Text
</Foo>
```

我们在`foo=”bar”`后面多了一个`>`。

# 不要使用未知的 DOM 属性

我们不应该给 DOM 元素添加未知的 DOM 属性。

例如，我们应该写:

```
<div className="hello">Hello World</div>
```

而不是:

```
<div class="hello">Hello World</div>
```

应用 CSS 类的正确方法是使用`className`属性，而不是`class`属性。

没有`class`道具，因为它是一个保留字。

# 没有不安全的生命周期方法

如果它们被标记为不安全，那么我们就不应该使用它。

它们包括:

*   `componentWillMount`(还有`UNSAFE_componentWillMount`的别名)
*   `componentWillReceiveProps`(和`UNSAFE_componentWillReceiveProps`别名)
*   `componentWillUpdate`(和`UNSAFE_componentWillUpdate`别名)

这些不能用于异步渲染，所以不推荐使用，将被移除。

调用`componentWillMount`中的`setState`将触发另一次渲染并降低性能。

这和`componentWillMount`是一样的。

同样，`componentWillReceiveProps`是一个同步方法，不适合异步渲染，所以它将被移除。

# 不要定义未使用属性类型

如果一个道具类型没有被使用，那么我们应该删除它。

所以我们不应该写:

```
class Hello extends React.Component {
  render() {
    return <div>Hello Bob</div>;
  }
});

Hello.propTypes = {
  name: PropTypes.string
},
```

相反，我们写道:

```
class Hello extends React.Component {
  render() {
    return <div>Hello {this.props.name}</div>;
  }
});

Hello.propTypes = {
  name: PropTypes.string
},
```

# 移除或使用未使用状态

如果我们有一个未使用的状态，我们应该使用它们或删除它们。

例如，我们不应该:

```
class Component extends React.Component {
  state = { foo: 0 };
  render() {
    return <BarComponent />;
  }
}
```

相反，我们应该写:

```
class Component extends React.Component {
  state = { foo: 0 };
  render() {
    return <BarComponent foo={this.state.foo} />;
  }
}
```

# 不要在 componentWillUpdate 中使用 setState

使用`componentWillUpdate`中的`setState`进行第二次渲染。

所以，我们不应该用这个。

相反，我们把我们的`setState`代码放在别的地方。

例如，我们不写:

```
class Hello extends React.Compoennt {
  componentWillUpdate() {
     this.setState({
        name: this.props.name.toUpperCase()
      });
  } render() {
    return <div>Hello {this.state.name}</div>;
  }
};
```

相反，我们写道:

```
class Hello extends React.Compoennt {
  componentWillUpdate() {
    this.updateStates();
  } render() {
    return <div>Hello {this.state.name}</div>;
  }
};
```

![](img/366bccbd8cabd063d8b7546e665c0dba.png)

[Olia Gozha](https://unsplash.com/@olia?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们应该停止使用旧的同步生命周期方法，因为它们不适用于异步渲染。

它们将在以后的版本中被删除。

使用`setState`将降低我们组件的复杂性，使它们更容易操作和测试。