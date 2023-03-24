# 为您的下一个全堆栈项目提供自以为是的技术堆栈

> 原文：<https://levelup.gitconnected.com/an-opinionated-tech-stack-for-your-next-full-stack-project-f6bb8ce317e9>

![](img/7dc150de84cedb35d8c91267a427a5eb.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [La-Rel Easter](https://unsplash.com/@lastnameeaster?utm_source=medium&utm_medium=referral) 拍摄的照片

# 概观

几年前，我很想找到这篇文章。这将为我节省无数的时间和精力去寻找合适的技术堆栈。如今，构建和托管您的应用程序有很多选择。这是我的观点，主要基于以下限制:

*   我更喜欢用**的一种** **编程语言**。在我的例子中，JavaScript 就是答案(TypeScript 也可以在这个堆栈中工作)。
*   我想**为多个平台**(例如 Web、移动、桌面)构建客户端，但是没有时间，也没有意愿将每个平台作为一个单独的项目来构建。
*   我不想花无数的时间去构建 **UI 组件**。
*   在项目的开发和早期阶段，我希望获得低成本或零成本的 PaaS 的好处。
*   当我提交代码时，我希望能够自动部署整个堆栈。

# 平台即服务🔥

我使用的是 [*Firebase*](https://firebase.google.com/) (构建于 [*谷歌云平台*](https://cloud.google.com) )，它提供了网络托管、认证、数据库(NoSQL &实时)、文件存储、无服务器功能、日志、分析、推送通知等服务！

> Firebase 是一个非常可行的创业平台…

我有一个约 500 名用户的应用程序，每月不用付费。我有其他用户较少的应用程序，总的来说，我目前每月支付大约 0.30 美元。显然，成本是相对的，并且会随着应用程序的受欢迎程度而快速增加，与平台无关。我的观点是 *Firebase* 从成本(和功能)的角度来看，对于初创公司来说是一个非常可行的平台。

# 客户👩‍💼

对于客户端，我喜欢 [*Quasar 框架*](https://quasar.dev) (构建于 [*Vue.js*](https://vuejs.org/) )，主要是因为我能够从一个代码库(例如 SPA、PWA、SSR、原生 *iOS* 、原生 *Android* 、原生 *Windows* 、原生 *macOS* 、原生 Linux、浏览器扩展……)构建所有的客户端。Quasar 还提供了一个奇妙的内置 UI 组件库，并有许多其他有用的插件、扩展和帮助功能。

web 客户端构建被部署到托管服务的 *Firebase。我在 [*GitHub*](https://github.com) 上托管我的代码，并使用[*GitHub Actions*](https://github.com/features/actions)自动部署到 *Firebase* 。*

使用每个商店的本地进程将本地客户端版本部署到适当的应用商店(例如，我使用 *XCode* 部署 *iOS* 应用)。 *Quasar* 通过内置对 *Cordova* 、*电容器*和*电子*的支持，使得构建过程相对容易。

> 对于任何有兴趣了解更多关于*类星体框架*的人来说，看看这个由 community favorite 制作的视频[和 Danny 制作应用](https://www.youtube.com/channel/UC6eR_ndNgaTeE5t2Ud4ZiHw):

# 营销(www)🌐

如果适用的话，我在一个单独的 *Quasar* 项目中构建营销网站，并将其构建为服务器端渲染(SSR)以获得 SEO 好处；然而，大多数情况下，我只是将这些页面作为应用程序的一部分，让用户通过一个行动按钮“启动”应用程序。

我创建公共路由、布局、页面，在用户访问“https://www.myappdomain.app”时创建匿名内容。

然后，我创建身份验证路由、布局、页面和组件来处理用户注册、登录、注销和其他相关特性。

最后，一旦用户通过身份验证，我就为应用程序创建安全的路线、布局、页面和组件。 *Quasar* 使得基于用户平台使用不同布局(和/或组件)变得非常简单。因此，我可以拥有移动布局和桌面布局，同时仍然利用相同的页面和组件。

# 证明文件📖

我可以构建嵌入我的客户端的文档(使用 *Quasar 的* [QMarkdown 应用扩展](https://quasar.dev/app-extensions/discover))；然而，因为我可以单独部署更新，我通常会使用另一个 *Quasar* 项目或类似 [VuePress](https://vuepress.vuejs.org/) 的项目创建一个单独的 docs 项目。

当使用一个单独的 docs 项目时，我通常会使用一个定制的子域(比如“https://docs.myappdomain.app”)将它部署到 [GitHub Pages](https://pages.github.com/) 。

# 计算机网络服务器🌀

我使用 [*Node/Express*](https://expressjs.com/) 和[*Firebase Admin SDK*](https://firebase.google.com/docs/admin/setup)构建了一个服务器项目。将服务器部署到 *Firebase 函数*给了我构建 REST APIs、发布/订阅触发器、数据库更新触发器和调度触发器的选项。

> 注意:对于那些熟悉 Firebase SDK 的人来说，关于是直接从客户端调用 Firebase 好，还是让服务器调用 Firebase 并向客户端提供 RESTful API 好，还存在一些争议。两者都有合理的理由，而且更有可能的是，你需要一个组合。我在项目中采用了这两种方法，我更喜欢尽可能使用服务器，只有在绝对必要的时候才从客户端直接调用 Firebase。
> 
> 何时有必要使用 Firebase client SDK 的示例有:
> 1)使用 Firebase 身份验证方法登录，如谷歌或脸书
> 2)将文件上传到 Firebase 存储器
> 3)使用 Firebase 分析来捕获客户端流量

# 项目结构

这只是让你对我的一个项目文件夹有个概念。目的是展示我正在使用带有脚本的单个 repo 来运行本地开发环境、执行构建并部署到适当的 PaaS 服务。

```
MyProject
  L client           // Quasar project
    L src            // Quasar code (for all platforms)
    L src-*          // auto-created when platforms added
    L ...            // other platforms added
    L dist
      L *            // Web client builds
    L quasar.conf.js // Quasar configuration
    L package.json   // scripts to run, build, deploy client(s) L docs           // Quasar or VuePress project
    L src          // Document markdown files
    L package.json // scripts to run, build, deploy docs L server
    L functions    // Node/Express project
      L index.js   // code starts here
    L package.json // scripts to run, build, deploy server L .git         // local "monolythic" repo
  L .github      // GitHub actions
  L package.json // scripts to run, build, deploy all
```

# 结论

当你开始一个新项目时，选择一个技术组合是你做的最重要的决定。在这个阶段做出错误的选择会导致时间、金钱和头发的巨大损失。😢

所有经验丰富的全栈开发人员都有自己的看法。每个人都从不同的角度和独特的限制来看待这个问题。

对我来说，这种“自以为是”的技术堆栈是一种不断尝试和错误的进化，到目前为止，还没有让我失望。当你试图找到你自己的“自以为是的”技术堆栈时，它至少是值得研究的！祝你好运！👍

希望这有所帮助！期待大家的评论！