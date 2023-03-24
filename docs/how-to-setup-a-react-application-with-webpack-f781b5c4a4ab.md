# 如何使用 Webpack 设置 React

> 原文：<https://levelup.gitconnected.com/how-to-setup-a-react-application-with-webpack-f781b5c4a4ab>

## 对于那些一直想在不使用 create-react-app 的情况下构建 React 应用程序的初学者

![](img/5f30c797984711d80b2e6c578e6f2d6d.png)

Webpack 徽标

Create-react-app 让我们所有人的生活变得更加轻松，绝对是快速开始构建 react 应用程序的绝佳工具。然而，你有没有想过从头开始制作这样一个应用程序可能会涉及到什么？

我一直用 CRA 做我自己的爱好项目，但是最近我收到了一个定制 webpack 配置的应用程序。这给了我一个很好的机会，在我投入到这个项目并开始贡献之前，我可以学习一些 webpack 的基础知识。

Webpack 有很多特性，文档虽然很有帮助，但可能会让人不知所措。我花了很多时间浏览文档，还在 YouTube 上看了很多教学视频。在这里，我想记录和分享我学到的东西，我希望任何有类似经历的人都会觉得这很有用。

# 初始化项目

首先，让我们创建一个空项目，并安装一些依赖项。创建一个目录，然后运行

```
npm init -y
```

初始化您的新项目。这应该会创建一个默认的 package.json 清单文件。

> 请随意省略-y 标志，并根据您的需要初始化项目。

现在让我们安装一些我们将使用的基本软件包:

*   react —主 react 库
*   react-DOM——允许我们在浏览器中使用 react 的包
*   web pack——JavaScript bundler，我们将在下面深入探讨
*   webpack-cli —允许我们从命令行运行 webpack 命令的工具。

要安装所有这些程序，请运行以下命令:

```
npm i react react-dom
npm i -D webpack webpack-cli
```

> 注意，webpack 和 webpack-cli 是作为开发依赖项安装的，因为我们只在开发过程中需要它们。整个教程中唯一的运行时包是 react 和 react-dom。

# 设置项目结构

此时，您应该已经在根目录中直接拥有了 package.json 和 package-lock.json 文件。现在创建几个空文件，我们稍后将填充这些文件，并设置您的项目，使其模仿如下所示的目录结构:

# 学习基本的 Webpack 术语

在我们开始配置 webpack 和制作我们的小应用程序之前，让我们了解一些关于 webpack 的事情。

webpack 是一个 JavaScript 代码捆绑器，它遍历项目的依赖图(您在 JS 文件中使用的导入链)，并创建一个静态 JavaScript 文件，准备附加到您的 HTML。

在我们开始使用 webpack 之前，有几个关于它的术语必须介绍一下:

*   Entry —这是依赖关系树(通常是 src/index.js)的顶部，webpack 从这里开始捆绑过程。
*   输出—输出文件。又名捆绑。
*   加载器——默认情况下，webpack 只支持 JavaScript 文件，但是我们显然希望能够导入其他文件类型(CSS、JSX 等)。).这就是装载机发挥作用的地方。它们是帮助我们将非 JavaScript 文件直接导入 JavaScript 的包(不包含在 Webpack 中)。
*   插件——插件也是其他第三方包，可以与 webpack 一起使用以扩展其功能。有很多插件，但在这里，我们将只处理 html-webpack-plugin。稍后将详细介绍。

# 创建一个最小的 React 应用程序

这就是我们开始学习 webpack 所需要了解的全部内容。现在让我们继续用一些代码填充我们之前创建的那些文件，这样我们就可以在浏览器上进行测试了。

> 我假设你们熟悉 React，并且不会在本文中解释 React 代码。

这个应用程序应该做的就是呈现一个标题标签，这个标签向页面显示“Hello World”。但是，如果您尝试使用

```
...
<script src="index.js" />
</body>
</html>
```

它不起作用。如果你在浏览器中打开它，你只会看到一个空白页。

这是因为浏览器不知道如何从“”导入 App。/App”。浏览器只能加载静态 JS 文件，所以让我们最后配置 webpack，将我们的应用程序转换成浏览器可以理解的东西。

# 安装装载机

让我们首先安装 babel-loader，它允许我们将 JSX 编译成浏览器兼容的 JavaScript。为此，请运行以下命令:

```
npm i -D @babel/core @babel/preset-env @babel/preset-react babel-loader
```

我们所做的是安装巴别塔，两个预置，然后最后加载程序需要加载我们的 JSX 文件。请浏览 babel 的[文档](https://babeljs.io/docs/en/babel-preset-env)以了解更多关于这些预设功能的信息。

# 配置 Webpack

现在我们准备请求 webpack 在捆绑期间友好地使用 Babel 来理解任何 JSX 文件。

根据我们上面的讨论，入口和输出应该是清楚的，但是什么是 module.rules 呢？

从[文件](https://webpack.js.org/configuration/module/#modulerules)来看，是:

> 一组[规则](https://webpack.js.org/configuration/module/#rule)，在创建模块时与请求相匹配。这些规则可以修改模块的创建方式。他们可以向模块应用加载器，或者修改解析器。

在我们的配置文件中，我们要求 webpack 使用 babel-loader，只要它看到任何以 js 或 jsx 结尾的文件。了解更多关于正则表达式(/\。(js|jsx)$/以上)工作，请把[这个](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)查出来。

# 捆绑应用程序

现在让我们向 package.json 添加一个脚本，这样我们就可以随时轻松地构建我们的应用程序。

> 请随意从命令行运行 webpack-cli。我添加了一个脚本，这样如果你想为开发、生产、登台等添加不同的脚本，也许可以在你的脚本中添加[模式](https://webpack.js.org/configuration/mode/)，你现在将知道如何以及在哪里添加。

终于我们准备好跑了

```
npm run build
```

如果您已经完全理解了我的意思，那么这应该会在项目根目录下的 dist 文件夹中创建一个 main.js 文件。我们现在可以将这个捆绑的 JavaScript 附加到我们的 html 中。

# 运行捆绑的应用程序

按照上面的建议添加脚本标签后，在浏览器中打开你的 index.html，瞧，你会看到“Hello World”！就是这样。您已经捆绑了您的第一个应用程序。很简单，对吧？

# 设置 HtmlWebpackPlugin

手动附加捆绑的 JS，就像我们上面做的那样，都很好，如果我们的捆绑包中只有一个 JavaScript 文件，那么我们最好就此打住。但是在一个真实的应用程序中，你将使用各种 webpack [插件](https://webpack.js.org/plugins/split-chunks-plugin/)来分块你的 JavaScript 文件，甚至可能为了缓存的目的而散列它们。所有这一切意味着，我们不想手动将我们的捆绑脚本导入到我们的 HTML 中。

为了自动化这个过程，让我们使用以下命令安装 html-webpack-plugin:

```
npm i -D html-webpack-plugin
```

现在我们修改我们的配置文件来包含这个插件:

我们所做的是包含插件，并为其提供一个模板 html，webpack 将在构建后将捆绑的 JavaScript 附加到该模板 html 中。

您可以继续从 src/index.html 中删除

```
npm build
```

您会注意到，不仅 main.js 是在 dist 文件夹中创建的，而且还有一个 index.html——main . js 会自动包含在内！

# 下一步是什么？

您可能已经注意到，我们没有对“index.css”文件做任何事情。我想把这个留给你们做练习。查找 style-loader 和 css-loader，看看能否将 index.css 导入到 App.js 文件中。这个过程与我们使用 babel-loader 非常相似。您需要首先编写一个测试来过滤掉。css 文件，然后使用样式和 css 加载器。

我更进一步，用 webpack-dev-server 配置了我的应用程序。你可以在我的 [github](https://github.com/Schapagain/tutorial-react-webpack) 里找到完成的项目。要启动 dev 服务器，只需运行(当然是在安装 npm 之后):

```
npm run start
```

因为 dev-server 设置了热重装，所以在对应用程序进行任何更改后，您不再需要刷新浏览器。

感谢阅读！