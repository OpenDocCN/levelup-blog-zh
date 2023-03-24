# JavaScript 最佳实践— JSX 间距和缩进、片段、列表

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-jsx-spacing-and-indentation-fragments-lists-40a1c8c8c1f7>

![](img/c23d1a14fe3caf2ca3361c55908fb486.png)

照片由[麦蒂·贝克](https://unsplash.com/@maddybakes?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

为了使代码易于阅读和维护，我们应该遵循一些最佳实践。

在这篇文章中，我们将看看我们应该遵循的一些最佳实践，以使每个人的生活更轻松。

# 功能组件的一种功能类型

对于功能组件，我们可以坚持使用一种功能类型。

我们既可以写一个正则函数，也可以写一个箭头函数。

例如，我们可以写:

```
const Foo = function (props) {
  return <div>{props.content}</div>;
};
```

或者:

```
const Foo = (props) => {
  return <div>{props.content}</div>;
};
```

箭头函数略短。

所以我们可能想把它用于所有的组件。

# JSX 的布尔属性符号

如果我们有一个布尔属性，我们想把它设置为`true`，我们不需要写出它的值。

例如:

```
const Foo = <Bar personal />;
```

与以下内容相同:

```
const Foo = <Bar personal={true} />;
```

我们可以坚持走捷径。

# JSX 属性和表达式中大括号内的空格

我们可以在 JSX 属性和表达式中明确指定空格。

例如，我们可以写:

```
<div>
  hello
  {' '}
  <a>world</a>
</div>
```

我们将空格字符串放在花括号中，使其显式。

# JSX 的闭合支架位置

我们可以用我们的 JSX 代码把右括号放在一个逻辑位置。

例如，我们可以写:

```
<Greeting firstName="jane" lastName="jones" />;
```

或者:

```
<Greeting
  firstName="jane"
  lastName="jones"
/>;
```

它们在逻辑上都是一致的。

# JSX 的结束标记位置

我们应该把结束标签放在一个整洁的地方。

例如，我们可以写:

```
<Greeting>
  james
</Greeting>
```

或者:

```
<Greeting>james</Greeting>
```

使它整洁。

# JSX 道具和/或孩子中没有不必要的花括号

小孩子身上的串串道具我们不需要花括号。

例如，大括号在下面的代码中是多余的:

```
<Greeting>{'hello'}</Greeting>;
<Greeting prop={'hello'} attr={"foo"} />;
```

我们可以通过书写来清理它们:

```
<Greeting>hello</Greeting>;
<Greeting prop='hello' attr='foo' />;
```

# JSX 属性和表达式中大括号中的换行符

我们应该用整洁的方式写花括号。

例如，我们可以写:

```
<div>
  { bar }
</div>
```

或者:

```
<div>
  {
    bar
  }
</div>
```

它们都是对称的。

# JSX 属性和表达式中大括号内的空格

表达式的两端应该有偶数个空格。

例如，不写:

```
<Greeting name={ firstname} />;
<Greeting name={firstname } />;
```

我们写道:

```
<Greeting name={firstname} />;
```

# JSX 属性中等号周围的空格

在 JSX 属性中，等号两边不需要空格。

例如，我们可以写:

```
<Greeting name={firstname} />;
```

# 第一个支柱的位置

我们可以将第一个道具放在标签名称的内联或下方。

例如，我们可以写:

```
<Hello
   foo
/>
```

或者:

```
<Hello foo />
```

# 反应片段简写或标准形式

片段是不呈现元素的组件，可以用作包装器组件。

它有长的和短的形式。

长格式是`<React.Fragment>...</React.Fragment>`，短格式是`<>...</>`。

例如，我们可以写:

```
<React.Fragment><Foo /></React.Fragment>
```

或者:

```
<><Foo /></>
```

长式可以有道具，短式不能。

# JSX 的事件处理程序命名约定

事件处理程序的名称应该以`on`开头，处理事件的函数应该以`handle`开头。

例如，我们可以写:

```
<Foo onChange={this.handleChange} />
```

或者:

```
<Foo onChange={this.props.onBar} />
```

属性名以`on`开始，处理程序方法以`handle`开始。

# JSX 压痕

我们需要一些缩进在我们的 JSX 代码。

例如，我们可以写:

```
<App>
  <Foo />
</App>
```

有两个缩进空间。

很容易阅读和打字。

# JSX 的道具压痕

我们也可以使用 2 个空格来缩进道具。

例如，我们可以写:

```
<Greeting
  firstName="james"
/>
```

# 在物品列表中添加 K `ey`道具

为了让 React 正确跟踪列表项，我们需要为每一项设置一个惟一的值作为`key`属性的值。

通过这种方式，React 可以正确地识别项目，无论它位于何处。

例如，不写:

```
data.map(x => <Hello>{x.name}</Hello>);
```

我们写道:

```
data.map(x => <Hello key={x.id}>{x.name}</Hello>);
```

![](img/b3a99c0071321c0f8c39d4a77b238ce4.png)

照片由 [Ricardo Moura](https://unsplash.com/@ricardomoura?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们应该记得为列表项目添加`key`道具。

良好的间距和缩进使得阅读代码更加容易。

我们可以使用带空格的字符串使空格显式化。