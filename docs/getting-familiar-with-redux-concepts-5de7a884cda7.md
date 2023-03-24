# 熟悉 Redux 概念

> 原文：<https://levelup.gitconnected.com/getting-familiar-with-redux-concepts-5de7a884cda7>

![](img/d42c601813c3e26fc12b0a11d636d582.png)

照片由[塞缪尔·泽勒](https://unsplash.com/@samuelzeller?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

Redux 是一个众所周知的状态管理库，经常在 React 应用程序中使用。从 Redux 开始，有很多名字和概念被抛来抛去。这篇文章旨在揭开这些名字和概念的神秘面纱，帮助你更快地学习 Redux。

# 一般概念

我们先来看一些一般概念。

## 应用程序状态与组件状态

国家通常分为两类。应用程序状态和组件状态。Redux 应该处理应用程序状态。那么这几种状态之间有什么区别呢？

## 组件状态

组件状态是组件用于按预期工作的状态。下拉菜单可能有一个名为`isExpanded`的变量。组件维护这种状态，不需要与其他组件共享这种状态。组件仅使用它来知道下拉列表是可见的还是隐藏的。

## 应用状态

应用程序状态是使应用程序工作的状态。在待办事项列表应用程序中，待办事项将是应用程序状态的一部分。

## 不变

不变性意味着数据不应该被修改，而是制作该数据的副本并用新值交换旧值。例如，如果一个对象有一些值，不要只修改需要改变的值。使用当前对象，并用新值代替旧值创建一个新对象。

```
const obj = { 
  firstname: 'John', 
  surname: 'Foe' 
}const newObj = { 
  ...obj,
  surname: 'Doe'
}
```

## 支柱钻井

当 React 组件具有需要一些数据子组件时，会发生正确的钻取。数据通常位于顶层组件中，并通过 props 发送给子组件，这没什么问题，这就是 React 的工作方式。不利的一面是，不需要道具的组件能够将它们传递得更远。

# 重复概念

有一些 Redux 概念也是很好的了解。

## 还原剂

在使用 Redux 时，Reducers 是您经常会与之交互的东西。这是一个更新应用程序状态的 switch 语句。可以有一个以上的减速器。这是一个减速器的例子:

```
const initialState = {
  todos: [],
}export default (state = initialState, action) => {
  switch (action.type) {
    case 'CREATE_TODO':
      return {
        ...state,
        todos: [
          ...state.todos, 
          action.data
        ]
      }
    default: 
      return state
  }
}
```

状态是不可变的。这是一个很好的实践，对于 React 和 Redux 跟踪状态变化是必要的。

## 行动

动作用于对 Redux 状态进行更改。动作是发送给缩减器的对象。该对象包含一个`type`属性和一个`data`的有效负载。类型用在开关中，决定将要发生什么，数据是我们想要添加到新状态的数据。

```
const addTodo = data => ({
  type: 'CREATE_TODO', 
  data: { 
    ...data, 
    isCompleted: false
  }
});
```

这里重要的一点是，`addTodo`将无法与 Redux 通信，直到它被包装在 Redux dispatch-function 中。稍后会有更多的介绍。

## 动作类型

动作类型是上述动作中使用的类型。我通常把它们和常量字符串放在一个文件里，这样更容易重用。代码看起来会像这样:

```
export const CREATE_TODO = 'CREATE_TODO';
export const DELETE_TODO = 'DELETE_TODO';
export const EDIT_TODO   = 'EDIT_TODO';
```

## 选择器

选择器是一个用来获取某种状态的函数。这或多或少是一个接受对象(状态对象)并返回特定属性的函数。如果我们要编写一个选择器来从上面的对象中获取姓氏，它看起来会像这样:

```
const applicationState ={ 
  firstname: 'John', 
  surname: 'Doe' 
}const selectSurname = state => state.surnameselectSurname(applicationState)
```

## 商店

存储区是保存应用程序状态的地方。等店搭好了，就不用想这么多了。知道商店持有状态是件好事。一个基本的商店是这样的:

```
import { 
  createStore, 
  applyMiddleware, 
  compose, 
  combineReducers
} from 'redux';
import thunk from 'redux-thunk';
import todoReducer from 'from/some/file'const rootReducer combineReducers({
  todoReducer
});export default function configureStore(initialState = {}) {
  return createStore(
    rootReducer,
    initialState,
    compose(applyMiddleware(thunk))
  );
}
```

如果有多个减速器，则使用`combineReducers`功能。

# 反应还原

当 Redux 与 React 一起使用时，通常会使用一个名为`react-redux`的库。这使得 Redux 的使用变得更加容易。

## 连接

这是一种用于将组件连接到 Redux 的方法。要使用它，请在 connect 函数中包装组件的导出。connect 函数返回一个函数，所以一开始看起来有点奇怪，下面是一个例子:

```
export default connect()(myComponent)
```

连接函数可以接受两个参数，`mapStateToProps`和`mapDispatchToProps`。有了这些参数，函数将如下所示:

```
export default connect(
  mapStateToProps,
  mapDispatchToProps
)(myComponent)
```

那么这些论点是什么呢？

## mapStateToProps

`mapStateToProps`是将 Redux 状态映射到传递给组件的 props 的一种方式。如果属性是嵌套的，并且有点复杂，那么选择器是有用的。

```
const mapStateToProps = (state) => {
 const { todos } = state
 return { todos } 
}
```

## mapDispatchToProps

`mapDispatchToProps`是传递应该与 Redux 对话的函数的地方。这些功能可以从 props 中获得。

```
const mapDispatchToProps = dispatch => {
  return {
    addTodo: todo => dispatch({
      type: 'ADD_TODO',
      data: todo 
    })
  }
}
```

对象`{ type: ‘ADD_TODO’, data: todo }`是一个动作。`type`属性是动作类型。这可以被包装在一个叫做`addTodo`的函数中。这叫做动作创建器，一个返回动作的函数。

在这些例子中，类型`'ADD_TODO'`是一个字符串。这应该是常量，所以我们不会拼错。

```
import { ADD_TODO } from 'from/some/file'const addTodo = todo => return ({ 
  type: ADD_TODO, 
  data: todo 
})export actions { addTodo }
```

或者更好:

```
import { actions } from 'from/some/file'const addTodo = todo => return (actions.addTodo(todo))
```

Redux 连接的组件看起来像这样:

```
const TodoListContainer = ({todos, addTodo}) => {
 return (
    <TodoList 
      todos={todos} 
      addTodo={addTodo}
    />
  )
}const mapStateToProps = state => {
  const { todos } = state
  return {
    todos
  }
}const mapDispatchToProps = dispatch => ({
  addTodo: todo => dispatch({
    type: 'ADD_TODO', 
    data: todo, 
    isCompleted: false
  }),
})export default connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoListContainer);
```

在上面的例子中，使用了一个容器组件。如果你想了解更多关于这个模式的信息，我建议你[阅读这篇文章](/container-and-presentational-components-in-react-c56aca7713ba)。

## 概括起来

Redux 是一个优秀的状态管理工具。在幕后，Redux 还有助于提高性能，例如组件的重新渲染。除了我在这篇文章中提到的，还有更多可以重复的。如果你想了解更多，那么 [Redux 网站](https://redux.js.org/)是一个很好的起点。这篇文章旨在解释 Redux 中使用的概念和名称，我希望你从中有所收获。

我还建议你检查一下 Redux 挂钩`useDispatch`和`useSelector`，这会使代码更干净。

如果你有兴趣看看 Redux 是如何用 React 设置的，我推荐你阅读[这篇文章。](https://medium.com/@vikingjonsson/setup-react-with-redux-96dc91944644)

感谢阅读！