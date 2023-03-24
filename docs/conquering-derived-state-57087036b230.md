# 征服派生状态

> 原文：<https://levelup.gitconnected.com/conquering-derived-state-57087036b230>

## 如何识别和替换 React 组件中的派生状态

![](img/1a18ca52418dddc14731cc2b522af340.png)

照片由[里卡多·克鲁斯](https://unsplash.com/@mavrick?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这篇文章最初发表在我的博客上[。看看我的其他文章吧！](https://mskelton.dev/blog/conquering-derived-state)

几周前，我在 Widen 的前端开发同事就我们的一个共享 React 组件进行了一次谈话，我们正在努力在我们的一个应用程序中实现该组件。虽然大多数 React 组件是完全受控的(通过 props)或完全不受控的(内部状态)，但该组件使用了所谓的“派生状态”，即在某种程度上由 props 控制的内部状态。虽然派生状态看起来是一个好的解决方案，但是它经常会导致比它试图解决的问题更多的问题。

我在本文中的目标并不是说服您应该避免派生状态。React 有一篇关于派生状态的极好的[博客文章，它很好地解释了为什么派生状态是一种反模式。本文的重点将是关于如何识别组件中的派生状态以及派生状态的几种替代方案。](https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html)

# 识别派生状态

![](img/97e6242af3d5e1625dbd1fe4c55a5f13.png)

照片由[通讯社跟随](https://unsplash.com/@olloweb?utm_source=medium&utm_medium=referral)于 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在我继续之前，让我提供几个派生状态的例子来帮助您开始识别派生状态模式。我将从基于类的组件开始，因为它们比基于函数的组件更容易识别。

```
class PartiallyControlledInput extends React.Component {
  state = {
    value: this.props.value,
  }; componentWillReceiveProps(nextProps) {
    if (nextProps.value !== this.props.value) {
      this.setState({ value: nextProps.value });
    }
  } render() {
    return (
      <input
        onChange={(e) => this.setState({ value: e.target.value })}
        value={this.state.value}
      />
    );
  }
}
```

在这个例子中，我们有一个部分受控输入字段的组件。组件在内部管理它的状态，但是它也允许父组件通过改变`value`属性来更新状态。这个例子的关键部分是`componentWillReceiveProps`,它是一个红色的标志，表示组件的状态来自于 props。

因为`componentWillReceiveProps`已经被弃用，React 有了一个更新的生命周期方法，名为`getDerivedStateFromProps`。这个方法几乎不需要例子，因为它的名字就说明了一切，但是一个简单的例子不会有什么坏处。

```
class PartiallyControlledInput extends React.Component {
  state = {
    value: this.props.value,
    prevFormId: this.props.formId,
  }; static getDerivedStateFromProps(props, state) {
    if (props.formId !== state.prevFormId) {
      return {
        prevFormId: props.formId,
        value: props.value,
      };
    } return null;
  } render() {
    return (
      <input
        onChange={(e) => this.setState({ value: e.target.value })}
        value={this.state.value}
      />
    );
  }
}
```

如您所见，`getDerivedStateFromProps`变得更加复杂，因为我们需要在状态中存储一个属性值，所以当属性改变时，我们可以基于属性重置状态。

随着我们转向基于功能的组件，派生状态有点难以识别，因为你必须寻找的不仅仅是`componentWillReceiveProps`或`getDerivedStateFromProps`。

```
function PartiallyControlledInput(props) {
  const [value, setValue] = useState(props.value); useEffect(() => {
    setValue(props.value)
  }, [props.value]) return (
    <input
      onChange={(e) => setValue(e.target.value)}
      value={value}
    />
  );
}
```

正如你所看到的，前面的例子是派生状态的一个更微妙的用法。当`props.value`改变时，将运行一个效果，用新的属性值设置输入值。这与我们上面展示的第一个例子本质上是相同的，并且遭受完全相同的问题。仅仅因为它是用钩子编写的，并不意味着它比基于类的等价类神奇地更好！

# 派生状态的替代方案

![](img/80fb6c04a70caa23ce93fd1154bec56b.png)

Javier Allegue Barros[在](https://unsplash.com/@soymeraki?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

现在我们已经看到了派生状态的一些例子，让我们来看看一些替代的解决方案。

## 带钥匙的完全不受控制的组件

第一种选择是使组件完全不受控制，并使用 React 的特殊`key`道具，这将创建组件的一个新实例，而不是更新现有的实例。这包括用提供的初始值重新初始化组件状态。

```
function FullyUncontrolledInput({ initialValue }) {
  const [value, setValue] = useState(initialValue); return (
    <input
      onChange={(e) => setValue(e.target.value)}
      value={value}
    />
  );
}
```

要使用这个组件，我们只需要提供一个`initialValue`道具和一个`key`道具。要重新初始化输入的内部状态，我们只需更改`key`。在下面的代码块中，我们使用了一个名为`formId`的变量，当表单需要重置时，我们可以更改这个变量。

```
function Form() {
  return (
    <FullyUncontrolledInput key={formId} initialValue="Initial" />
  )
}
```

## 完全受控组件

下一个选项是让输入完全受控，并让父组件管理其状态。该选项要求我们向输入组件添加一个`onChange`属性，这样父组件就可以监听变化并相应地更新状态。

```
function FullyControlledInput({ value, onChange }) {
  return (
    <input
      onChange={(e) => onChange(e.target.value)}
      value={value}
    />
  );
}
```

有了完全受控的输入组件，我们现在可以向表单组件添加一个简单的`useState`钩子来管理输入的状态。

```
function Form() {
  const [value, setValue] = useState("Initial"); return (
    <FullyControlledInput onChange={setValue} value={value} />
  );
}
```

## 定制挂钩！

虽然前面的例子听起来像是简单的解决方案，但是许多真实世界的组件要比那些简单的例子复杂得多。在这些情况下，创建一个定制挂钩可能正是您在没有大量样板文件的情况下实现高级定制所需要的。

例如，考虑一个日期选择器组件，它接受当前所选日期和月份的属性，以及当用户更改日期或月份时的事件处理程序。与其让表单组件管理所有的状态和事件处理程序，不如将该逻辑包装在一个自定义的`useDatePicker`钩子中！

```
function useDatePicker(initialValues = { day: 1, month: "Jan" }) {
  const [month, setMonth] = useState(initialValues.month);
  const [day, setDay] = useState(initialValues.day); return {
    datePickerProps: {
      day,
      month,
      onDayChange: setDay,
      onMonthChange: setMonth,
    },
    day,
    month,
    setDay,
    setMonth,
  };
}
```

现在，我们可以很容易地调用表单组件中的`useDatePicker`钩子，并将`datePickerProps`传递给日期选择器组件，该组件将添加管理组件状态所需的所有属性。

```
function Form() {
  const { datePickerProps, day, month } = useDatePicker(); useEffect(() => {
    // Do something when day or month changes
  }, [day, month]); return <DatePicker {...datePickerProps} theme="dark" />;
}
```

因为挂钩不直接绑定到组件，所以我们可以实现高级别的定制。例如，如果我们想在天气变化时触发一些副作用，我们可以简单地添加一个依赖于`day`变量的`useEffect`钩子。或者，如果我们需要定制`onDayChange`逻辑，我们可以用定制的`onDayChange`属性覆盖`datePickerProps`中返回的属性。我们甚至可以在表单组件中创建一个 reset 按钮，单击该按钮，使用`setDay`和`setMonth`函数将日期选择器重置为当前日期。可能性是无穷无尽的，所以发挥你的想象力，想出一些伟大的东西！