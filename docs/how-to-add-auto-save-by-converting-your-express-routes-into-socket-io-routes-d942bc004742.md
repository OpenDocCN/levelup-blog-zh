# 如何通过将您的快速路由转换为 Socket.io 路由来实现自动保存

> 原文：<https://levelup.gitconnected.com/how-to-add-auto-save-by-converting-your-express-routes-into-socket-io-routes-d942bc004742>

![](img/2159f61e976f875a2390764bffc6120b.png)

我将介绍如何以及为什么使用 Socket.io 在单页面 web 应用程序中实现自动保存功能。

如果你有任何问题或者遇到困难，欢迎在下面评论。

我欢迎任何建设性的反馈。

# 什么是 web socket 技术？

> “插座。IO 支持实时、双向和基于事件的通信。
> 它可以在任何平台、浏览器或设备上运行，同样注重可靠性和速度。”

简单地说，这基本上意味着**网络套接字允许网站向其用户传输数据**。这使得应用程序能够实现实时聊天或自动保存功能等功能。

# 为什么要使用 web socket 技术？

在我的例子中，我开发了一个任务管理应用程序，每当有人试图在短时间内进行大量编辑时，它就会崩溃。

# v.0:保存每次击键

起初，每当您进行编辑时，我的代码会尝试保存您的任务:

```
//html
<input type="text" class="todo" id="{{todo.id}}">//js
**$(".todo").on('keyup', function(e)** {
  const todo = document.activeElement;
  const todoId = todo.getAttribute("id")
  const todoValue = document.activeElement.getAttribute("id");
  **axios.post**('/save-todo', { todo, todoId, todoValue })
     **.then**(res => {
        // no need to update frontend in this case
     })
     **.catch**(err => return console.log(err));
}
```

在上面的例子中，我要求服务器在您每次按下键盘上的按钮时更新这个特定的任务(' todo ')。

Axios 会向服务器发送请求，一旦收到 6 个并发请求，Axios 就会开始限制我的请求。

该页面看起来似乎挂起了，但实际上它仍然在试图完成堆积的请求队列。

# v.1:为批处理请求添加计时器

根据我在网上的发现，这个问题最流行的解决方案是使用计时器批量更新(一种被称为[去抖](/debounce-in-javascript-improve-your-applications-performance-5b01855e086)的技术)，看起来像这样:

```
//html
<input type="text" class="todo" id="{{todo.id}}">//js**var timeout = null;
var timerDelay = 800;**$(".todo").on('keyup', function(e) {
  **clearTimeout(timeout); // reset every keystroke
  timeout = setTimeout(function () {**
    const todo = document.activeElement;
    const todoId = todo.getAttribute("id")
    const todoValue = document.activeElement.getAttribute("id");
    axios.post('/save-todo', { todo, todoId, todoValue })
       .then(res => {
          // no need to update frontend in this case
       })
       .catch(err => return console.log(err));
  **}, timerDelay);** }
```

这是怎么回事？

*   在文件的顶部，你已经声明了一个定时器`timeout`
*   save 函数仍然会在每次击键时被调用，但是现在它会重置一个计时器`clearTimeout(timeout)`，然后启动一个计时器
*   如果您没有通过继续编辑长达 800 毫秒来重置计时器(因为`timerDelay = 800`)，那么 **axios.post()** 请求将最终被调用。

![](img/3d2e1b00518f6d2ae11d920ac1ff8e27.png)

仍然在等待超快的打字者

这实际上为 99%的用户解决了问题，但仍有少数用户(比如我自己)打字很快，喜欢批量检查任务，所以服务器仍处于挂起状态…

增加`timerDelay`本来是可行的，但是如果用户关闭浏览器，保存请求将永远不会触发，因此更改将会丢失。所以我一直在寻找更好的解决方案。我的研究将我引向了 web socket 技术。

**v.2:使用 Socket.io 技术**

```
//html
<input type="text" class="todo" id="{{todo.id}}">//js$(".todo").on('keyup', function(e) {
    const todo = document.activeElement;
    const todoId = todo.getAttribute("id")
    const todoValue = document.activeElement.getAttribute("id");
    **socket.emit('save-todo', { todo, todoId, todoValue });** }
```

那么上面发生了什么呢？

我使用了一个 **socket.emit** ，而不是一个 **axios.post** 请求。否则，函数看起来完全一样。

![](img/775daf9dc5c92b5dbfa86ee53f54dc9b.png)

Socket.io 技术

而且，成功了！

在实现 socket.io 技术后，页面不再崩溃，我也不再用异步 POST 请求让服务器过载。相反，我将数据传输到服务器。作为一个网站用户，即使我关闭了我的窗口，服务器最终还是会完成对象的保存。

# 从这里去哪里？

如果您想减少服务器上的工作，您可以添加一个计时器(如上面的 v1 所示)来批处理套接字请求。

如果您想自己对套接字进行更改，您需要:

*   安装 Socket.io，你可以用节点包管理器(如果你正在使用的话)通过`npm install socket.io`来完成
*   `require` Socket.io 并在你的`app.js`或`server.j` s 文件中实例化它
*   将您的快速路线转换为 Socket.io 路线

为了帮助您将 Express routes 转换为 Socket.io，我创建了一个 Github GIST，其中的文件带有一个完整的前缀，在“从 Express 切换到 Socket.io”之前添加了“T4”，在“从 Express 切换到 socket . io”之后添加了“T5”或“T6”，因为当我自己进行切换时，我很难找到完整的示例。

# 示例:用 Express 和 Socket.io 编写的相同路由

*最后更新时间:2019 年 3 月 2 日*

下面的例子是用 JavaScript 编写的，使用 Node.js、Express、Socket.io 和 Handlebars 作为视图引擎。在这里找到最新的代码:[https://github.com/nsafai/looplist](https://github.com/nsafai/looplist)

希望有帮助！感谢阅读。

如果您需要帮助或有任何可以改进本文的反馈，请随时联系我们。

[![](img/9914c5dd23ac08b70eea6f4f9ba6fed2.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/node-js) [## 学习 Node.js -最佳 Node.js 教程(2019) | gitconnected

### 前 32 个 Node.js 教程-免费学习 Node.js。课程由开发人员提交和投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/node-js)