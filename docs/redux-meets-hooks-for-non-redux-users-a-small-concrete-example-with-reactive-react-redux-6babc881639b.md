# 用 React 钩子和上下文替换 Redux:用 React-React-Redux 的一个小的具体例子

> 原文：<https://levelup.gitconnected.com/redux-meets-hooks-for-non-redux-users-a-small-concrete-example-with-reactive-react-redux-6babc881639b>

## 使用 reactive-react-redux 库从 Redux 构建一个 todo 列表示例

![](img/710b8544654bdae6795c9b872b850ce8.png)

# 介绍

如果你已经使用过 Redux 并且喜欢它，你可能不理解为什么人们试图使用 React 上下文和钩子来代替 Redux(也就是没有 Redux 炒作)。对于那些认为 Redux DevTools 扩展和中间件很好的人来说，Redux 和 context + hooks 实际上是两个选项。

上下文+钩子正好可以在组件之间共享状态。然而，如果应用程序变得更大，他们可能需要 Redux 或其他类似的解决方案；否则他们最终会有很多不容易处理的上下文。(我承认这是假设，我们将来能够找到更好的解决方案。)

我一直在开发一个名为“reactive-react-redux”的库，虽然它基于 redux，但它是不同的。

[](https://github.com/dai-shi/reactive-react-redux) [## 代时/反应-反应-还原

### 用 React 钩子和代理绑定 React Redux。通过创造一个新的环境，为 dai-shi/reactive-react-redux 的发展做出贡献

github.com](https://github.com/dai-shi/reactive-react-redux) 

它的 API 非常简单，而且由于代理，它的性能得到了优化。我希望这个库能够吸引那些用上下文+钩子寻找 Redux 替代品的人，所以我创建了示例代码。这是 Redux 中著名的 Todo 列表示例。

[](https://redux.js.org/basics/example) [## 示例:待办事项列表还原

### 示例:待办事项列表

冗余示例:托多 Listredux.js.org](https://redux.js.org/basics/example) 

本文的其余部分展示了用 reactive-react-redux 重写的示例代码。

# 类型和减速器

该示例是用 TypeScript 编写的。如果不熟悉 TypeScript，可以尝试忽略`State`、`Action`和`*Type`。

以下是状态和动作的类型定义。

## 。/src/type/index . ts

```
export type VisibilityFilterType =
  | 'SHOW_ALL'
  | 'SHOW_COMPLETED'
  | 'SHOW_ACTIVE';export type TodoType = {
  id: number;
  text: string;
  completed: boolean;
};export type State = {
  todos: TodoType[];
  visibilityFilter: VisibilityFilterType;
};export type Action =
  | { type: 'ADD_TODO'; id: number; text: string }
  | { type: 'SET_VISIBILITY_FILTER'; filter: VisibilityFilterType }
  | { type: 'TOGGLE_TODO'; id: number };
```

减压器与原始示例几乎相同，如下所示。

## 。/src/reducers/index.ts

```
import { combineReducers } from 'redux';import todos from './todos';
import visibilityFilter from './visibilityFilter';export default combineReducers({
  todos,
  visibilityFilter,
});
```

## 。/src/reducers/todos.ts

```
import { TodoType, Action } from '../types';const todos = (state: TodoType[] = [], action: Action): TodoType[] => {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        {
          id: action.id,
          text: action.text,
          completed: false,
        },
      ];
    case 'TOGGLE_TODO':
      return state.map((todo: TodoType) => (
        todo.id === action.id ? { ...todo, completed: !todo.completed } : todo
      ));
    default:
      return state;
  }
};export default todos;
```

## 。/src/reducers/visibility filter . ts

```
import { Action, VisibilityFilterType } from '../types';const visibilityFilter = (
  state: VisibilityFilterType = 'SHOW_ALL',
  action: Action,
): VisibilityFilterType => {
  switch (action.type) {
    case 'SET_VISIBILITY_FILTER':
      return action.filter;
    default:
      return state;
  }
};export default visibilityFilter;
```

# 动作创建者

分派动作可能有几种方式。我会为每个动作创建钩子。请注意，我们仍在探索更好的实践。

## 。/src/actions/索引. ts

```
import { useCallback } from 'react';
import { useReduxDispatch } from 'reactive-react-redux';import { Action, VisibilityFilterType } from '../types';let nextTodoId = 0;export const useAddTodo = () => {
  const dispatch = useReduxDispatch<Action>();
  return useCallback((text: string) => {
    dispatch({
      type: 'ADD_TODO',
      id: nextTodoId++,
      text,
    });
  }, [dispatch]);
};export const useSetVisibilityFilter = () => {
  const dispatch = useReduxDispatch<Action>();
  return useCallback((filter: VisibilityFilterType) => {
    dispatch({
      type: 'SET_VISIBILITY_FILTER',
      filter,
    });
  }, [dispatch]);
};export const useToggleTodo = () => {
  const dispatch = useReduxDispatch<Action>();
  return useCallback((id: number) => {
    dispatch({
      type: 'TOGGLE_TODO',
      id,
    });
  }, [dispatch]);
};
```

它们不是真正的动作创建者，而是返回动作调度器的钩子。

# 成分

我们不区分表示组件和容器组件。关于如何构造组件的讨论仍然是开放的，但是对于这个例子，组件只是扁平的。

## 。/src/components/App.tsx

App 也与原始示例相同。

```
import * as React from 'react';import Footer from './Footer';
import AddTodo from './AddTodo';
import VisibleTodoList from './VisibleTodoList';const App: React.FC = () => (
  <div>
    <AddTodo />
    <VisibleTodoList />
    <Footer />
  </div>
);export default App;
```

## 。/src/components/Todo.tsx

有小的修改但不是主要的。

```
import * as React from 'react';type Props = {
  onClick: (e: React.MouseEvent) => void;
  completed: boolean;
  text: string;
};const Todo: React.FC<Props> = ({ onClick, completed, text }) => (
  <li
    onClick={onClick}
    role="presentation"
    style={{
      textDecoration: completed ? 'line-through' : 'none',
      cursor: 'pointer',
    }}
  >
    {text}
  </li>
);export default Todo;
```

## 。/src/components/visibletodolist . tsx

我们没有`mapStateToProps`或选择器。`getVisibleTodos`被简单地称为在渲染。

```
import * as React from 'react';
import { useReduxState } from 'reactive-react-redux';import { TodoType, State, VisibilityFilterType } from '../types';
import { useToggleTodo } from '../actions';
import Todo from './Todo';const getVisibleTodos = (todos: TodoType[], filter: VisibilityFilterType) => {
  switch (filter) {
    case 'SHOW_ALL':
      return todos;
    case 'SHOW_COMPLETED':
      return todos.filter(t => t.completed);
    case 'SHOW_ACTIVE':
      return todos.filter(t => !t.completed);
    default:
      throw new Error(`Unknown filter: ${filter}`);
  }
};const VisibleTodoList: React.FC = () => {
  const state = useReduxState<State>();
  const visibleTodos = getVisibleTodos(state.todos, state.visibilityFilter);
  const toggleTodo = useToggleTodo();
  return (
    <ul>
      {visibleTodos.map(todo => (
        <Todo key={todo.id} {...todo} onClick={() => toggleTodo(todo.id)} />
      ))}
    </ul>
  );
};export default VisibleTodoList;
```

## 。/src/components/FilterLink.tsx

同样，当`useReduxState`返回整个状态时，它简单地使用其属性来评估`active`。

```
import * as React from 'react';
import { useReduxState } from 'reactive-react-redux';import { useSetVisibilityFilter } from '../actions';
import { State, VisibilityFilterType } from '../types';type Props = {
  filter: VisibilityFilterType;
};const FilterLink: React.FC<Props> = ({ filter, children }) => {
  const state = useReduxState<State>();
  const active = filter === state.visibilityFilter;
  const setVisibilityFilter = useSetVisibilityFilter();
  return (
    <button
      type="button"
      onClick={() => setVisibilityFilter(filter)}
      disabled={active}
      style={{
        marginLeft: '4px',
      }}
    >
      {children}
    </button>
  );
};export default FilterLink;
```

## 。/src/components/页脚. tsx

因为我们依赖于类型检查，所以将字符串传递给 filter prop 到 FilterLink 是可以的。

```
import * as React from 'react';import FilterLink from './FilterLink';const Footer: React.FC = () => (
  <div>
    <span>Show: </span>
    <FilterLink filter="SHOW_ALL">All</FilterLink>
    <FilterLink filter="SHOW_ACTIVE">Active</FilterLink>
    <FilterLink filter="SHOW_COMPLETED">Completed</FilterLink>
  </div>
);export default Footer;
```

## 。/src/components/AddTodo.tsx

这是对原始示例的一点修改，使用带有`useState`的受控形式。

```
import * as React from 'react';
import { useState } from 'react';import { useAddTodo } from '../actions';const AddTodo = () => {
  const [text, setText] = useState('');
  const addTodo = useAddTodo();
  return (
    <div>
      <form
        onSubmit={(e) => {
          e.preventDefault();
          if (!text.trim()) {
            return;
          }
          addTodo(text);
          setText('');
        }}
      >
        <input value={text} onChange={e => setText(e.target.value)} />
        <button type="submit">Add Todo</button>
      </form>
    </div>
  );
};export default AddTodo;
```

# 在线演示

请访问 [codesandbox](https://codesandbox.io/s/github/dai-shi/reactive-react-redux/tree/master/examples/11_todolist) 并在你的浏览器中运行这个例子。

源代码也可以在[这里](https://github.com/dai-shi/reactive-react-redux/tree/master/examples/11_todolist)找到。

# 了解更多信息

我在这篇文章中没有解释关于 reactive-react-redux 的内部细节。请访问[GitHub repo](https://github.com/dai-shi/reactive-react-redux)查看更多信息，包括[以前的博客文章列表](https://github.com/dai-shi/reactive-react-redux#blogs)。

*原载于 2019 年 6 月 3 日 https://blog.axlight.com*[](https://blog.axlight.com/posts/redux-meets-hooks-for-non-redux-users-a-small-concrete-example-with-reactive-react-redux/)**。**