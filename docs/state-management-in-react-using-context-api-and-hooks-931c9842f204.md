# React 中使用上下文 API 和钩子的状态管理

> 原文：<https://levelup.gitconnected.com/state-management-in-react-using-context-api-and-hooks-931c9842f204>

![](img/c67ba4ae41bc15913f6ee5c688394fea.png)

照片由[克莱门特·H](https://unsplash.com/@clemhlrdt?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

状态管理是反应中最重要的概念。React 中有几种管理状态的方法。管理状态最常见的方式是将道具传递给组件。

但是，如果您正在构建一个大型或中型的应用程序，这种管理状态的方式是不可伸缩的。因此，在这里我们将讨论如何使用带有钩子的上下文 API 来全局管理状态。

> Redux 是 React 中非常流行的状态管理库。然而，如果你是一个初学者，你可能会发现很难理解 Redux。所以你可以选择上下文 API 方法。

在这里，我们将使用上下文 API 和钩子构建一个 Todo 应用程序。实际上，我们不需要为这个小应用程序全局管理状态，但是我想给出一个上下文 API 的简要概述，这样你就可以在一个更大的项目中使用它。

> [你可以在这里看到直播项目](https://react-todo-dipankar.netlify.app/)。

我们将使用 **Bootstrap** 作为样式，使用**字体 Awesome** 作为图标。所以一定要把 index.html 的 CDN 链接导入到公共文件夹下的文件中。

最终项目的项目结构如下所示。

```
public
src
 |- components
    |- TodoForm.js
    |- TodoList.js
 |- contexts
    |- TodoContext.js
 |- App.css
 |- App.js
 |- index.jsREADME.md
package.json 
```

> 完整项目[的 Github 链接在这里。](https://github.com/dipankar-js/TodoApp)喜欢就掉一颗星。

让我们首先创建我们的上下文，

TodoContext.js

如您所见，我们使用 createContext 创建上下文，该上下文是从“react”导入的。

```
export const TodoContext = createContext();
```

我们需要将它包装在一个提供者下，以便其他组件可以使用它。我们基本上使用了两个函数， **addTodo** 和 **removeTodo** 来添加和删除我们将在 Todo 组件中使用的 Todo。

```
const addTodo = todo => {    
      setTodos([...todos, { todo, id: uuid() }]);  
};const removeTodo = id => {      
      setTodos(todos.filter(todo => todo.id !== id));  
};
```

您需要像这样在提供者内部将这些函数作为**值**传递。

```
<TodoContext.Provider 
      value={{ todos, addTodo, removeTodo }}
> 
     {props.children}    
</TodoContext.Provider>
```

所以我们的 TodoContext 已经完成了，但是您将如何使用它呢？它没有连接到任何其他组件。其他组件如何知道这个文件的存在？

您需要在 App.js 中导入这个文件并包装它，以便它可以将所有这些值传递给其他组件。

App.js

如您所见，TodoContextProvider 正在包装其他组件。不要担心 **TodoForm** 和 **TodoList** ，我们现在将创建这些组件。在 TodoContext.provider 内部传递的所有值现在都可以在 TodoForm 和 TodoList 组件内部使用。

让我们创建一个表单，在 TodoForm 组件中输入 todos。

TodoForm.js

`useContext`是一个内置的钩子，使功能组件可以方便地访问您的上下文。我们正在通过`useContext.`访问 addTodos 功能

```
const { todos, addTodo } = useContext(TodoContext);
```

提交按钮时，调用 addTodo 函数，Todo 作为参数传递。

```
const handleSubmit = e => {    
     e.preventDefault();    
     addTodo(todo);    
     setTodo("");  
};
```

对于样式，我们主要使用 bootstrap 类和一些用 App.css 编写的自定义 CSS。

TodoList.js

该组件负责在 UI 中列出待办事项。与 TodoForm 组件相同，我们正在使用`useContext.`访问`todos`和`removeTodo`

```
const { todos, removeTodo } = useContext(TodoContext);
```

在`removeTodo`中，todo id 被传递，该 id 将用于删除该特定的 todo。我们正在映射所有的`todos`，然后在 UI 中显示它们。

用于基本自定义样式的 App.css 文件。

这就是我们如何使用`useContext`管理全局状态。这是一个小应用程序，但是您可以在任何大的应用程序中使用`useContext`来处理状态。它消除了对外部库`Redux`的依赖，比`Redux.`更容易使用和理解

希望这篇文章对你有所帮助。如果是，请鼓掌并在下面写下您的宝贵反馈。谢谢大家！！

> 在 [Instagram](https://www.instagram.com/js.nerd/) 、 [Twitter](https://twitter.com/jsnerd_) 上关注我，获取日常编码技巧。