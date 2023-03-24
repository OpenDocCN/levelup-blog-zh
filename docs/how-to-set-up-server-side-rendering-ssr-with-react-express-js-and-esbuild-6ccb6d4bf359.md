# 如何使用 React、express.js 和 esbuild 设置服务器端渲染(SSR)

> 原文：<https://levelup.gitconnected.com/how-to-set-up-server-side-rendering-ssr-with-react-express-js-and-esbuild-6ccb6d4bf359>

![](img/cbc7d70fce4c452027d82f03ee33e664.png)

服务器端渲染是神奇的

# 目录

[什么是服务器端渲染？](https://devtails.xyz/how-to-set-up-server-side-rendering-ssr-with-react-and-esbuild#what-is-server-side-rendering)
[为什么要使用服务器端渲染？](https://devtails.xyz/how-to-set-up-server-side-rendering-ssr-with-react-and-esbuild#why-use-server-side-rendering)
[如何用 React 实现服务器端渲染](https://devtails.xyz/how-to-set-up-server-side-rendering-ssr-with-react-and-esbuild#how-to-implement-server-side-rendering-with-react)

**本教程的代码可以在** [**GitHub**](https://github.com/adamjberg/react-ssr) 上找到

# 什么是服务器端渲染？

服务器端呈现是服务器立即为客户端返回完全呈现的 HTML 页面的能力。一个简单的静态文件服务器可能会返回服务器上一个`index.html`文件的确切内容。

一种常见的做法是使用某种模板语言/引擎(例如， [Markdown](https://daringfireball.net/projects/markdown/) 、 [pug](https://pugjs.org/api/getting-started.html) 或 [jinja](https://jinja.palletsprojects.com/en/3.0.x/) )，允许对输出的 html 文本进行更高级的控制。

对于像 React 这样的客户端库，不管访问的是什么 URL，通常都会返回一个简单的`index.html`文件。

```
<!-- index.html --><html lang="en">
<head>
    <script src="app.js" async defer></script>
</head>
<body>
    <div id="root"></div>
</body>
</html>
```

一旦上面的`app.js`脚本被加载，它会立即启动 React 库，反过来 React 库会使用`#root`元素引导自己。

```
// index.tsximport React from "react";
import ReactDOM from 'react-dom';
import { App } from './App';ReactDOM.render(<App />, document.getElementById('root'));
```

上面的代码告诉 React 渲染`#root` div 下的`App`组件。您可能还会使用`[react-router](https://reactrouter.com/)`来处理不同 URL 的导航。通过这种方法，react-router 可以防止浏览器在页面上单击链接时从服务器获取新页面。

在 React 的上下文中，服务器端呈现意味着当访问特定的 URL 时，返回的 html 已经填充了页面应该显示的内容。

举一个非常简单的 React 应用程序:

```
// App.ts
import React from "react";export const App: React.FC = () => {
    return <h1>Hello World!</h1>
}
```

当服务器端渲染被正确实现时，服务器将返回:

```
<!-- index.html --><html lang="en">
<head>
    <script src="app.js" async defer></script>
</head>
<body>
    <div id="root">
        <h1 data-reactroot="">Hello World!</h1>
    </div>
</body>
</html>
```

现在我已经解释了什么是服务器端渲染，让我们来看看为什么和什么时候应该使用它，以及最后如何实际实现。

# 为什么使用服务器端渲染？

客户端 web 应用程序在 Web 2.0 中风靡一时。通过让用户的浏览器控制页面上显示的信息，它们帮助 web 应用程序变得更具交互性。

不幸的是，这也带来了一些弊端。一个 web 页面现在需要获取一个`index.html`文件，解析 html 文件，然后获取一个 JavaScript 文件(通常不止一个)，然后解析 JavaScript，执行 JavaScript 以确定在页面上呈现什么，通常还要发送几个请求来获取当前页面所需的实际信息。

在这篇文章中，我将使用一个简单的博客网站作为例子。当使用像 React 这样的客户端库来构建博客时，有一些关于站点性能和搜索引擎优化的问题可以通过服务器端渲染来改善。

# 页面性能

这是实施 SSR 的主要原因之一。使用 SSR，浏览器发出的第一个请求就会返回应该显示的 html。这可能节省了获取其他资源的多次往返，并进一步加快了速度，因为捆绑的 JavaScript 代码不需要解析和执行就可以决定在页面上显示什么。

对于包含基于单个用户的动态内容的页面，添加服务器端呈现可能会给服务器带来相当大的压力，因为每个请求都需要服务器生成唯一的响应。更大的收益来自于呈现的 html 对于多个客户端是相同的情况。在我们的博客例子中，情况正是如此。一个帖子对每个浏览它的人来说都是一样的。我们不会在这篇文章中讨论这个问题，但是缓存呈现的 html 是可能的，这样就不需要在每次请求时都重新呈现。

# 支持禁用 JavaScript 的浏览器

有一小部分互联网用户要么禁用 JavaScript，要么使用计算能力有限的设备访问互联网。在某些情况下，使用服务器端呈现来预呈现内容使得这些用户仍然可以访问应用程序的内容(尽管没有一些交互部分)。

# 搜索引擎优化

网络爬虫也属于这一类，因为它们在爬行网页时通常不运行客户端 javascript。如果您的页面需要 JavaScript 来查看内容，这些爬虫将无法索引您的网页。这可能会对您的搜索引擎优化产生负面影响，导致您的网站不会出现在搜索结果中。

# 社交媒体上的链接预览

大多数社交媒体网站使用[开放图形协议](https://ogp.me/)来决定当你在社交媒体上分享链接时，如何显示那些花哨的预览图像。如果没有服务器端呈现，就不可能动态填充这些信息。对于我们的博客示例，这意味着特定帖子的链接预览将返回与主页相同的元信息。因此，它不是展示我们链接到的特定帖子的漂亮预览，而是显示网站的一般信息。在以后的文章中，我会更详细地介绍如何在我们正在实现的服务器端渲染之上添加这些链接预览。

# 如何用 React 实现服务器端渲染

# 初始化项目

创建一个新文件夹来存放此项目:

`mkdir react-ssr`

更改目录

`cd react-ssr`

初始化 npm 包

`npm init`

# 创建简单的反应应用程序

安装 react 和 react-dom

`npm i react react-dom`

创建一个文件夹来存放 React 客户端代码

`mkdir -p client/src`

在`client/src`中创建名为`index.tsx`的文件

```
// index.tsx
import React from "react";
import ReactDOM from 'react-dom';
import { App } from './App';ReactDOM.render(<App />, document.getElementById('root'));
```

在`client/src`中创建应用组件`App.tsx`

```
// App.tsx
import React, { useEffect, useState } from "react";export const App: React.FC = () => {
    const [clientMessage, setClientMessage] = useState("");

    useEffect(() => {
        setClientMessage("Hello From React");
    })return <>
        <h1>Hello World!</h1>
        <h2>{clientMessage}</h2>
    </>
}
```

# 将 React 应用程序与 esbuild 捆绑在一起

安装 esbuild

`npm i --dev esbuild`

将构建脚本添加到`package.json`

```
{
  "name": "react-ssr",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    **"client:build": "esbuild client/src/index.tsx --bundle --outfile=built/app.js"**,
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "react": "^17.0.2",
    "react-dom": "^17.0.2"
  },
  "devDependencies": {
    "esbuild": "^0.14.0",
  }
}
```

# 创建 Starter Express.js 应用程序

创建一个文件夹来存放 express.js 应用程序

`mkdir -p server/src`

在`server/src`中创建名为`server.tsx`的文件

```
// server.tsx
import express from "express";const app = express();app.get('/', (req, res) => {
    const html = `
        <html lang="en">
        <head>
            <script src="app.js" async defer></script>
        </head>
        <body>
            <div id="root"></div>
        </body>
        </html>
    `
    res.send(html);
});app.use(express.static("./built"));app.listen(4242);
```

# 将 express.js 服务器与 esbuild 捆绑在一起

查看[这篇文章](https://devtails.xyz/bundling-your-node-js-express-app-with-esbuild),了解更多关于将服务器端代码与 esbuild 捆绑在一起的细节。

将`server:build`和`start`脚本添加到`package.json`

```
{
  "scripts": {
    "client:build": "esbuild client/src/index.tsx --bundle --outfile=built/app.js",
    **"server:build": "esbuild server/src/server.tsx --bundle --outfile=built/server.js --platform=node",
    "start": "node built/server.js"**
  },
}
```

# 构建服务器捆绑包

`npm run server:build`

# 运行服务器

`npm start`

你现在可以打开浏览器到 http://localhost:4242 并看到“Hello World！”消息。

# 添加服务器端渲染

```
// server.tsx
import express from "express";
import * as ReactDOMServer from 'react-dom/server';
import { App } from "../../client/src/App";const app = express();app.get('/', (req, res) => {
 **const app = ReactDOMServer.renderToString(<App />);** const html = `
        <html lang="en">
        <head>
            <script src="app.js" async defer></script>
        </head>
        <body>
            <div id="root">**${app}**</div>
        </body>
        </html>
    `
    res.send(html);
});app.use(express.static("./built"));app.listen(4242);
```

这会导入 App 组件，并使用`react-dom/server`库将其呈现为一个字符串。然后，我们将呈现的字符串放在#root div 中，并返回结果 html。

用`npm run server:build`重新构建服务器。然后用`npm start`重启服务器。

您可以使用`curl`通过运行`curl [http://localhost:4242](http://localhost:4242.)` [来确认这是否按预期工作。](http://localhost:4242.)

您应该会看到以下输出:

```
<html lang="en">
<head>
    <script src="app.js" async defer></script>
</head>
<body>
    <div id="root"><h1 data-reactroot="">Hello World!</h1></div>
</body>
</html>
```

请注意，缺少“来自 React 的 Hello”消息。这是因为`renderToString`方法不会运行`useEffect`逻辑。

如果我们在浏览器中刷新页面，我们应该会看到正确的`clientMessage`。

# **从**[**react DOM . render**](https://reactjs.org/docs/react-dom.html#render)**切换到**[**react DOM . hydrate**](https://reactjs.org/docs/react-dom.html#hydrate)

虽然这看起来像预期的那样工作，但是还有最后一个变化要做。服务器返回呈现的 html，然后一旦加载了`app.js`脚本，它将重新呈现整个应用程序组件并覆盖已经存在的内容。

```
// index.tsx
̶R̶e̶a̶c̶t̶D̶O̶M̶.̶r̶e̶n̶d̶e̶r̶(̶<̶A̶p̶p̶ ̶/̶>̶,̶ ̶d̶o̶c̶u̶m̶e̶n̶t̶.̶g̶e̶t̶E̶l̶e̶m̶e̶n̶t̶B̶y̶I̶d̶(̶'̶r̶o̶o̶t̶'̶)̶)̶;̶ **ReactDOM.hydrate(<App />, document.getElementById('root'));**
```

通过这一更改，React 将重用现有标记，并将任何事件侦听器附加到现有元素。

在我们的小例子中，这不会有太大的不同，但是在一个大的应用程序中，这可以节省大量的重新渲染。

# 总结

恭喜你！现在，您有了一个具有服务器端呈现的 react 应用程序。根据我的经验，从服务器端渲染开始比事后实现要容易得多。现在您已经了解了它背后的基础，您应该能够将这些原则应用到现有的 React 应用程序中。

# 额外资源

[https://www . digital ocean . com/community/tutorials/react-server-side-rendering](https://www.digitalocean.com/community/tutorials/react-server-side-rendering)

*原载于 2021 年 11 月 28 日*[*https://devtails . XYZ*](https://devtails.xyz/how-to-set-up-server-side-rendering-ssr-with-react-and-esbuild)*。*