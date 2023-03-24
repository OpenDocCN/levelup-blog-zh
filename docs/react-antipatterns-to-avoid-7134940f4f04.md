# 对反模式做出反应以避免

> 原文：<https://levelup.gitconnected.com/react-antipatterns-to-avoid-7134940f4f04>

![](img/ec09d8cbf289ae45165d6fa2d1d56e8a.png)

照片由[卢卡·布拉沃](https://unsplash.com/@lucabravo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

React 是构建现代交互式前端 web 应用最常用的前端库。它还可以用来构建移动应用程序。

在本文中，我们将研究一些我们应该避免的 React 反模式。

# bind()和组件中的函数

当我们在 React 类组件中调用`bind`时，我们不应该在我们的道具中重复调用它们。相反，我们应该在`constructor`中调用`bind`，这样当我们将类组件方法作为道具传入时，就不必匆忙调用`bind`。

例如，我们可以编写以下代码来实现这一点:

```
import React from "react";export default class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name: ""
    };
    this.updateValue = this.updateValue.bind(this);
  }
  updateValue(e) {
    this.setState({
      name: e.target.value
    });
  }
  render() {
    return (
      <>
        <form>
          <input onChange={this.updateValue} value={this.state.name} />
        </form>
        <p>{this.state.name}</p>
      </>
    );
  }
}
```

在上面的代码中，当输入值改变时，我们有一个调用`updateValue`方法的输入。

我们有:

```
this.updateValue = this.updateValue.bind(this);
```

以便它总是将组件绑定为`this`的值。

现在我们不必在任何地方重复做一遍。此外，我们不会在每次发出`onChange`事件时都创建新函数，因为我们没有调用返回新函数的`bind`。

当我们的应用程序变大时，动态创建方法对性能的影响在某些情况下会很明显。

所以与其写:

```
<input onChange={this.updateValue.bind(this)} value={this.state.name} />
```

我们在上面写下我们所拥有的。

# 缺少密钥属性或在密钥属性中使用索引

当我们呈现列表时，prop 很重要，即使它有时看起来什么也不做。唯一的`key`属性值让 React 正确识别列表项。

`key`属性值用于将原始树中的子节点与后续树中的子节点进行匹配。

因此，使用数组的索引也是不好的，因为它可能导致 React 呈现错误的数据。

最好使用唯一的 id 作为 key prop 的值。我们可以创建一个具有唯一 id 的数组，用作键，如下所示:

```
import React from "react";
import { v4 as uuidv4 } from "uuid";const arr = [
  {
    fruit: "apple",
    id: uuidv4()
  },
  {
    fruit: "orange",
    id: uuidv4()
  },
  {
    fruit: "grape",
    id: uuidv4()
  }
];export default function App() {
  return (
    <div className="App">
      {arr.map(a => (
        <p key={arr.id}>{a.fruit}</p>
      ))}
    </div>
  );
}
```

在上面的代码中，我们使用了`uuid`包来创建一个唯一的 UUID，用作`key`属性的值。这样，唯一的 id 几乎没有冲突的机会。

这样，ID 是惟一的和可预测的，我们不必自己想办法为每个项目生成惟一的 ID。

# 组件名称

组件名必须以大写字母开头。例如，如果我们编写下面的代码，我们将从 React 得到一个错误:

```
import React from "react";const foo = () => <p>foo</p>;export default function App() {
  return (
    <div className="App">
      <foo />
    </div>
  );
}
```

如果我们运行上面的代码，React 将给出错误信息'<foo>在这个浏览器中无法识别。如果您打算呈现 React 组件，请以大写字母开始其名称。</foo>

因此，我们应该用大写字母来命名我们的组件:

```
import React from "react";const Foo = () => <p>foo</p>;export default function App() {
  return (
    <div className="App">
      <Foo />
    </div>
  );
}
```

然后我们会看到‘foo’如我们所料的显示在屏幕上。

![](img/3feb34adb2da5d4a0d41db5d9165e75e.png)

照片由[查塔纳林·普兰纳潘](https://unsplash.com/@chatnarin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 关闭每个元素

所有组件标签都必须有一个结束标签，或者在自结束组件的末尾有一个斜杠。例如，如果我们有:

```
import React from "react";const Foo = () => <p>foo</p>;export default function App() {
  return (
    <div className="App">
      <Foo>
    </div>
  );
}
```

我们将得到一个语法错误，因为我们缺少了一个结束斜杠或标签:

```
<Foo>
```

为了让应用程序运行，我们改为编写:

```
import React from "react";const Foo = () => <p>foo</p>;export default function App() {
  return (
    <div className="App">
      <Foo />
    </div>
  );
}
```

`<Foo></Foo>`也管用。

# 结论

当我们编写 React 组件时，很容易出错。我们必须记住组件名称的第一个字母要大写。此外，我们必须关闭组件的标签，或者为自关闭组件添加一个关闭斜杠。

同样，我们应该首先调用`bind`，这样我们就不必重复调用它来绑定到基于类的组件中`this`的正确值。

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)