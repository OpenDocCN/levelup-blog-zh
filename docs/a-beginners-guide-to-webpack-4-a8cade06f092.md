# Webpack 4 初学者指南

> 原文：<https://levelup.gitconnected.com/a-beginners-guide-to-webpack-4-a8cade06f092>

在科技世界里，唯一不变的是变化。流行模块 bundler Webpack 经历了一次大规模更新，现已发布第 4 版。Webpack 4 提供了巨大的性能改进，零配置，旨在减轻开发人员的工作。本教程将介绍 Webpack 的核心基础知识，帮助您入门。

![](img/195c2e31092ae97bf81019c7eaef39d9.png)

只是网络包的东西！

# 什么是 Webpack？

Webpack 是一个**构建工具，它将所有的*资产，包括 Javascript、图像、字体和 CSS，放到一个依赖图中。*** Webpack 允许你在源代码中使用`require()`指向本地文件，比如图像，并决定它们在最终的 Javascript 包中如何处理，比如用指向 CDN 的 URL 替换路径。

# 依赖图的简史

Webpack 给了我们一个依赖图。那是什么意思？

在早期，我们通过以特定的顺序包含文件来“管理”Javascript 依赖关系:

```
<script src="jquery.min.js"></script>  
<script src="jquery.some.plugin.js"></script>  
<script src="main.js"></script>
```

这太慢了，因为它导致了过多的 HTTP 请求。然后我们发展到在构建步骤中连接和缩小我们的脚本:

```
// build-script.js
var scripts = [  
    'jquery.min.js',
    'jquery.some.plugin.js',
    'main.js'
].concat().uglify().writeTo('bundle.js');// Everything our app needs!
<script src="bundle.js"></script>
```

这仍然**依赖于串联文件**的手动排序。更糟糕的是，代码只能通过全局变量进行通信！第一个脚本声明了一个全局`jQuery`变量，然后`jquery.some.plugin.js`创建了一个新的全局，或者修改了全局`jQuery`对象。呸。

现在我们使用 CommonJS 或 ES6 模块将我们的 Javascript 放到一个真正的**依赖图中。我们制作明确描述他们需求的小文件。这是一件好事！**

```
// Version.js
module.exports = { version: 1.0 };// App.js
var config = require('./Version.js');  
console.log('App Version:', config.version);
```

浏览器不支持`require()`，所以我们用一个构建工具将上述文件转换成浏览器可以正常执行的“捆绑”文件。

像 Browserify 这样的工具已经解开了你的`require()`调用，并构建了“捆绑”的 Javascript 文件发送给浏览器。Webpack 在哪里出现？

# 快速启动

1.  让我们创建一个目录。

```
mkdir webpack_demo && cd webpack_demo
```

2.初始化`yarn`或`npm`，随你喜欢。这将设置一个 package.json。

```
npm init// ORyarn init
```

3.在 devDependencies 中添加新的 webpack 4.x

```
npm install --save-dev webpack webpack-cli// ORyarn add webpack webpack-cli --dev
```

4.现在打开`package.json`和一个构建脚本。

```
"scripts: {
    "build": "webpack"
}
```

保存它，现在尝试运行

```
npm run build
```

当你按下命令时

```
ERROR **in** Entry module not found: Error: Can't resolve './src' in '~/webpack_demo'
```

Webpack 正在寻找指向“”的入口点。/src ”,我们的项目中目前还没有`src`目录。

以前，我们需要在`webpack.config.js`中指定这一点，但是如前所述，我们不需要在 Webpack 4 中定义入口点。因此，让我们将`./src/index.js`创建为默认值，并键入以下代码。它会把这个包分成。/dist/main.js

```
***console***.log('hello webpack');
```

现在再试一次

```
npm run build
```

现在，当你点击这个命令，你会遇到这个警告。

```
WARNING in configuration
The 'mode' option has not been set, webpack will fallback to 'production' for this value. Set 'mode' option to 'development' or
 'production' to enable defaults for each environment.
You can also set it to 'none' to disable any default behavior. Learn more: [https://webpack.js.org/concepts/mode/](https://webpack.js.org/concepts/mode/)
```

# 网络包模式

Webpack 附带了两个配置文件。即。,

*   **开发**配置——使用`webpack-dev-server`(热重装)，调试启用等
*   **Production**config——这将产生一个在生产环境中使用的优化的、最小化的(丑陋的 JS)source mapped 包。

有了新的模式特性，Webpack 4 通过简单地在命令中添加一个模式参数来默认处理这些问题。我们上面遇到的警告是缺少`mode`。在你的`package.json` Webpack 中找不到确定正确构建路径的模式。要指定模式，只需将以下内容添加到 package.json 中:

```
"scripts": {
    "dev": "webpack --mode development",
    "build": "webpack --mode production"
}
```

生产模式允许许多现成的优化。包括缩小、范围提升、树抖动代码分割等等。另一方面，开发模式针对编译速度进行了优化，只不过提供了一个未缩小的包。

# 用 Babel 7 传输 Javascript ES6

现代 Javascript 大部分是用 ES6 写的，但并不是每个浏览器都知道如何处理 ES6。我们需要对它进行转换，以便代码可以在任何地方工作。

这个转换步骤称为**传输**。Transpiling 是采用 ES6 并使其能被旧浏览器理解的行为。

Webpack 不知道如何进行转换，但他的朋友称之为 **loaders** :变形金刚。

**babel-loader** 是用于传输 ES6 及以上版本，下至 ES5 的 webpack 加载器。

要开始使用加载程序，我们需要安装一些依赖项。特别是:

*   巴别塔核心
*   巴贝尔装载机
*   babel 预置 env 用于将 Javascript ES6 代码编译成 ES5

让我们开始吧:

```
yarn add babel-core babel-loader babel-preset-env --dev// ORnpm install @babel/core babel-loader @babel/preset-env --save-dev
```

接下来，通过创建一个名为**的新文件来配置 Babel。项目文件夹中的 babelrc** :

```
{
    "presets": [
        "env"
    ]
} 
```

如果你在这一步面临一些问题，你可能需要更新。从旧预设到新预设的 babelrc 文件" @babel/preset-env "

```
{
    "presets": [
        "@babel/preset-env"
    ]
}
```

现在我们达到了分歧，我们有 2 个选项来配置 babel-loader:

*   使用 webpack 的配置文件
*   使用—模块绑定您的 npm 脚本

```
"scripts": {
    "dev": "webpack --mode development --module-bind js=babel-loader",
    "build": "webpack --mode production --module-bind js=babel-loader"
}
```

**或**

创建一个`webpack.config.js`

```
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      }
    ]
  }
}
```

我选择创建一个`webpack.config.js`,原因很简单——零配置适用于入口点、默认输出和指定模式。通过使用一个配置文件，它保持了`npm`脚本的整洁和平静。

> 没有必要指定入口点，除非您想要自定义它。

Webpack 需要另外两个组件来处理 HTML: h `tml-webpack-plugin`和`html-loader`。

添加依赖项:

```
npm install html-webpack-plugin html-loader --save-dev// ORyarn add html-webpack-plugin html-loader --save-dev
```

现在将以下内容添加到您的 webpack.config.js 中

```
plugins: [
    new HtmlWebPackPlugin({
        template: "./src/index.html",
        filename: "./index.html"
    })
]
```

现在重新构建

```
npm run build
```

并且检查文件`dist/index.html`你找到你的 html minifed 了吗？这就是 Webpack 能做到的！

# 将 CSS 提取到文件中

现在我们在`dist`目录中有了作为`main.js`的小型 JavaScript 文件和作为`index.html`的小型 HTML 文件，那么 CSS 呢？Webpack 不知道将 CSS 提取到文件中。以前是**extract-text-web pack-plugin**的工作。但不幸的是，该插件不能很好地与 Webpack 4 兼容。

> *extract-text-webpack-plugin 已经到了维护它成为太多负担的地步，而且升级一个主要的 web pack 版本由于它的问题而变得复杂和麻烦也不是第一次了。迈克尔·辛尼亚斯基*

但是，Webpack 4 有一个新的选项，[mini-CSS-extract-plugin](https://github.com/webpack-contrib/mini-css-extract-plugin)，它将帮助我们克服这个问题。所以，让我们试试这个。

***注*** *:务必将 Webpack 更新到 4.2.0 版本。否则 mini-css-extract-plugin 就不行了！*

我们首先需要安装的是`css-loader`(目前)。我们现在使用的是普通的 CSS，但是你可以选择你喜欢的。常见的装载机有`scss-loader`、`post-css-loader`等。要安装 css-loader 和 mini-css-extract-plugin，只需运行以下命令。

```
npm install mini-css-extract-plugin css-loader --save-dev
```

接下来我们需要的是一个 CSS 文件。在 src 目录中创建一个`index.css`,添加下面的代码。

```
h1 {
    color: rebeccapurple;
}
```

现在，我们需要配置我们的新插件，所以在`webpack.config.js`中添加以下代码

```
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
plugins: [
       new MiniCssExtractPlugin({
        filename: "[name].css",
        chunkFilename: "[id].css"
    })
]
```

整个`webpack.config.js`会是这个样子。

```
const HtmlWebPackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
module.exports = {
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /node_modules/,
                use: {
                    loader: "babel-loader"
                }
            },
            {
                test: /\.html$/,
                use: [
                    {
                        loader: "html-loader",
                        options: { minimize: true }
                    }
                ]
            },
            {
                test: /\.css$/,
                use: [MiniCssExtractPlugin.loader, "css-loader"]
            }
        ]
    },
    plugins: [
        new HtmlWebPackPlugin({
            template: "./src/index.html",
            filename: "./index.html"
        }),
        new MiniCssExtractPlugin({
            filename: "[name].css",
            chunkFilename: "[id].css"
        })
    ]
};
```

最后，将 CSS 文件导入到主 JS 文件中

```
**import './index.css'**;
***console***.log('hello webpack');
```

现在运行构建

```
npm run build
```

检查`dist`目录，你会看到`main.css`。这个文件是由 Webpack 从我们导入的 CSS 文件中精心制作的。现在，检查索引页面，看看你的 CSS 现在工作了。

# 无需重新加载即可查看更改？

每当你修改代码时，你需要执行`npm run dev`吗？这远非理想。

用 Webpack 配置开发服务器只需一分钟。

一旦配置完毕 [Webpack 开发服务器](https://github.com/webpack/webpack-dev-server)将在浏览器中启动你的应用程序。每当你改变一个文件时，它也会自动刷新浏览器的窗口。要设置 Webpack 开发服务器，请安装包含以下内容的软件包:

```
npm install webpack-dev-server --save-dev
```

要在不手动重新加载页面的情况下查看更新，修改`package.json`中的 dev server 命令

```
**"dev"**: **"NODE_ENV=development webpack-dev-server --content-base dist --hot --mode development"**,
**"build"**: **"webpack --mode production"**
```

这使得 Webpack 中的热模块替换成为可能，它显示了代码中的新变化(在`css`或`js`或任何文件中，只要 Webpack 知道如何加载它),而无需完全重新加载页面。用`npm run dev`重启服务器，尝试修改你的 CSS。您会注意到页面中的变化，而不需要实际重新加载页面。

## 结论

让我们回顾一下我们所学的内容:

1.  什么是 Webpack？
2.  如何从 Webpack 开始？
3.  什么是 webpack 模式？
4.  什么是运输？如何用巴别塔 7 处理 JavaScript ES6
5.  如何将 CSS 解压到一个文件？
6.  什么是 webpack-dev-server

谢谢你花时间来看我的文章！

如果你喜欢这篇文章，放下一些掌声，[在 Medium 上关注我](https://medium.com/@bhavikbamania)，也和你的朋友分享这篇文章。你也可以在 [twitter](https://twitter.com/bhavikbamania) 和 [instagram](https://www.instagram.com/bhavikbamania/) 上关注我。

下次见！

[![](img/9914c5dd23ac08b70eea6f4f9ba6fed2.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/webpack) [## 学习 Webpack -最佳 Webpack 教程(2019) | gitconnected

### 8 大 Webpack 教程-免费学习 Webpack。课程由开发人员提交并投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/webpack)