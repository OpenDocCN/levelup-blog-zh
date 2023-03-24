# 我的新 React 包裹工作流程

> 原文：<https://levelup.gitconnected.com/my-new-react-workflow-with-parcel-a6fdaaf4846b>

## 脱离创建-反应-应用程序，学习捆绑我自己的资产

![](img/626d945c958fb9a7cb3c5d594d72dca7.png)

大家好👋我希望你在这艰难的时刻能平安无事。由于这种情况，我已经有一段时间没有写任何东西了，但我想为什么不分享一下我的新 React 工作流，因为有些人可能会觉得它很有趣，所以我们开始吧。

我几乎总是使用 create-react-app 作为我的 react starter，有时 Gatsby 或 Next.js 分别用于静态站点和服务器端渲染，但大多数情况下 create-react-app 用于我所有的单页面应用程序。然而，正如你们中的一些人可能知道的，create-react-app 的内部是使用 react-scripts 包抽象出来的。这有助于像我这样的初学者轻松入门，而不必担心 webpack。但是我很好奇它实际上是如何工作的，并决定从头开始制作一个 React 应用程序。

在学习捆扎机的过程中，我发现了[package](https://parceljs.org)，这是一款无需配置的捆扎机，开箱即可工作。这让我非常兴奋，几个步骤之后，我也用 React 做了一个包，下面是我如何设置它的。

另外，一定要坚持到最后，看看一个超级简单的方法，开始使用我做的一个小软件包。

# 步骤 0

如果您还没有安装 [node](https://nodejs.org) 和 [npm](https://npmjs.org) ，那么请从前面的链接为您的系统下载它们的最新版本。

# 第一步

为您的项目创建一个目录，在 Windows 上我使用了以下命令。我相信你可以把它们翻译到你的操作系统上。

```
mkdir react-with-parcel
cd react-with-parcel
```

# 第二步

使用以下命令初始化 npm 项目并下载依赖项:

```
npm init -y
npm i -D parcel-bundler @babel/core @babel/preset-env @babel/preset-react
npm i react react-dom
```

如你所见，我们使用`npm init`初始化一个 npm 项目，而`-y`标志就在那里，所以它不会问你默认的问题。然后我们安装 package-bundler 和一些 babel 包作为开发依赖项(这就是为什么使用了`-D`标志)，还有`react`和`react-dom`作为常规依赖项。我目前仍在使用包裹 v1(即包裹-bundler npm 包裹而非包裹)。这是因为包裹 v2 仍在 alpha 中，还不稳定。然而，一旦它稳定了，它就承诺了一些惊人的特性，比如对 jsx 的开箱即用支持，所以我们甚至不必配置 babel。

# 第三步

现在我们已经安装了所有的依赖项，我们可以实际创建我们的应用程序了。Parcel 的工作原理是以一个 HTML 文件作为入口点(也可以有多个)，然后用它来捆绑所有的资产。所以让我们创建一个`index.html`文件。就我个人而言，我把这个文件放在一个`src`目录中，但是如果你愿意，你可以直接把它放在你的项目的根目录中。但是，请确保在以下步骤中更改路径。

```
<!--The src/index.html file--><!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Parcel & React</title>
  </head>
  <body>
    <div id="root"></div>
    <script src="./index.js"></script>
  </body>
</html>
```

如你所见，我们创建了一个 id 为“root”的`div`，然后使用一个脚本标签链接到我们的 JavaScript 文件。因此，让我们创建 JavaScript 文件。

```
// The src/index.js fileimport React from "react";
import ReactDom from "react-dom";
import "./index.css";const App = () => <h1>Hello World</h1>;ReactDom.render(<App />, document.getElementById("root"));
```

我们将文件保持得非常简单，但是您可以非常容易地从您的文件或 npm 包中导入其他组件和 CSS 文件。因为我已经进口了”。/index.css”，让我们继续创建该文件:

```
/* The src/index.css file */* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}html {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
    Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
}code {
  font-family: "Courier New", Courier, monospace;
}
```

我们保持它非常基本，只是一些小的重置和系统字体。

# 第四步

我们快完成了！现在转到你的`package.json`，添加`start`和`build`脚本。这是您的`package.json`现在的样子:

```
{
  "name": "react-with-parcel",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "parcel src/index.html",
    "build": "parcel build src/index.html"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "react": "^16.13.1",
    "react-dom": "^16.13.1",
  },
  "devDependencies": {
    "@babel/core": "^7.9.0",
    "@babel/preset-env": "^7.9.0",
    "@babel/preset-react": "^7.9.4",
    "parcel-bundler": "^1.12.4"
  }
}
```

我们所做的就是调用 parcel 并给它我们想要使用的 HTML 文件。为了构建一个生产构建，我们调用 parcel build 并再次给它 HTML 文件。宗地构建将输出到项目中的 dist 目录。

# 你完了

只需打开一个终端并运行`npm start`，开发服务器应该在 [https://localhost:1234](https://localhost:1234) 上启动。惊人的正确！

# 让这变得更加容易

因为我希望做更多次，所以我决定做一个小小的 npm 包来引导我的项目，这样它就像 create-react-app 一样简单。我终于完成了这个包——我把它叫做`parcreate`,现在你可以在你的终端上运行它了。

```
# The recommended way: (so that you have the latest version)
npx parcreate my-apps-name# The old fashioned way:
npm i -g parcreate
parcreate my-apps-name
```

创建`parcreate`非常有趣，我将很快添加更多带有附加功能的模板，比如`scss`，所以请保持关注。

你可以在这里找到 github repo for parcreate [，如果你在运行它时遇到任何问题，请告诉我，因为我只在 windows 上测试过它。感谢大家阅读我的发现&确保安全。再见！](https://github.com/kartiknair/parcreate)

又及:我正努力在社交媒体上更加活跃&认识更多的人，所以请在我的推特(@nairkartik_)上给我留言。