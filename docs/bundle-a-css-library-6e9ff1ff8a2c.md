# 捆绑一个 CSS 库

> 原文：<https://levelup.gitconnected.com/bundle-a-css-library-6e9ff1ff8a2c>

## 如何用 SASS、postcss 和 clean-css 构建自己的自定义 CSS 库

![](img/63a5193e8c1180a11265b825f9f73dbe.png)

照片由 [KOBU 机构](https://unsplash.com/@kobuagency?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](/s/photos/css?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我们以相对分散的方式构建了 DeckDeckGo😅。这是我们的网络编辑器，可以用来创建和展示幻灯片，还可以自动与我们的遥控器同步。还有一个开发者工具包，它支持 HTML 或 markdown，甚至还有另一个版本，我们只用于在线部署您的演示文稿作为渐进式网络应用程序。

所有这些多种应用程序和套件都有一个共同点，即它们共享完全相同的核心和功能，而不管它们的技术如何，这要归功于使用[模板](https://stenciljs.com/)制作的 Web 组件。

这些也必须共享相同的布局功能。例如，如果我们在全屏模式下定义了一个 32px 的根字体大小，它应该可以应用到任何地方，因此，应该可以在我们的 eco 系统中容易且一致地传播。

这就是为什么我们必须创建我们自己的自定义 CSS 库，也是为什么我要与你分享，你如何也可以为你自己的项目捆绑这样一个实用程序。

# 信用

这个方案是由 CSS 框架[布尔玛](https://bulma.io/)实现的。没有必要重新发明轮子，当它已经被奇妙地解决的时候。感谢布尔玛[开源](https://github.com/jgthms/bulma)🙏。

# 入门指南

为了初始化我们的库，我们创建了一个新文件夹，例如`bundle-css`，并用一个新的`package.json`文件来描述它。它应该包含库的名称、版本、主文件，在我们的例子中是一个(即将到来的)`sass`入口文件、作者和许可证。当然，您可以添加更多的细节，但这些给了我们一个快速的基础。

```
{
  "name": "bundle-css",
  "version": "1.0.0",
  "main": "index.scss",
  "author": "David",
  "license": "MIT"
}
```

在一个新文件夹`src`中，我们创建了样式表文件`index.scss`。正如我在介绍中提到的`fullscreen`模式，我们可以在文件中添加一个全屏样式，将蓝色背景应用到“main”元素的子段落中。

```
:fullscreen #main {
  p {
    background: #3880ff;
  }
}
```

# 干净输出

我们可能希望确保每次构建库时，结果都不包含我们之前删除的任何样式。

这就是为什么我们首先将 [rimraf](https://github.com/isaacs/rimraf) 添加到我们的项目中，以便在每次构建开始时删除输出文件夹。

```
npm i rimraf -D
```

*请注意，我们添加到项目中的所有依赖项都必须作为开发依赖项添加，因为它们都不是输出的一部分。*

一旦安装了 rimraf，我们就可以通过编辑`package.json`中的`scripts`来启动我们的`build`。

```
"scripts": {
  "build": "rimraf css"
}
```

我选择了`css`作为包含 CSS 输出的输出文件夹的名称。你可以使用另一个名字，重要的是将它添加到文件`package.json`中，以便将其包含在最终的包中，你可以稍后安装在你的应用程序中或发布到 [npm](http://npmjs.com/) 。

```
"files": [
  "css"
]
```

至此，合起来，我们的`package.json`应该包含以下内容:

```
{
  "name": "bundle-css",
  "version": "1.0.0",
  "main": "index.scss",
  "scripts": {
    "build": "rimraf css"
  },
  "files": [
    "css"
  ],
  "author": "David",
  "license": "MIT",
  "devDependencies": {
    "rimraf": "^3.0.2"
  }
}
```

# 厚颜无耻

我们正在使用 [SASS](https://sass-lang.com/) 扩展来编辑样式。所以，我们要把它编译成 CSS。为此，我们使用了 [node-sass](https://github.com/sass/node-sass) 编译器。

```
npm i node-sass -D
```

我们用一个新的脚本增强了我们的`package.json`,它应该负责编译成 CSS，我们用我们的主`build`脚本链接它。

```
"scripts": {
  "build": "rimraf css && npm run build-sass",
  "build-sass": "node-sass --output-style expanded src/index.scss ./css/index.css"
}
```

我们提供输入文件，并将输出指定为编译参数。我们还使用选项`expanded`来确定 CSS 的输出格式。它使它具有可读性，而且，由于我们将在管道的后期缩小它，我们还没有多余的新行大小。

如果我们通过运行`npm run build`来尝试我们的构建脚本，我们应该会发现一个从`SASS`转换成`CSS`的输出文件`/css/index.css`。

```
:fullscreen #main p {
  background: #3880ff;
}
```

# 自动修复

为了支持更老的浏览器和 Safari，有必要给选择器`:fullscreen`加上前缀[。其他选择器也可能是这种情况。为了自动解析 CSS 并将供应商前缀添加到 CSS 规则中，使用来自](https://caniuse.com/#search=fullscreen)[的值，我可以使用](https://caniuse.com/)吗，我们正在使用 [autoprefixer](https://github.com/postcss/autoprefixer) 。

```
npm i autoprefixer postcss-cli -D
```

我们现在再次添加一个新的构建脚本到我们的`package.json`中，并且在我们已经声明的两个步骤之后链接这个步骤，因为我们的目标是一旦 CSS 被创建，就在值前面加上前缀。

```
"scripts": {
  "build": "rimraf css && npm run build-sass && npm run build-autoprefix",
   ...
  "build-autoprefix": "postcss --use autoprefixer --map --output ./css/index.css ./css/index.css"
}
```

使用这个新命令，我们用新值覆盖了 CSS 文件，并生成了一个`map`文件，这对调试非常有用。

如果我们运行我们的构建管道`npm run build`，输出`css`文件夹现在应该包含一个`map`文件和我们的`index.css`输出，其前缀应该如下:

```
:-webkit-full-screen #main p {
  background: #3880ff;
}
:-ms-fullscreen #main p {
  background: #3880ff;
}
:fullscreen #main p {
  background: #3880ff;
}

/*# sourceMappingURL=index.css.map */
```

# 最佳化

越少越好，这就是为什么我们在 [clean-css](https://github.com/jakubpawlowicz/clean-css) 的帮助下优化我们的 CSS 库。

```
npm i clean-css-cli -D
```

通过将最后一个脚本添加到我们的链中，我们能够创建一个缩小版的 CSS 文件。

```
"scripts": {
  "build": "rimraf css && npm run build-sass && npm run build-autoprefix && npm run build-cleancss",
  ...
  "build-cleancss": "cleancss -o ./css/index.min.css ./css/index.css"
}
```

如果我们最后一次运行`npm run build`，我们现在应该在输出文件夹`css`中发现输入 CSS 的缩小版本。

```
:-webkit-full-screen #main p{background:#3880ff}:-ms-fullscreen #main p{background:#3880ff}:fullscreen #main p{background:#3880ff}
```

# 总共

概括来说，这里的`package.json`包含了创建我们自己的定制 CSS 库的所有依赖项和构建步骤。

```
{
  "name": "bundle-css",
  "version": "1.0.0",
  "main": "index.scss",
  "scripts": {
    "build": "rimraf css && npm run build-sass && npm run build-autoprefix && npm run build-cleancss",
    "build-sass": "node-sass --output-style expanded src/index.scss ./css/index.css",
    "build-autoprefix": "postcss --use autoprefixer --map --output ./css/index.css ./css/index.css",
    "build-cleancss": "cleancss -o ./css/index.min.css ./css/index.css"
  },
  "files": [
    "css"
  ],
  "author": "David",
  "license": "MIT",
  "devDependencies": {
    "autoprefixer": "^9.8.4",
    "clean-css-cli": "^4.3.0",
    "node-sass": "^4.14.1",
    "postcss-cli": "^7.1.1",
    "rimraf": "^3.0.2"
  }
}
```

# 摘要

多亏了许多开源项目，我们可以快速轻松地为自定义 CSS 创建一个库，这太棒了。

在您的下一次演讲中尝试一下 [DeckDeckGo](https://deckdeckgo.com) ，如果您准备对我们常见的 [deck](https://github.com/deckgo/deckdeckgo/tree/master/utils/deck) 风格做出一些改进，我们将非常欢迎您的帮助😃。

到无限和更远的地方！大卫