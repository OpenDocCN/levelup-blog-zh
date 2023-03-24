# 通过构建 TodoApp 应用程序实现苗条身材的基本介绍

> 原文：<https://levelup.gitconnected.com/basic-introduction-to-sveltejs-todoapp-9d174ebb99f9>

![](img/76a5cf284b0730b24d316747e9e378d9.png)

> SvelteJS 不仅仅是另一个 JS 库或框架——它是一个现代的 Javascript 编译器。

## **什么是苗条身材**

Svelte.js(或者只是“Svelte”)是一个现代的 JavaScript 编译器，允许您编写易于理解的 JavaScript 代码，然后编译成在浏览器中运行的高效代码。

它类似于 [React](https://reactjs.org/docs/getting-started.html) 或 [Vue](https://vuejs.org/v2/guide/) ，但是在 Svelte 中，我们没有任何依赖关系。这意味着解释我们的代码不需要花费任何时间，所以我们将在运行前得到纯 JavaScript。我们只需编写简单的普通 JavaScript 代码，加上一些简单的规则，然后 Svelte 会将这些代码编译成高度优化的 Javascript 代码包，直接在浏览器中运行。

与其他 JS 库相比，使用 Svelte 有许多特性和优势，我将在下一篇文章中讨论这些。但现在，我们将编写一个简单的 todo 应用程序，它将帮助我们了解苗条的基本知识。

首先为这个项目创建启动文件。

```
npx degit sveltejs/template svelte-app
```

然后运行`**npm install && npm run dev**`。

检查`src`文件夹，你会看到文件`App.svelte`和`main.js`。

纤细的组件组成在`.svelte`文件中。

```
<script>
 // component logic
</script><style>
 /* component specific styles */
</style><! — component markup -->
```

现在让我们写一些基本的 HTML 代码。

```
<div class=”container”>
  <main>
    <div class=”container”>
      <p class=”empty-state__description”>Add Todo Lists</p>
      <form>
        <input
          type=”text”
          aria-label=”Enter a new todo item”
          placeholder=”eg. gyms”
        />
      </form>
    </div>
  </main>
</div>
```

也添加一些风格。

```
.container { height: 100%; width: 500px; margin: 0 auto;}h1 { color: blue;}ul { list-style-type: none;}.checked { text-decoration: line-through;}a { text-decoration: none;}
```

现在我们将创建一个基本的 JavaScript 类，负责初始化变量和函数。

```
class TodoApp { constructor() { this.todoItems = []; this.newTodo = “”; } addTodo() { todoApp.newTodo = todoApp.newTodo.replace(/ /g, “”); if (todoApp.newTodo.length == 0) return; const todo = { id: todoApp.todoItems.length + 1, text: todoApp.newTodo, checked: false }; todoApp.todoItems = […todoApp.todoItems, todo]; todoApp.newTodo = “”; } toggleDone(id) { const index = todoApp.todoItems.findIndex(item => item.id === Number(id)); todoApp.todoItems[index].checked =   !todoApp.todoItems[index].checked; } remove(id) { todoApp.todoItems = todoApp.todoItems.filter( item => item.id !== Number(id) ); }}const todoApp = new TodoApp();
```

让我解释一下上面的代码。

我们创建了`TodoApp`类，在这里我们初始化了两个变量`todoItems`和`newTodo`。

有三个功能:

`addTodo` : 在这里我们创建一个`newTodo`并将其推送到`todoItems`。

`toggleDone`:切换任务是否完成。

`remove`:移除选中的待办事项。

现在我们更新 HTML 代码中的 JS 动作:

```
<div class=”container”> <p class=”empty-state__description”>Add Todo Lists</p> <form on:submit|preventDefault={todoApp.addTodo}> <input type=”text” aria-label=”Enter a new todo item” placeholder=”eg. gyms” bind:value={todoApp.newTodo} /> </form> <ul class=”todo-list”> {#each todoApp.todoItems as todo (todo.id)} <li class=”todo-item”> <a href=”javascript:void(0)” on:click={() => todoApp.toggleDone(todo.id)}> <span class={todo.checked ? ‘checked’ : ‘’}>
              {todo.text}
            </span> </a> <button class=”delete-todo” on:click={() => todoApp.remove(todo.id)}> X </button> </li> {/each} </ul></div>
```

仅此而已！这是一个简单的 Svelte 实现，提供一个总体概述。查看本教程，了解更多信息。[https://svelte.dev/tutorial/basics](https://svelte.dev/tutorial/basics)。

源代码:[https://github.com/kaissaroj/svelte-todo-app](https://github.com/kaissaroj/svelte-todo-app)

[](https://gitconnected.com/learn) [## 了解如何编码-查找编码教程| gitconnected

### 从开发者提交和排名的教程中学习任何编程语言、框架或库。教程是…

gitconnected.com](https://gitconnected.com/learn)