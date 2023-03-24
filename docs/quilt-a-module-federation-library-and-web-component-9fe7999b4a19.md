# quilt——一个模块联合库和 web 组件

> 原文：<https://levelup.gitconnected.com/quilt-a-module-federation-library-and-web-component-9fe7999b4a19>

# 介绍

最近，我对网络中的平台和框架不可知论有些着迷。也许是我经历了 JQuery 的衰落，AngularJS 的崛起和随后的衰落(1。x)，以及目前 React 的黄金时代。在许多方面，感觉前端经常处于 Gartner 炒作周期的顶峰，新框架以震耳欲聋的速度出现。

这促使我去探索我们可以追溯到 JavaScript 根源的方法，我偶然发现了 web 组件。我以前写过关于 web 组件[ [1](https://dev.to/cawfeecoder/web-components-an-introspective-363c) ]的文章，以及我所看到的拥有一组基本原语的好处，这些原语可以存在于现在和将来的任何框架中。把它当成一种对冲，防止 React 在 5 到 10 年后成为最热门的东西。

随后，我也一直在探索模块联合。几年前，我曾经写过一个框架，利用模块联合将应用程序动态地交付给我正在构建的智能镜像平台。现在，随着对微前端的兴趣达到临界质量，我看到了许多动态联邦非常有意义的情况。

# 什么是被子

Quilt 是我创作的一个 web 组件和库，用于简化微前端和模块联合。主机(或消费)应用程序不需要在“主机”模式下设置 Webpack w/Module Federation，也不需要担心如何将动态模块联邦(例如，在运行时动态地拉取模块)集成到它们的特定框架中，而 Quilt 既提供了一个库(面向除 UI 组件之外的任何联邦对象)又提供了一个 web 组件(消除了许多麻烦)。

我一直在计划——并与之斗争——web pack 和一个名为“atomico”[2](https://atomicojs.github.io)的鲜为人知的微型库，以使 web 组件部分成为现实。其中一部分是因为我自己注意到，在 Webpack 的规范中使用联邦模块很容易，但是实现动态加载要困难得多(在我看来，这需要一点神秘的知识)。我希望在未来进一步扩展 Quilt，以涵盖诸如“vite 模块联盟”或模块联盟规范之类的东西，这比将自己与 webpack 捆绑在一起更不可知

# 它是如何工作的？

棉被的库 web 组件所依赖的——是一个简单的函数[ [3](https://github.com/seam-dev/quilt/blob/main/src/lib.ts) ]，看起来与 webpack 自己的例子[ [4](https://github.com/module-federation/module-federation-examples/blob/master/dynamic-system-host/app1/src/App.js) 有些相似。主要区别在于，我们没有使用 React Lazyloader 之类的特定于框架的加载，而是选择通过动态生成的脚本标签进行加载。

你看，当一个 Webpack 模块通过一个脚本标签加载时，它把它的内容放入 DOM 对象的窗口中，使用它的名字作为键。这意味着在 window[name]下，我们会发现我喜欢称之为“模块容器”的东西。这个容器公开了两个函数:init()和 get()。init()函数用于初始化模块将与宿主应用程序共享的共享范围。如果您没有绑定依赖项，而是希望宿主应用程序与加载的模块“共享”它们，这可能会很有帮助(这会大大减少包的大小)。另一个函数 get()允许我们检索已经公开的特定 JavaScript 模块的内容——一个 Webpack 模块可以公开一个或多个 JavaScript 模块。这个 get()函数的结果是一个工厂，当它被构造时，允许我们从模块中获取对象、函数和其他语言原语，就像我们是一个普通的 JavaScript 模块一样。

web 组件采用了这一概念，并将其专门应用于应用程序中的联合——尽管我更喜欢称它们为“小应用程序”,因为我认为人们会希望根据微前端实践将多个应用程序紧密地结合在一个页面上。web 组件附加提供了模块加载失败时的处理—允许用户提供的元素在失败场景中显示—以及允许定制在模块被获取和加载时显示的元素。

我还没有用 Astro [ [5](https://astro.build) ]之类的东西测试过服务器端渲染，不过我打算很快看看你是否能两全其美(或者做些调整让它成为可能)。

我真的认为 Quilt 擅长于实现或促进以下场景:

*   多个小软件团队的“小程序”需要在一个页面上集合在一起
*   交付一个“小应用程序”作为框架的强大替代(为了可访问性和更好的集成)
*   在运行时动态调整哪些模块被联合，以支持每个用户或 A/B 测试的不同配置

我确信整个社区将会提出更多我无法预见的用例，我期待着看到它们:)

# 例子

为了演示 Quilt，我想我应该展示一些代码作为例子。请注意，我已经公开了一个模块联合模板[这里](https://github.com/seam-dev/federation-template)可以很容易地引导你制作你的第一个模块，并将其联合到另一个应用程序:)

首先，我们要去 https://github.com/seam-dev/federation-template 的[克隆存储库。您同样可以选择使用它作为模板存储库来形成您自己的存储库:](https://github.com/seam-dev/federation-template)

```
git clone [git@github.com](mailto:git@github.com):seam-dev/federation-template.git
```

package.json 中提供了 6 个主要命令，但实际上我们只关心其中的两个:

```
npm run start - Uses webpack-dev-server to give you a "hot-reload" development experience, sans module federationnpm run serve:watch - Uses webpack --watch, Vercel serve, and concurrently to continually rebuild the federated module in a "hot-reload" fashion (though the consuming host application requires a manually reload each time to load the new file)
```

否则，在这些命令之外，这是一个没有开发陷阱的普通标准 Preact(尽管您可以轻松地替换掉框架)应用程序。

要使用该应用程序，您可以使用以下内容创建一个 index.html 页面。注意，您必须使用`npm run serve:watch`运行您的联邦模块(如上),这样我们就有一个本地文件服务器来托管文件:

您现在可以在浏览器中打开 index.html(或者使用类似 browser-sync [ [6](https://browsersync.io) ])并实时查看您的模块联邦:)

关于上面的一些注意事项:我通常将 UI 加载作为默认导出从导出 I name”中公开。/loadApp”，如 webpack.config.ts 中所示。这纯粹是我自己的命名约定:

# 去哪里拿？

您目前可以通过多种方式获得被子:

`npm install @seam-dev/quilt`

```
https://unpkg.com/@seam-dev/quilt@latest/fed.wc.js
```

或者去仓库:[https://github.com/seam-dev/quilt](https://github.com/seam-dev/quilt)

我期待看到它开始在那里被使用:)

期待未来系列展示 Quilt 真正发挥作用的业务用例示例。