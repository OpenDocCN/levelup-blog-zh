# 将 IndexedDB API 与 React(和钩子)一起使用

> 原文：<https://levelup.gitconnected.com/using-the-indexeddb-api-with-react-and-hooks-4e63d83a5d1b>

![](img/8e36b810eff1e875703a814fda6232b3.png)

凯文·Ku 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

*2019 年 2 月 7 日编辑:React Hooks 现在是 React 版本 16.8.0 的一个稳定特性。本教程现在使用 16.8.1。*

*2019 年 2 月 21 日编辑:我上传了一个演示到 GitHub 页面:*[https://brookslybrand.github.io/react-indexeddb-example/](https://brookslybrand.github.io/react-indexeddb-example/)

# 灵感

我的公司正在开发一款令人兴奋的新产品，将目前完全靠笔和纸完成的流程数字化。和大多数新项目一样，没多久就发现了主要的障碍。

在我们最初的一次发现会话中，我们意识到我们的应用程序需要能够完全脱机工作。更重要的是，由于我们的目标受众将在我们的应用程序上填写大量的表单，他们可能会在使用它时多次失去连接，因此表单数据必须是持久的。最后，我们正在构建一个渐进式 web 应用程序(PWA ),所以所有这些都必须在浏览器中使用 javascript 来完成。

当我开始研究我们的应用程序是否可以脱机运行时，我很快发现了 [IndexedDB API](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) 。我很兴奋地了解了这个工具，以及如何利用浏览器中的磁盘来保存用户对表单所做的更改，即使他们脱机时也是如此。

在研究过程中，我确实找到了至少一篇[不错的文章解释了 IndexedDB API 的一些基础知识，但是我找不到一个使用 React 的例子，而这正是我真正想要的。经过一段时间的搜索，我放弃了，并决定建立自己的简单的例子。这是相当草率的，但我仍然能够创建我想要的概念证明。](https://itnext.io/indexeddb-your-second-step-towards-progressive-web-apps-pwa-dcbcd6cc2076)

你可以在这里查看我最初的尝试。

后来，当我阅读 MDN 关于 IndexedDB API 的文档时，我意识到我忽略了一个重要的注意事项:

> **注意** : IndexedDB API 很强大，但是对于简单的情况来说似乎太复杂了。如果您更喜欢简单的 API，可以尝试使用一些库，如[local feed](https://localforage.github.io/localForage/)、 [dexie.js](http://www.dexie.org/) 、 [ZangoDB](https://github.com/erikolson186/zangodb) 、 [PouchDB](https://pouchdb.com/) 和 [JsStore](http://jsstore.net/) ，它们使得 IndexedDB 对程序员来说更加友好。

在快速浏览了所有选项、比较了 API 方法、包大小、npm 上的每周下载量以及库的维护之后，我决定尝试使用 [Dexie.js](http://dexie.org) 重写我的概念证明。根据 dexie 的描述:

> Dexie.js 是 IndexedDB 的包装器库，indexed db 是浏览器中的标准数据库。

我故意放慢向项目添加 npm 包的速度。然而，鉴于我是一个前端开发团队，我认为依靠其他人的知识和 IndexedDB API 的打包可能是实现我的目标的更好选择。

下面是我如何使用 Dexie.js、React 和 React 即将推出的最激动人心的特性之一 [Hooks](https://reactjs.org/docs/hooks-intro.html) 来解决在简单 PWA 上持久化数据的问题。

# 教程

首先要注意的是，在本教程中，您将希望在所有开发和测试中使用匿名窗口。如果您使用常规窗口，除非您手动删除它，否则数据库不会被重置。使用隐名窗口，如果你搞砸了，或者想重新开始，你只需要关闭并重新打开窗口，一切都会恢复正常。你可以通过右击你的浏览器图标并点击“新建匿名[私人]窗口”来打开一个匿名窗口，或者在 Windows 上点击`Ctrl + Shift + n` ，或者在 Mac 上点击`⌘ + Shift + n`来打开 Chrome。

接下来，我们将使用[创建-反应-应用](https://facebook.github.io/create-react-app/)来使开始更容易。

在您的终端中，键入:

`npx create-react-app indexeddb-example && cd indexeddb-example`

现在我们已经完成了 React 应用程序的设置，让我们删除所有不需要的内容:

`rm src/App.css src/App.test.js src/logo.svg`

太好了！现在我们准备开始写了。首先，我们需要注册服务人员，以便我们的应用程序可以离线运行。我不打算详细介绍服务人员，但幸运的是，create-react-app 使设置一个简单的服务人员变得很容易。如果您想了解更多关于使用 React 服务工作者的信息，[这里是我开始](https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app)的地方。

现在，我们需要知道的是，这将允许我们离线运行应用程序。需要注意的是，离线功能在我们运行`npm run build`之前是不可用的，但是我们会实现的。在文件底部的`src/index.js`中，将`serviceWorker.unregister()`改为`serviceWorker.register()`。你的`index.js`现在应该是这样的:

好吧，让我们进入`src/App.js`。您可以删除该文件中的所有内容。我们的应用程序组件将非常简单。本质上，它只是一个包含`Form`组件的 div，带有一点逻辑，用于安装/卸载表单以供演示。

这里有一些事情要做，但是首先我们需要继续安装 Dexie.js

`npm install dexie`

`App`包含一个简单的 div，一个打开/关闭(挂载/卸载)表单的小按钮，以及实际挂载表单的逻辑。注意，每当我们装载表单时，我们也在创建数据库连接。如果我们的应用程序组件要经常重新呈现，这不是一个好主意，我们可能希望将它移动到一个状态钩子、redux 或其他什么地方，但对于我们的目的来说，这将是很好的。

现在进入本教程的核心部分。让我们在`src`中制作一个名为`Form.js`的文件。为了让它工作，我们需要再安装一个 npm 包(最后一个，我保证)。这将允许我们动态隐藏我们的提交，直到客户端重新上线。

`npm install react-detect-offline`

这里是`Form.js`

注释应该能够解释大部分代码，但是我们真正做的只是用 React 钩子制作一个受控表单。还没什么特别的。如果你运行`npm start`，你会看到这个表单“有效”。但是，如果在键入后刷新页面，数据就会消失。我们还没有完全持久化数据。让我们利用我们传承下来的`db`道具。首先，我们将使用另一个钩子，`useEffect`(不要忘记在顶部从 React 导入它)。在您的`useState`下方和事件处理程序上方添加以下代码:

这个效果只有在`db`道具改变的时候才会被调用，在我们的例子中，只有当它被安装的时候才会被调用。我们将它设置在第 33 行，这是第二个，也是`useEffect`的可选参数。从第 4 行开始的第一个参数是一个函数，它

1.  连接或创建商店`formData`
2.  执行读/写事务
3.  获取`firstname`和`lastname`的值，如果它们不存在，则将它们设置为空字符串
4.  将存储中的值设置为州的值
5.  返回一个函数，该函数在该效果重新运行之前或组件卸载时被调用。该函数本身关闭数据库连接，因为一旦表单被卸载，我们就不再需要它了

太好了，我们已经建立了数据库！但是我们还是遗漏了一些东西。我们需要我们的事件处理程序在发生变化时写入数据库，这样即使我们离线和/或刷新页面，数据也会被保存…也就是持久性。

幸运的是，这并不是很难完成。只需更新`setName`，用 id 和新值调用`formData`存储上的`put`方法:

就是这样！现在，您应该能够在表单中键入一些内容，刷新页面，并看到您的数据仍然在那里！要想知道即使你的应用程序离线也能工作，继续运行`npm run build`，然后运行`serve -s build/`。您可能需要首先全局安装`serve`，这可以通过运行`npm -g install serve`来完成。一定要打开一个匿名/私人窗口，进入 localhost:5000，输入你的名字，离线(你可以从 Chrome 上的检查器的网络标签中完成)，刷新页面。

如果你的应用程序有些地方不太正常，完整的项目可以在这个 GitHub 库中找到[。](https://github.com/brookslybrand/react-indexeddb-example/tree/master/dexie-example)

我希望这个小教程对任何想利用 React 应用程序内部的 IndexedDB API 的人有所帮助。如果你喜欢这个教程，请给它一个或两个掌声。如果我没有解释清楚，或者我的代码有问题，请留下评论。

[![](img/9914c5dd23ac08b70eea6f4f9ba6fed2.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/react) [## 学习 React -最佳 React 教程(2019) | gitconnected

### 排名前 49 的 React 教程-免费学习 React。课程由开发人员提交并投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/react)