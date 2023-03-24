# 苗条——灵活的数据处理

> 原文：<https://levelup.gitconnected.com/svelte-flexible-data-handling-c0010cc14e5b>

## Svelte 及其处理异步数据的技术介绍

![](img/9738f4405eea706588997b47f95a83e4.png)

费伦茨·阿尔马西在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 内容

*   [简介](#8c1b)
*   [先决条件](#1c4e)
*   [什么是苗条](#2bb0)
*   [处理<脚本>](#e01a) 中的异步数据
*   [处理标记中的异步数据](#1e05)

Svelte 相对较新，但组件框架越来越受欢迎，因为它提供了超越 Angular、React 和 Vue 等其他框架的独特优势。本文介绍了 Svelte 及其处理异步数据的技术。

# 先决条件

请注意这篇文章:

*   需要 HTML 和 JavaScript 概念的基础知识。
*   不包括建立一个苗条的项目。同样参考[本](https://svelte.dev/blog/the-easiest-way-to-get-started)资源。

# 什么是苗条

Svelte 是一个组件框架——像 React 或 Vue——但有一个重要的区别。React 和 Vue 等传统框架在应用运行时在用户浏览器中完成大部分工作。Svelte 将这项工作转变为编译步骤，在构建应用程序时进行。因此，Svelte 产生了高度优化的普通 JavaScript。

Svelte 的主要卖点是:

*   **性能提升** —在构建时运行，不使用[虚拟 DOM diffing](https://reactjs.org/docs/reconciliation.html) 。
*   **没有样板代码** — Svelte 使你能够编写没有样板的组件。
*   **简化的状态管理** — Svelte 不需要外部状态管理库。

像任何框架一样，Svelte 也有它的怪癖。然而，Svelte 也与其他框架有许多相似之处。下面的列表并不详尽，但对于本文来说已经足够了:

*   一个苗条的文件可以有两个部分。 **<脚本>** — JavaScript 代码
    2。**标记** —要显示的 HTML(标记中的花括号可以包含任何
    JavaScript 代码)

```
<script>
  let name = ‘world’;
</script><h1>Hello {name}!</h1>**Output**:
Hello world!
```

*   您可以使用 props 将数据传递给子组件。例如，`Users`组件可以通过属性`users`将数据传递给其子组件`UserList`。

`<UserList users={users} />`

*   另外，Svelte 需要你在`UserList`中声明道具用户。

```
<script>
  export let users;
</script>
```

*   Svelte 有**生命周期方法**来管理代码执行，有**逻辑块**来表达标记中的逻辑。在接下来的章节中会有更多的介绍。

在以下部分中:

*   `Users`是获取和发送数据的父组件。
*   `UserList`是接收数据的子组件。

# 在

**<脚本>** 中处理数据的代码与其他框架类似。在将父组件中的异步数据传递给子组件之前，需要对其进行 T4。

因此，父组件`Users`看起来是这样的:

```
<script>
  import UserList from ‘./UserList.svelte’;
  import { onMount } from ‘svelte’; let users = []; onMount(async () => {
    const res = await fetch(`[https://api.github.com/users`](https://api.github.com/users`));
    users = await res.json();
  });
</script><UserList {users} /> // users = {users} can be shortened to {users}
```

上面的代码使用了`onMount`生命周期方法。用 Svelte 编码的时候，你会用的最多的是`onMount`。`onMount`中的代码在组件第一次呈现到 DOM 后运行。

**注意** — Svelte 建议将`fetch`放在`onMount`而不是 **<脚本>** 的顶层，以确保[延迟加载](https://developer.mozilla.org/en-US/docs/Web/Performance/Lazy_loading)。

除了`onMount`，Svelte 还有以下生命周期方法来管理代码执行:

*   **onDestroy** —当组件被破坏时运行
*   **更新前** —在 DOM 更新前立即运行
*   **afterUpdate** —在 DOM 与您的数据同步后运行

子组件`UserList`:

```
<script>
  export let users;
</script>{#each users as { login, repos_url }}
  <h4>{login}</h4>
  <p>{repos_url}</p>
{/each}
```

上面的代码使用`each`逻辑块遍历数据列表。

# 处理标记中的异步数据

当您构建稍微复杂到复杂的应用程序时，您可能会遇到这样的情况:

*   当组件挂载时，不需要处理异步数据。
*   需要用户操作来确定要提取的数据。
*   需要在等待异步操作完成时显示其他数据。

你可以在 **<脚本>** 部分处理上述(以及其他熟悉的场景)。然而，使用 Svelte，您可以用最少的代码和扩展来处理**标记**部分中的异步数据。

坚持本文中的场景，考虑一个函数`getUsers` ——包含获取数据的逻辑——分配给父组件中的 prop 变量。

父组件`Users`:

```
<script>
  import UserList from ‘./UserList.svelte’; const getUsers = async () => {
    const res = await fetch(`[https://api.github.com/users`](https://api.github.com/users`));
    return await res.json();
  }; const users = getUsers();
</script><UserList {users} />
```

在子组件`UserList`中，您只需要将`each`块包装在`await`块中来处理异步数据:

```
<script>
  export let users;
</script>{#await users}
  <p>loading…</p>
{:then users}
  {#each users as { login, repos_url }}
    <h4>{login}</h4>
    <p>{repos_url}</p>
  {/each}
{:catch error}
  <p>{error.message}</p>
{/await}
```

此外，`then`和`catch`块可以和`await`一起使用来复制 JavaScript 代码流。

除了`await`和`each`之外，Svelte 还有以下逻辑块来处理条件:

*   **如果**
*   **否则**
*   **否则-如果**

这就是了。您现在知道了什么是 Svelte，以及如何在 Svelte 中处理异步数据。

# 来源

*   *苗条的*。 [https://svelte.dev](https://svelte.dev)
*   *苗条 3:反思反应性*。【https://svelte.dev/blog/svelte-3-rethinking-reactivity】