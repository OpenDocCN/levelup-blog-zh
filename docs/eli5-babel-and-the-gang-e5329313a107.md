# ELI5:巴别和那帮人

> 原文：<https://levelup.gitconnected.com/eli5-babel-and-the-gang-e5329313a107>

## 用简单易懂的术语解释巴别塔(ELI5 像我 5 岁一样解释)

![](img/9b42f1e7b04ac4809f30a292bcae1aba.png)

我撒了谎，我今天不会再说谎了。相反，我会试着和 *ELIN(解释得像我是新来的)*巴别塔。我只希望当我第一次听说巴别塔的时候，有人能和我进行这样的对话。我想永远都不会太晚:)。我们开始吧。

**注意(2018 年 9 月 8 日)**:文章更新反映了巴别塔 v7 的一些变化。

**过去的我(PM)** :阿发克，`create-react-app`使用巴别塔。如果我想继续使用所有前沿 JS 特性，这似乎是一个有趣的工具。我需要什么样的`npm`方案来启动项目？

**现在的我(CM)** :别急先生！首先，我们来说说什么是巴别塔。根据[文件](https://babeljs.io/):

> Babel 是一个工具链，主要用于将 ECMAScript 2015+代码转换为当前和旧版本浏览器或环境中向后兼容的 JavaScript 版本

**Past Me (PM):** 酷，所以我写的任何东西(TypeScript 或者 ES6+)都可以转换成浏览器能理解的 ES5。太棒了，在我开始之前还有什么我需要知道的吗？

**现在的我(CM):** 是的，几件事。Babel，a.k.a. `@babel/core,`本身就是一个外壳，它接受你的代码，然后按原样吐出来。你真正需要的是对你的代码进行处理和转换的`plugins`。

**PM:** 哦，好的。我如何得到`plugins`？肯定有人创建了这些`plugins`的库，对吗？

**CM:** 没错，在`npm`注册表上有很多`plugins`，你可以根据你的项目来安装和使用。`Plugins`的范围从简单的变换如箭头函数的[变换](https://babeljs.io/docs/en/babel-plugin-transform-arrow-functions)到更复杂和精密的`plugins`如模块系统的[变换](http://@babel/plugin-transform-modules-systemjs)。

您需要确定您将需要的`plugins`，并将它们作为`devDependency`导入到您的项目中。

**PM:** 所以作为`devDependency`导入就足以让我的应用程序按预期工作了？

**CM:** 是的。差不多了。您需要进一步告诉`babel`您希望它挑选某些`plugins`并将其应用到您的代码中。最简单也是推荐的方法是使用`.babelrc`文件，可以在项目的根目录下创建，并在其中添加`plugins`数组。

```
{ 
    "plugins": ["@babel/plugin-transform-arrow-functions"] 
}
```

或者，您可以创建并添加您的自定义插件，并选择性地向其传递附加参数。

```
{ 
    "plugins": [
        ["name", {
           "option1": "value1",
           "option2": "value2"
        }]
    ] 
}
```

此外，不要忘记，您需要将 babel 本身包含在您的项目中。如果你使用 webpack，你可以使用 babel-loader，它可以通过规则应用[。](https://github.com/babel/babel-loader#usage)

PM: 尽管这听起来并不容易。我是否必须根据我想要使用的 ES6+或 TypeScript 特性来添加这些`plugins`中的每一个？

**CM:** 绝对不行！所以才有了`presets`。开发者不应该为每个项目都列出插件列表。只需为您的项目使用最合理的`preset`，并根据需要添加额外的`plugins`或`presets`。目前，只有少数`presets`得到官方支持。虽然有[很多可用的插件](https://github.com/babel/babel/tree/master/packages)，但在大多数情况下你不太可能需要它们。

**PM:** 官方有哪些预置？他们支持 React 吗？

他们肯定会这么做，有四个主要的插件:

1.  [@babel/preset-env](https://babeljs.io/docs/en/babel-preset-env) :让你的代码兼容最新版本的 JavaScript(截至目前为 ES2017)。根据需要添加*多孔填料*。它可以接受过多的选择来支持你所有的愿望。此外，需要注意的是，v7.x.x 的多孔填料不支持实验功能。
2.  [@babel/preset-flow](https://babeljs.io/docs/en/babel-preset-flow) :如果你在你的项目中使用 [flow](https://flow.org/) 进行静态类型检查，你可以使用[@ babel/plugin-transform-flow-strip-types](https://babeljs.io/docs/plugins/transform-flow-strip-types/)附带的`preset-flow`预置，它有助于去掉我们在代码中添加的类型检查。
3.  [@babel/preset-react](https://babeljs.io/docs/en/babel-preset-react) :为您提供所有 [react 特定插件](https://babeljs.io/docs/en/babel-preset-react)，最重要的一个是将`JSX`转化为`JS`。
4.  @babel/preset-typescript :使用所有必要的[插件将你的 TS 代码转换成 JS。](https://babeljs.io/docs/en/babel-preset-typescript)

直到 v7.x.x Babel 还为 JS 的实验特性提供支持，这些特性在 TC39 的[标准化的`stages`中提供。总共有 5 个阶段，最后一个阶段是特征的标准化。如果您愿意，在`npm`中有名称为`@babel/preset-stage-STAGE_NUMBER`的`presets`，可以根据需要包含在您的项目中。明智地使用它们。](http://exploringjs.com/es2016-es2017/ch_tc39-process.html#_problem-ecmascript-2015-es6-was-too-large-a-release)

自从 v7.x.x 出现以来，舞台预置已经被弃用，取而代之的是带有`plugin-proposal`前缀的显式插件。这样做是为了解决几个挑战，例如在生产中减少使用阶段 0 实验功能，以及[更多的](https://babeljs.io/blog/2018/07/27/removing-babels-stage-presets)。

**PM:** 爽！最后一个问题——在我的基于 create-react-app 的应用程序中，我看到使用的巴别塔预置是`react-app`。那和`@babel/preset-react`有什么不同？

**CM:** 很棒的问题！如果你看一下`create-react-app`项目使用的所有插件和包的[描述，你会发现它们比相对简单的`@babel/preset-react`预置使用了更多。他们结合了他们认为对任何使用`create-react-app` CLI 构建应用程序的人有用的东西。如果你没有使用 CLI，那么你可以自由使用你认为合适的插件和预置。](https://github.com/facebook/create-react-app/blob/next/packages/babel-preset-react-app/package.json#L19-L34)

**PM:** 牛逼！我想我已经准备好开始使用`babel`了。谢谢！你现在可以回到未来了。

**CM:** 万无一失:d .别忘了仔细阅读[文档](https://babeljs.io/docs/en/usage) n、[FAQ](https://babeljs.io/docs/en/faq)和[了解`babel`的注意事项](https://babeljs.io/docs/en/caveats)。

要深入了解巴别塔的内部运作，请参考[建筑手册](https://github.com/jamiebuilds/babel-handbook/blob/master/translations/en/plugin-handbook.md)。

*如果你喜欢这个博客，一定要为它鼓掌，* [*阅读更多*](https://medium.com/@kashyap.mukkamala) *或者关注我的*[*LinkedIn*](https://www.linkedin.com/in/kashyap-mukkamala/)*。*

[![](img/ff5028ba5a0041d2d76d2a155f00f05e.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/webpack) [## 学习 Webpack -最佳 Webpack 教程(2019) | gitconnected

### 8 大 Webpack 教程。课程由开发者提交并投票，使您能够找到最好的 Webpack…

gitconnected.com](https://gitconnected.com/learn/webpack)