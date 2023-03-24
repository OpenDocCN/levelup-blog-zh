# React 开发人员的基本术语

> 原文：<https://levelup.gitconnected.com/essential-terms-for-a-react-developer-feb88be0206b>

![](img/602bc945b722fc39d265e5dfb2ede218.png)

本文是 React for 软件开发的概述。它为希望使用 React 或了解更多相关知识的人提供了基本的术语和概念。我一年多前开始使用 [React.js](https://reactjs.org) ，并用它构建了 [Spandraw](https://spandraw.com) 。这篇文章涵盖了我在这个过程中学到的最重要的东西。

如果你正在寻找现代 web 开发术语的概述，你可能会发现我之前关于 web 开发的文章很有用。它解释了 React 是什么以及为什么使用它。

## JSX (JavaScript XML)

JSX 文件代表包含 XML 语法(如 HTML)的 JavaScript 文件。使用 React 时，所有组件都应该用 JSX 语法编写。JSX 使得将 HTML、JS 和 CSS 组合成一个组件成为可能，通常作为一个单独的文件，这是 React 的主要目的之一。

## 文档对象模型

界面中表示网页上 HTML 元素的 DOM。例如，文章中的每一段可以在一个`<p />`元素中表示，而一个图像可以在一个`<img />`元素中表示。

React 在网页上对这些元素有自己的表示。这被称为 React DOM，这个概念被称为虚拟 DOM。其他前端框架如 Angular 和 Vue 使用类似的虚拟 DOM 原理来操作网页上的 HTML。

## SPA(单页应用)

单页应用程序是单个网页，当导航到 URL 内的子域时，它会更改自己的内容，而不是从 web 服务器加载不同的网页。这是因为 JavaScript 能够改变页面上的 HTML 来响应用户的动作。

## 入口点

入口点是托管应用程序的基本 URL。以我自己在[app.spandraw.com](https://app.spandraw.com)的应用为例，这将是入口点，这个位置将包含根目录中的一个 index.html 文件。这个 index.html 只需要包含一个`<div id=”root” />`元素，以及适当的头。加载了整个“应用程序”的 JavaScript，然后可以将这个 div 元素作为目标，并替换为必要的内容。通常，根组件被称为“index.jsx”。

## 路由器

当用户导航到 react 应用程序中的页面[app.spandraw.com/draw](https://app.spandraw.com/draw)时，位于[app.spandraw.com](https://app.spandraw.com)的 index.html 仍然被加载。子 url '/draw '被客户端路由器识别，所需的组件呈现在根 div 元素中。

在同一个应用程序内更改页面意味着可以使用相同的组件，并且可以在这个应用程序内的不同页面之间以“状态”共享数据。

```
<Switch>
  <Route exact path="/" component={Home} />
  <Route exact path="/draw" component={Draw} />
</Switch>
```

## 生命周期方法

生命周期方法是在 React 类组件内部的特定场景中被调用的函数。这些仅在类组件中可用，在使用钩子的函数组件中不可用。最常见的生命周期方法是`componentDidMount()`、`render()`和`componentDidUpdate()`。

## componentDidMount()

`componentDidMount()`在组件创建时被调用。这对于执行只需要在页面加载时执行一次的操作非常有用，例如，检查用户是否登录。

## 渲染()

`render()`在组件每次更新时被调用，包括创建时。React 仅在组件的“状态”或“属性”改变时更新组件。任何需要显示的文本和 HTML 元素都需要从这个方法返回，使用 JSX 语法。

来自[官方 React 文档的示例:](https://reactjs.org/docs/react-component.html#overview)

```
render() {
  return <h1>Hello, {this.props.name}</h1>
}
```

## componentDidUpdate(prevProps，prevState)

`componentDidUpdate()`在每次组件更新时被调用，而不是在创建组件时被调用。Render()只能访问当前状态和属性，而 componentDidUpdate()也可以访问以前的状态和属性。

当 render()被调用时，你知道有些东西已经改变了，但是你不知道是什么。在 componentDidUpdate()中，您可以具体检查发生了什么变化，并只执行该条件的逻辑。

来自[官方 React 文档的示例:](https://reactjs.org/docs/react-component.html#componentdidupdate)

```
componentDidUpdate(prevProps) {
  // Typical usage (don't forget to compare props):
  if (this.props.userID !== prevProps.userID) {
    this.fetchData(this.props.userID);
  }
}
```

## 反应组分

React 的一个主要目的是重用代码，通过将代码分组到一个“组件”中，该组件对应于要呈现给页面的元素。有两种主要类型的组件,“类”组件(标准的 React 组件)或“功能”组件。

类组件有内置的生命周期方法(ComponentDidMount、ComponentDidUpdate 等，见上文)。Function 组件是一个标准的 JavaScript 函数，它使用 react 挂钩来访问状态和 React 生命周期功能，而不使用类。

## 小道具

Props 用于在连接的组件之间传递数据(父组件与子组件，反之亦然)。react 中的约定是在一个名为“props”的对象中传递数据。对于类组件，可以在生命周期方法中使用“this.props”来访问它。对于函数组件，对象“props”作为参数传递给函数。

当传递给组件的任何道具发生变化时，组件都会更新(重新渲染)，因此任何相关的生命周期方法都会被调用，比如 render()和 componentDidUpdate()等等。

## 状态

状态在组件中用于保存更改的数据，但不需要向其他组件公开。props 和 state 的主要区别在于组件能够更新自己的状态，但是 props 只能在传入时更改。在类组件中，通过调用`this.setstate({…})`来改变状态。在函数组件中，通过使用 React 中的 useState 钩子来改变状态。

与 props 一样，如果任何状态发生变化，组件将更新并调用相关的 React 生命周期方法。

## 纯成分

使用 React。PureComponent 类似于我们熟悉的 React。除了 PureComponent 实现了“shouldComponentUpdate()中的浅层属性和状态比较”。使用“Component”要求用户在必要时手动执行 shouldComponentUpdate()来优化更新。这实际上意味着 React 自动为 PureComponent 中的所有属性和状态实现类似这样的东西:

```
shouldComponentUpdate(nextProps, nextState) {
  // update component if any of the props or state is changed using a shallow comparison
  If (this.props.isLoading !== nextProps.isLoading) return true;
  If (this.state.isButtonClicked !== nextState.isButtonClicked) return true;

  // otherwise do not update component
  return false;
}
```

对于大多数组件来说，PureComponent 通常比 Component 更具性能，因为它避免了不必要的更新，除非相关的属性或状态发生了变化。

但是，如果使用更复杂的数据，并且有必要检查项目引用(而不仅仅是值)是否发生了变化，则应该使用“组件”。当组件包含一个 props 或 state 中的对象或数组时，那么您应该考虑是否可以接受浅层比较。使用 PureComponent 的风险是组件不会在预期的时候更新。

## 事件绑定

作为 React 初学者，一个特别令人困惑的领域是如何在 React 中绑定事件处理程序。您可能希望向函数传递三种类型的数据来进行事件处理，它们是:

1.  访问 React 组件上的属性或状态
2.  “事件”本身(通常是点击、按键或表单值)
3.  任何其他自定义值

这让我很困惑，因为有太多的方法可以做到这一点，特别是从 ES6 开始就有了箭头功能，而且不同的人用不同的方法做这件事。然而，滥用 arrow 函数会导致代码可读性差，并触发不必要的渲染，损害性能。

我个人的方法是:

```
handleClick() {
  console.log(‘handleClick does not need to be an arrow function’)
}

render() {
  return <SomeButton onClick={this.handleClick} />
}
```

如果您忘记将“this”绑定到要传递给该方法的组件，您将得到类似“无法读取 undefined 的 property 'props'”的错误，因为“this”是未定义的。当您收到这个错误时，首先要检查的是您已经为您的事件处理方法绑定了“this”。

```
constructor(props) {
  super(props);
  this.handleGetDataFromProps = this.handleGetDataFromProps.bind(this);
}

handleGetDataFromProps() {
  const { data } = this.props;
  console.log(‘handleGetDataFromProps has access to data from component props: ’, data)
}

render() {
  return <SomeButton onClick={this.handleGetDataFromProps} />
}
```

在案例 3 中，在事件处理方法中，通常会基于输入值执行 setState 或触发 redux 操作。

github 上的 [airbnb react 风格指南是在 react 上使用现代 javascript 的良好实践的良好参考来源。](https://github.com/airbnb/javascript/tree/master/react)

## 反应钩

React 挂钩是在 v16.8 中引入的(2019 年 2 月)。React 文档的描述是“它们让你不用写类就能使用状态和其他 React 特性”。下面是一个例子(摘自 [react 文档](https://reactjs.org/docs/hooks-state.html)):

```
const [count, setCount] = useState(0);

  // Similar to componentDidMount and componentDidUpdate:
  useEffect(() => {
    // Update the document title using the browser API
    document.title = `You clicked ${count} times`;
  });
```

钩子和类的工作方式完全不同，下面的内容可以帮助您决定使用哪一种:

*   类通过生命周期事件工作。
*   钩子通过同步工作。

截至目前(2019 年 12 月)，react 挂钩仍然相对较新，因此与类组件相比，现有的文档和文章较少。如果您有时间，那么尽一切可能尝试一下钩子并阅读文档。据我所知，钩子可以做类能做的任何事情，除了 ComponentDidCatch 生命周期事件。

## Redux

在创建 React 应用程序时，很可能需要在组件之间共享状态。一种方法是将状态作为道具传递给子组件。然而，当引入更多要传递的状态和更多的组件层时，这很快就会变得混乱。还有一个问题是跟踪状态在哪里被改变，并试图跟踪哪个组件改变了状态，这在复杂的应用程序中也会很快变得混乱。

Redux(link)是一个库，采用了管理所有这些状态的最佳实践，采用了脸书创建的“Flux”架构。它通过三个主要原则做到这一点:

1.  使用中央“存储”来保存所有“状态”(有时称为 Redux 存储)。
2.  Redux 存储中的所有“状态”都是只读的。若要更改存储中的任何状态，必须调度 Redux 操作。
3.  为了改变 Redux 存储，使用“reducer”来寻找动作的类型，并相应地改变状态。建议返回一个新的状态，而不是突变之前的状态(即。纯函数)，这在使用数组和对象时尤为重要。

**使用 Redux**

通过使用“mapStateToProps”和“mapDispatchToProps”并将组件包装在“connect”中，可以从任何组件访问此存储。动作通过 mapDispatchToProps 作为函数属性传入。“选择器”函数的使用是可选的，但是我建议您使用它，因为如果您希望重命名存储中的状态或嵌套，您可以只更改选择器函数，而不是转到使用该状态的每个组件并在那里更改 mapStateToProps。对于复杂的 app，建议将 Redux store 划分为对应其用途的多个部分，每个部分都有自己的 reducer。

使用 Redux 的一个主要好处是 Chrome 的 devtools 插件的强大。它允许您查看 Redux 存储中的所有状态、任何被触发的动作，以及存储如何响应动作(即检查减速器是否正常工作)。在我看来，如果你需要构建一个需要伸缩的 React app，那么你需要 Redux。

## Redux 传奇

如果你正在使用 Redux，并且你需要使用异步动作(例如，获取 API，链接动作)，你将会想要使用某种类型的中间件来处理这个。Redux Saga 是一个中间件，它可以拦截 Redux 动作，并根据 API 调用的结果异步执行逻辑。我个人使用 Redux Saga，并发现它非常有用，但根据用例的范围，还有其他选择。

例如，您可以使用 Sagas 来监听“Get API”操作，然后调用 REST API，监听响应。如果得到状态 200 的响应，则返回“API 成功”动作，如果得到状态 400，则返回“API 失败”动作。这个动作然后可以被减速器拾取，并且状态可以相应地改变。当等待 API 请求的响应时，这是设置“加载”状态以呈现微调器的好方法。

## 以打字打的文件

[TypeScript](https://www.typescriptlang.org/) 基本上就是带有类型定义的 JavaScript。TypeScript 文件以*结尾。ts(或*。tsx 如果替换 a *。jsx 文件)，这被自动编译成可以在 web 浏览器中运行的 JavaScript 文件。它是由微软创建的，与用 TypeScript 编写的 VS 代码编辑器配合得特别好。

## **优点:**

*   在代码编辑器中捕获由于不正确的类型而导致的错误，而不是在运行时捕获。
*   协助代码完成(智能感知)和编辑器工具，如“跳转到定义”，节省开发人员大量时间
*   它帮助团队交流代码库和构建应用程序
*   帮助文档库——几乎每个主要的 npm 包都有 typescript 定义

**缺点:**

*   更详细，需要输入更多
*   编译错误会阻止应用程序运行，在原型开发新功能时会令人沮丧，并且只需要记录一些结果
*   不容易从其他地方复制和粘贴

在我看来，TypeScript 的好处远远大于坏处，如果没有它，我不会考虑构建一个新的 React 应用程序。当我构建自己的应用程序 Spandraw 时，我开始只使用 jsDoc，这对于一个小应用程序来说是可以的，但在扩展时很快就遇到了问题。然后转行做 TypeScript，渐渐的学了个里里外外。我用得越多，就越觉得它有价值，这只能是一个好现象。

2019 年的 TypeScript 比 2017/2018 年好了很多，React 社区也真正拥抱了它。如果你正在构建一个需要伸缩的应用程序，并且有很多组件，那么你真的应该考虑使用 TypeScript 和 Redux。

## 样式组件

Styled Components 是一个用 JavaScript 编写 CSS 组件的库，也称为 CSS-in-JS。这允许使用变量，变量可以作为道具传递到“样式组件”中，或者作为常量直接导入。样式化的组件可以和 React 组件包含在同一个文件中，这是我喜欢的样式。这避免了在开发过程中必须在 React 和 CSS 文件之间切换。

与内联 css 相比，它的一个优势是使用与 css 相同的语法，而使用 React 的内联 CSS 需要将 CSS 转换成 JS 对象。这需要将名称中的破折号转换为骆驼大小写，将值转换为字符串，在进行这种转换时很容易出错。

有许多方法可以在 React 应用中实现 CSS，因为默认的 CSS 在使用变量方面有限制。其他一些方法包括 SCSS，较少，CSS 模块。我个人推荐使用 React 的风格化组件，因为它们可以很好地相互补充。

如果你觉得 React 的概述有用，请[在 LinkedIn 上联系我](https://www.linkedin.com/in/howanto/)或[在 Medium 上关注我](https://medium.com/@ho.wan)。我曾经是一名结构工程师，我打算把它和计算一起写下来。在 Spandraw.com查看我自己的 React 应用程序的进度

*原载于 2019 年 12 月 20 日*[*https://spandraw.com*](https://spandraw.com/blog/overview-of-react-553)*。*