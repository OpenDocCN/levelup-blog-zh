# 使用 Vue 构建更快的应用

> 原文：<https://levelup.gitconnected.com/building-faster-apps-with-vue-3d9a4302061d>

## 了解组件通信、路由、Axios、优化和部署

![](img/21c61580c0cad7602bbedbb2c2570f28.png)

avaScript 是现代 web 应用程序的灵魂。它是 web 应用前端开发的核心组件。市场上有各种各样的 JavaScript 框架。其中一个流行的框架是 **Vue** 。

Vue 是一个用于构建优秀用户界面的渐进式框架。核心库主要集中在视图层，根据需要很容易将不同的其他库集成到其中。此外，Vue 能够实现复杂的单页面应用程序(SPA)以及现代工具和广泛可用的支持库

在这篇文章中，我们将创建一个简单的应用程序来创建和管理任务。此外，我们还将了解 Vue 的一些强大功能，例如:
1。路由
2。组件基础知识
3。组件间通信
4。Axios 库和后端连接配置
5。优化
6。对接和部署

但是在开始上述所有主题之前，让我们首先按照下面给出的步骤创建和设置用于管理任务的基础项目

1.  使用命令`vue create notes-app`用 Vue CLI 3 创建一个项目
2.  将依赖库添加到 **package.json** 中，如下所示:

package.json

3.使用命令`npm install`安装添加的依赖项

一旦一切都设置好了，让我们进入下一步。

## 按指定路线发送

路由是 Vue 提供的重要功能之一。它可以通过安装`vue-router`集成到一个 Vue 应用中。Vue-router 是 Vue 的官方路由器。它为我们提供了一系列功能，例如:
*嵌套路由/视图映射
*模块化、基于组件的路由器配置
*路由参数、查询、通配符
*由 Vue.js 过渡系统支持的视图过渡效果
*细粒度导航控制
*具有自动活动 CSS 类的链接
* HTML5 历史模式或哈希模式，在 IE9 中具有自动回退功能
*可定制的滚动行为

要在我们的应用程序中实现路由，请在 router 文件夹中创建一个名为 **index.js** 的文件，并向其中添加以下代码:

Route: index.js

观察 routes 对象，它包含我们的应用程序支持的所有路由，并以嵌套路由的方式实现。

`children`对象包含所有底层路线，这些路线将被加载到仪表板布局的顶部。

DashboardLayout.vue

上面显示的`router-view`标签是最重要的标签，因为它就像一个容器，所有与路线匹配的组件都将在其中呈现。

## 组件基础

组件是 Vue 的核心特性，它为我们提供了模块化的方法，使用这种方法我们可以将 DOM 分成几个小组件，这些组件可以在整个应用程序中重用。

在设计一个组件的时候，我们应该考虑一些重要的事情，以使它具有可伸缩性和可重用性。识别可以作为组件分离的相关功能的单个单元。
2。不要用不相关的功能重载组件。
3。仅将内部使用的代码添加到组件中，例如，默认数据绑定，如年份、性别等。
4。不要将代码添加到实现发生变化的组件中，例如 API 调用等。

组件的简单例子是`NavBar`，它只包含与导航项目相关的 DOM。

导航条. vue

DashboardLayout.vue 中使用了上述组件，如下所示:

DashboardLayout.vue

## 组件间通信

在任何 web 应用程序中，数据应该以正确的方式流动，以便可以有效地操作和管理，这一点非常重要。

在组件化方法中，当我们将 DOM /代码分成几个小块时，**我们如何传递和处理被不同组件使用的数据**？这就是组件间通信的由来。

使用
1，可以通过三种方式实现 Vue 中的组件间通信。**道具**:当**父**子
2 有通信流时使用。 **$emit():** 当**子节点到**父节点
3 之间存在通信流时使用。 **EventBus:** 主要用在组件之间有深层嵌套的时候，或者我们想在任何组件之间全局实现发布/订阅事件模型的时候。

为了理解组件间通信的概念，让我们添加两个组件

*   `**Add**`:该组件将用于添加新任务以及编辑现有任务。
*   `**NoteViewer**`:这个组件将用来显示一个单独的注释。

Add.vue

NoteViewer.vue

现在，我们已经创建了组件，仔细观察两个组件的`**script**`部分。

在`**props**`下，我们已经声明了一些对象及其类型。这些是我们将传递给这个组件的对象，当我们实际使用它进入某个页面时。

另外，观察`**$emit()**`函数，我们已经生成了一个事件来将数据传递回父组件。

让我们看看如何使用上面创建的组件。将数据传递给这些组件，并侦听来自这些组件的事件，以将数据返回到 Home.vue 中，如下所示:

Home.vue

现在，如果我们仔细看看，名为`**note-editor**`的 Add.vue 被重用了两次，一次是用于**添加**注释，另一次是用于**更新**注释。

此外，我们通过使用`v-for`属性遍历从数据库中获取的一系列注释来扩展`**note-viewer**`组件。

可以观察到的另一点是`**note-editor**`标签中的`[**@cancel**](http://twitter.com/cancel)`事件，在重用同一组件时，对于添加操作和更新操作，该事件的处理是不同的。

```
<!-- Add Task -->
<note-editor v-show="enableAddNote"
:key="enableAddNote"
@add="addNote"
**@cancel="disableAdd"** /><!-- Update Task -->
<note-editor v-show="!note.viewMode"
:add-mode="false"
:note="note"
@update="updateNote"
**@cancel="cancelUpdate(note)"** />
```

这就是我们如何避免可伸缩性问题的方法，当有机会改变实现时，通过将事件暴露在组件之外。

我们已经将数据动态地注入到两个组件中，例如`**note-viewer**`标签中的`**:note**`属性。

就是这样。现在我们的组件可以来回传递数据。

## Axios 库和配置

Axios 是一个基于 Promise 的库，用于调用 API 并向其发送/接收数据。

它是如此强大和安全，以至于它可以提供客户端支持来防止 XSRF 攻击、请求/响应拦截器、转换请求/响应数据、取消请求等。

让我们将 Axios 创建并配置为一个库，这样我们就不需要在每次使用时都导入它。

Axios: index.js

在 **main.js** 中，添加响应拦截器，以便在应用程序中使用之前从 API 响应中过滤数据和错误，如下所示:

main.js 中的 Axios 拦截器实现

接下来，初始化 **main.js** 中的全局变量`$http`，如下所示，这样就可以通过 vue 实例在整个应用程序中访问它。

```
import HTTP from "./axios";
**Vue.prototype.$http = HTTP;**
```

就是这样。现在，我们已经准备好进行 API 调用，如下所示:

```
const data = await this.$http.get("notes/getall");
```

## 最佳化

让我们假设我们的应用程序已经有了数百个组件和视图。

这将影响应用程序的加载时间，因为整个应用程序将以 JavaScript 的形式一次性下载到浏览器中。

在这样的时候，
1。我们如何防止加载当前没有使用的组件和视图？
2。我们如何减少可下载单元的大小？
3。我们如何改善装载时间？

作为一个解决方案，我们可以一次性加载基础应用程序，并通过修改路由器按需加载组件和视图，如下所示

```
{
path: "/notes",
name: "Notes",
component: () =>
import(**/* webpackChunkName: "home" */** "../views/Home.vue")
}
// *Observe the /* webpackChunkName: "home" */*
```

这将生成一个单独的块([view])。[哈希]。js ),当访问该路由时，该路由是延迟加载的。

## 对接和部署

由于我们的代码现在运行没有任何错误，是时候将它部署为一个容器了。因此，让我们将下面的 docker 文件添加到项目中

对于生产场景，我们将在像 **Nginx** 这样强大的 http 服务器后面公开我们的应用程序，以防止它被黑客攻击，并保护它免受恶意攻击。

还记得我们在配置 axios 时声明的服务主机的环境变量吗？

```
**process.env.VUE_APP_API_HOST**
```

由于它是一个基于浏览器的应用程序，我们需要在构建时设置和传递这个环境变量。在 docker 映像构建时使用`**--build-arg**`很容易做到这一点，如下所示:

```
**sudo docker build --build-arg VUE_APP_API_HOST=<Scheme>://<ServiceHost>:<ServicePort>/ -f Dockerfile -t vue-app-image .**
```

**注意**:请务必更换 ***<方案>*** ，***<service host>***和***<service port>***。

最后，使用以下命令运行应用程序:

```
**sudo docker run -d -p 8080:80 — name vue-app vue-app-image**
```

恭喜您，您的 vue 应用程序已经启动并运行。

如果您想了解完整的应用程序代码，请访问下面的链接:

1.  Vue [前端](https://github.com/vivekcgi/sample-notes-app)
2.  服务[后端](https://github.com/vivekcgi/sample-notes-app-service)

如果你有任何疑问，我非常乐意帮忙。你可以到 vivek.khandekar91@gmail.com 找我。