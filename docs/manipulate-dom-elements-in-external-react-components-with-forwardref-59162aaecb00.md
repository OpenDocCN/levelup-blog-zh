# 用 ForwardRef 操作外部 React 组件中的 DOM 元素

> 原文：<https://levelup.gitconnected.com/manipulate-dom-elements-in-external-react-components-with-forwardref-59162aaecb00>

![](img/2e4093d86eaac51b229248c6a65f4eb4.png)

照片由[布鲁克·卡吉尔](https://unsplash.com/@brookecagle?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

React 是一个用于创建前端视图的库。它有一个庞大的图书馆生态系统与之合作。此外，我们可以用它来增强现有的应用程序。

为了用 React 直接操作 DOM 元素，我们可以创建一个`ref`，将它与一个元素相关联，然后引用它并调用它的方法来直接操作 DOM 项。

在本文中，我们将研究如何通过转发引用将引用传递给其原始组件之外的组件。

# 将引用转发到 DOM 组件

我们可以通过创建一个 ref 来转发 ref，然后调用`forwardRef`方法来获取 ref，然后将它传递到我们想要的任何地方。

例如，如果我们有一个返回`button`元素的`Button`组件，并且我们想从外部组件获取`button`元素，我们可以编写以下代码:

```
const Button = React.forwardRef((props, ref) => (
  <button id="custom-button" ref={ref}>
    {props.children}
  </button>
));class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {};
    this.buttonRef = React.createRef();
  } componentDidMount() {
    this.buttonRef.current.focus();
  } render() {
    return <Button ref={this.buttonRef}>Click Me</Button>;
  }
}
```

在上面的代码中，我们有一个通过使用传入回调的`React.forwardRef`方法创建的`Button`组件。

回调函数有一个`props`参数，它包含从父组件传入的属性，还有一个`ref`对象，它包含从父组件传入的引用。

然后我们从参数中设置`button`元素的`ref`属性为`ref`。

然后我们可以通过调用`App`的构造函数中的`React.createRef`在`App`组件中创建新的 ref。然后我们将它传递给`Button`的`ref`属性，这样我们就可以从`button`元素访问 ForwardRef。

这意味着`this.buttonRef`正在引用`button`元素，因为我们将它传递给了`Button`，后者通过我们传递给`React.forwardRef`的回调将`this.buttonRef`传递给了`button`元素。

然后在`App`组件中，我们调用:

```
this.buttonRef.current.focus();
```

在`componentDidMount`中，从`App`中聚焦`Button`中的`button`。

![](img/cb93841346a06b08954ced9f2a2580bc.png)

[Jeremy Yap](https://unsplash.com/@jeremyyappy?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 转发高阶元件中的参考

我们还可以在高阶元件中转发 ref。为此，我们编写如下代码:

```
const Button = ({ forwardedRef, children }) => (
  <button ref={forwardedRef}>{children}</button>
);function logProps(Component) {
  class LogProps extends React.Component {
    componentDidMount() {
      console.log(this.props);
    } render() {
      const { forwardedRef, ...rest } = this.props;
      return <Component forwardedRef={forwardedRef} {...rest} />;
    }
  } return React.forwardRef((props, ref) => {
    return <LogProps {...props} forwardedRef={ref} />;
  });
}class App extends React.Component {
  constructor(props) {
    super(props);
    this.buttonRef = React.createRef();
  } componentDidMount() {
    this.buttonRef.current.focus();
  } render() {
    const ButtonLogProp = logProps(Button);
    return <ButtonLogProp ref={this.buttonRef}>Click Me</ButtonLogProp>;
  }
}
```

在上面的代码中，我们有`logProps`高阶组件。`logProps`组件取一个`Component`。

`logProps`通过使用一个返回`LogProps`的回调函数调用`React.forwardRef`，返回一个带有转发引用的组件。

在`App`组件中，我们将`this.buttonRef`作为值传递给`ButtonLogProp`。

`ButtonLogProp`是通过调用传入了`Button`组件的`logProps`创建的。这意味着`Component`是`logProp`中的`Button`。

然后在`LogProp`组件的`render`方法中返回`Component`，也就是`Button`。

在`logProp`高阶组件的末尾，我们返回从`React.forwardRef`返回的组件。我们传入`forwardRef`的回调返回`LogProps`组件，其中包含传入的转发引用。

然后所有的道具都将到达`Button`组件，该组件将`button`元素的`ref`设置为`this.buttonRef`，它首先被传入`logProp`，然后被传入`LogProp`，`React.forwardRef`返回一个组件，其中转发的 ref 被传入`Button`组件，然后传入`button`元素。

换句话说，我们通过 ref 从`App`，到`logProp`，到`LogProp`，然后用`React.forwardRef`转发到`Button`，再到`button`。

# 结论

我们可以通过转发引用来使用来自外部组件的引用。这让我们可以在一个组件内操作来自外部组件的 DOM 元素。

转发参考也适用于高阶元件。我们要做的就是用传入的 forwarded ref 在`React.forwardRef`回调中的高阶组件中返回我们想要返回的组件。