# React 挂钩简介

> 原文：<https://levelup.gitconnected.com/an-introduction-to-react-hooks-50281fd961fe>

理解内置钩子和定制钩子的创建过程

![](img/8fb2ddcd1935964cb1fd25534e5bbe04.png)

React 钩子是 React 的一个很好的补充，它完全改变了我们编写代码的方式。从 16.8.0 版本开始，React 中引入了钩子。

在 React hooks 之前，没有办法在功能组件中使用状态和生命周期方法，这就是为什么功能组件被称为`Stateless Functional Components`。现在，随着 React 钩子的引入，我们可以在功能组件中使用生命周期方法和状态。使用钩子使我们的代码更简单、更短、更容易理解。

## **安装**

要开始使用 React 钩子，需要使用最新版本的`create-react-app`。如果您使用自己的 webpack 安装 React，如本文中的[所述，您需要使用安装最新版本的`react`和`react-dom`](https://javascript.plainenglish.io/webpack-and-babel-setup-with-react-from-scratch-bef0fe2ae3e7?source=friends_link&sk=880a6b9a35fb638eef19e5e99276428e)

```
npm install react@latest react-dom@latest
```

现在让我们开始探索 React 挂钩。

> 挂钩是让您“挂钩”功能组件中的反应状态和生命周期特性的功能。

React 提供了许多内置的钩子，还允许我们创建自己的定制钩子。

让我们来看看一些最重要的内置挂钩。

## **使用状态挂钩**

这是 React 提供的第一个钩子，它允许我们在功能组件内部使用状态。

`useState`钩子接受一个参数，这个参数是状态的初始值。

在基于类的组件中，`state`总是一个对象，但是当使用`useState`时，你可以提供任何值作为初始值，如`number`、`string`、`boolean`、`object`、`array`、`null`等。

`useState`钩子返回一个数组，它的第一个值是状态的当前值，第二个值是我们用来更新状态的函数，类似于`setState`方法。

让我们举一个使用状态的基于类的组件的例子，我们将使用钩子把它转换成一个功能组件。

演示:【https://codesandbox.io/s/delicate-thunder-xdpri 

让我们把上面的代码转换成使用钩子。

演示:[https://codesandbox.io/s/elegant-heyrovsky-3qco5](https://codesandbox.io/s/elegant-heyrovsky-3qco5)

让我们来理解上面的代码

1.为了使用`useState`钩子，我们需要像在第一行中那样导入它。

2.在 App 组件内部，我们通过传递 0 作为初始值并使用析构语法来调用`useState`，我们将`useState`返回的数组值存储到`counter`和`setCounter`变量中。

3.像在`setCounter`中一样，在用于更新状态的函数名前面加上`set`关键字是一种常见的约定。

4.当我们单击 increment 按钮时，我们定义了一个内联函数，并通过传递更新后的`counter`值来调用`setCounter`函数。

5.注意，因为我们已经有了`counter`值，所以我们使用`setCounter(counter + 1)`来增加计数器

6.由于内联点击处理程序中只有一条语句，所以没有必要将代码移到单独的函数中。尽管如果处理程序内部的代码变得复杂，您也可以这样做。

**我们可以在单个功能组件中有多个** `**useState**` **调用:**

演示:[https://codesandbox.io/s/long-framework-n1tyn](https://codesandbox.io/s/long-framework-n1tyn)

正如你在上面的代码中看到的，我们已经声明了两个`useState`调用，用户名和计数器都是相互独立的。我们可以根据需要添加任意数量的`useState`通话。

**使用 useState 钩子时状态不合并:**

如果您在`useState`中使用`object`需要注意的一点是，在更新状态时，状态不会被合并。

让我们看看当我们将`username`和`counter`组合成一个对象来更新状态时会发生什么。

演示:[https://codesandbox.io/s/sleepy-darkness-6jz9b](https://codesandbox.io/s/sleepy-darkness-6jz9b)

如果您运行代码并尝试增加计数器，它会工作，但一旦您尝试在输入字段中键入内容，计数器值就会消失，当您再次尝试增加计数器时，它会显示为`NaN`，并且您也会在控制台中得到一个警告。

**这是因为当我们使用** `**setState**` **方法设置状态时，使用** `**useState**` **中的一个对象会完全替换该对象而不是合并它。**

在上面的代码中，初始状态值是

```
{ counter: 0, username: "" }
```

但是当我们调用`increment`方法时，它就变成了

```
{ counter: 1 }
```

于是`username`就这样失去了。

如果您首先在输入字段中键入内容，那么

```
{ counter: 0, username: "" }
```

成为

```
{ username: "new_value" }
```

因此`counter`丢失。

> 因此，建议使用多个`useState`调用，而不是使用单个`useState`来存储对象。

如果你还想使用`useState`中的一个对象，你需要像这样手动合并之前的状态

```
const handleOnClick = () => { 
  setState(prevState => ({
   ...prevState,
   counter: prevState.counter + 1
  }));
};const handleOnChange = event => {
 const value = event.target.value;
 setState(prevState => ({
   ...prevState,
   username: value
 }));
};
```

演示:[https://codesandbox.io/s/strange-dawn-s0nyi](https://codesandbox.io/s/strange-dawn-s0nyi)

## **使用特效挂钩**

React 提供了一个`useEffect`钩子，使用它我们可以在功能组件中实现生命周期方法。

`useEffect`钩子接受一个函数作为第一个参数，可选数组作为第二个参数。

`useEffect`允许您在功能组件中执行副作用。

> 数据获取、设置订阅和手动更改 React 组件中的 DOM 都是副作用的例子。

`useEffect`接受两个参数:一个函数和一个可选数组。

`useEffect`与`componentDidMount`、`componentDidUpdate`和`componentWillUnmount` 组合在一起的作用相同。

看看下面的代码

演示:[https://codesandbox.io/s/stupefied-darwin-qpcmm](https://codesandbox.io/s/stupefied-darwin-qpcmm)

如果您运行该代码，您将会看到`useEffect`中提供的功能在页面加载时(即组件安装时)被立即调用。当组件在任何状态或属性改变时被重新呈现时，它也会被调用。

所以当你点击增量按钮时，`useEffect`钩子会被再次调用。

因此它服务于`componentDidMount`和`componentDidUpdate`生命周期方法的目的。

*如果你想让这个效果只在组件挂载时被调用，你需要提供一个空数组作为* `*useEffect*` *调用的第二个参数。*

```
useEffect(() => {
  console.log("useEffect called");
}, []);
```

所以现在，这个效果只有在组件被挂载的时候才会被调用，再也不会被调用了。

演示:[https://codesandbox.io/s/gifted-newton-71vzl](https://codesandbox.io/s/gifted-newton-71vzl)

看看下面的代码

演示:[https://codesandbox.io/s/long-framework-n1tyn](https://codesandbox.io/s/long-framework-n1tyn)

如您所见，当计数器和用户名改变时，`useEffect`被执行。

如果你想让它只在`counter`改变时执行，你需要在依赖数组中提到它，这是`useEffect`的第二个参数。

```
useEffect(()=>{
console.log("This will be executed when only counter is changed");
},[counter]);
```

理解这一点非常重要，因为当计数器改变时，如果你在`useEffect`中做一些计算，当用户名或任何其他属性或状态改变时，执行这些代码是没有意义的。

> 因此，通过在 dependencies 数组中提供变量，只有当这些变量被更改时，效果才会被执行。

这是一个很大的改进，因为它不再需要我们以前在`componentDidUpdate`方法中使用的额外条件

```
componentDidUpdate(prevProps) {
 if(prevProps.counter !== this.props.counter) {
  // do something
 }
}
```

因此，使用`useEffect`并提及依赖关系消除了将先前属性或状态值与当前属性或状态值进行比较的需要，因为`useEffect`内的代码将仅在依赖关系改变时执行。

如果有多个依赖项，可以在依赖项数组中将其指定为逗号分隔的值。

```
useEffect(()=>{
 console.log("This will be executed when any of the dependency is changed");
},[counter, some_other_value]);
```

## **清理效果**

如果您从`useEffect`返回一个函数，它将在组件被卸载之前被调用。所以和`componentWillUnmount`方法差不多。

假设你正在使用`setInterval`函数，那么你可以清除`useEffect`返回的函数中的区间。

> 该清理效果在组件卸载时以及由于后续渲染而重新运行 useEffect 之前运行

演示:[https://codesandbox.io/s/sleepy-wood-biu89](https://codesandbox.io/s/sleepy-wood-biu89)

在上面的代码中，我们添加了 scroll 事件处理程序，当组件被挂载时，它将只被注册一次，因为我们在`useEffect`依赖列表中传递了一个空数组。

*当页面滚动时，这段代码在控制台中显示一条消息。*

当我们点击`unmount component`按钮时，组件将被移除，从`useEffect`返回的函数将被调用，我们移除滚动事件处理程序并将消息`“component unmounted”`打印到控制台。

值得注意的是，在子组件中，每次重新渲染父组件时都会调用`useEffect`。

看看下面的代码

完整代码和演示:[https://codesandbox.io/s/jolly-tesla-gj170](https://codesandbox.io/s/jolly-tesla-gj170)

这里，我们添加了一个新的 todo。因此，对于每个添加的 todo，TodoList 组件将被重新呈现，因此，TodoList 中定义的`useEffect`将被调用，以便在控制台中打印消息。

所以如果你不想重新渲染 TodoList 的效果，你需要在第二个参数`useEffect`中传递一个空数组`[]`作为依赖。

```
useEffect(() => {
 console.log("This will be executed only once");
}, []);
```

**单个组件中的多种使用效果:**

您也可以在单个功能组件中有多个`useEffect`调用，每个调用执行不同的任务，React 将按照组件中定义的顺序调用它们。

## **useRef 挂钩**

`useRef`钩子允许我们访问任何 DOM 元素，这与在 React 中使用`ref`是一样的。

它具有以下语法。

```
const refContainer = useRef(initialValue);
```

`initialValue`是可选的。

演示:[https://codesandbox.io/s/damp-water-fye8r](https://codesandbox.io/s/damp-water-fye8r)

如您所见，我们已经使用

```
const usernameRef = useRef();
```

并将其分配给输入字段。所以现在我们可以通过使用`usernameRef.current`属性随时访问输入字段。

`useRef`与 React `ref`并不完全相同，因为`useRef`返回一个可变的 ref 对象，其`.current`属性被初始化为传递的参数(`initialValue`)。

> 返回的对象将在组件的整个生存期内保持不变。

演示:[https://codesandbox.io/s/black-cache-e5vlb](https://codesandbox.io/s/black-cache-e5vlb)

在上面的代码中，我们创建了一个计时器，显示每秒钟更新的时间。当我们点击`unmount component`按钮时，我们清除`setInterval`从而停止计时器。

还要注意，我们添加了另一个`useEffect`调用，在 5 秒钟后清除计时器。

要记住的关键点是，我们能够在第一个`useEffect`中访问相同的`interval`变量的值，即使它在第二个`useEffect`中被赋值。如果我们没有使用`useRef`而是使用了普通变量，那么在第一个`useEffect`中它将不可用，因为`useEffect`为效果的每次执行创建了一个新函数。

## **useReducer 挂钩**

React 提供了一个`useReducer`钩子，允许我们使用 redux 来处理复杂的计算，而不需要安装 redux 库。

因此，它有助于避免创建样板 redux 动作、reducer 代码。

当您有涉及多个子值的复杂状态逻辑或者下一个状态依赖于前一个状态时，`useReducer`通常比`useState`更好。

`useReducer`的语法如下:

```
const [state, dispatch] = useReducer(reducer, initialState);
```

`useReducer`接受`reducer`和`initialState`并返回`current state`和一个`dispatch`函数，我们可以用它来`dispatch`动作。

我们通过一个例子来了解一下。我们将创建一个待办事项列表组件，允许我们添加和删除待办事项。

让我们创建一个`reducer`来处理 todo。

```
const todosReducer = (state, action) => {
 switch (action.type) {
  case "ADD_TODO":
   return [...state, action.todo];
  case "REMOVE_TODO":
   return state.filter(todo => todo !== action.todo);
  default:
   return state;
 }
};
```

我们将`useReducer`的`initialState`中`todosReducer`的初始状态初始化为

```
const initialState = [];
const [state, dispatch] = useReducer(todosReducer, initialState);
```

并分派像这样的动作

```
dispatch({ type: "ADD_TODO", todo: value });
```

完整代码和演示:[https://codesandbox.io/s/admiring-bose-jw6wx](https://codesandbox.io/s/admiring-bose-jw6wx)

如您所见，`useReducer`钩子使得管理 redux 状态变得非常容易，而不需要实际使用 redux，这是一个很大的改进。

但这并不意味着 redux 现在就没用了。如果您正在管理更大的状态或者有多个 reducers，那么您应该使用 redux 来处理这个问题。

如果你想在单个组件中处理复杂的状态，就让它变得简单。

## **创建自定义挂钩**

React 允许我们使用内置钩子创建自己的定制钩子。这允许我们将组件逻辑提取到可重用的功能中。

让我们从这里的[中取出之前的定时器代码](https://codesandbox.io/s/black-cache-e5vlb)，我们将把它转换成一个定制的可重用钩子。

演示:[https://codesandbox.io/s/infallible-visvesvaraya-qkwdw](https://codesandbox.io/s/infallible-visvesvaraya-qkwdw)

这里，我们封装了在`useTimer`函数中显示计时器的代码，并从该函数返回更新的时间。

所以现在任何想要显示时间的组件，都可以调用`useTimer`函数并使用这个定时器钩子。

> 用`useTimer`这样的`use`关键字来`begin`自定义钩子名称是很常见的约定。

## **使用挂钩时需要注意的事项**

*   只调用顶层的钩子**。不要在循环、条件或嵌套函数中调用钩子。通过遵循这条规则，您可以确保每次组件呈现时都以相同的顺序调用钩子。这使得 React 能够在多个`useState`和`useEffect`调用之间正确地保持钩子的状态。**
*   仅从 React 函数组件调用钩子**。不要从常规的 JavaScript 函数中调用钩子。但是你可以从你自己定制的钩子中调用它。**

**要了解如何使用 Async/await 和** `**useEffect**` **钩子处理 API 调用，请查看本文。**

看看我最近出版的[掌握 Redux](https://master-redux.yogeshchavan.dev/) 课程。

在本课程中，您将构建 3 个应用程序以及一个点餐应用程序，您将了解:

*   基本和高级冗余
*   如何管理数组和对象的复杂状态
*   如何使用多个减速器管理复杂的冗余状态
*   如何调试 Redux 应用程序
*   如何在 React 中使用 Redux 使用 react-redux 库让你的 app 反应性。
*   如何使用 redux-thunk 库处理异步 API 调用等等

最后，我们将从头开始构建一个完整的[订餐应用](https://www.youtube.com/watch?v=2zaPDfCKAvM)，集成 stripe 以接受支付，并将其部署到生产中。

**别忘了直接在你的收件箱** [**这里**](https://yogeshchavan.dev) **订阅我的每周时事通讯，里面有惊人的技巧、诀窍和文章。**