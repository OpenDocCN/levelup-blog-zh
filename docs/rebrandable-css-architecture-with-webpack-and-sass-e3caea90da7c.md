# 具有 Webpack 和 SASS 的可重构 CSS 架构

> 原文：<https://levelup.gitconnected.com/rebrandable-css-architecture-with-webpack-and-sass-e3caea90da7c>

## 创建多品牌风格系统

![](img/ba3e7d7a38b9106b4b9d1d95a27ceb80.png)

所有程序员都讨厌重复自己。即使我们的代码有变化，我们也想尽可能地重用这些东西。

> 好的程序员知道写什么。伟大的人知道重写(和重用)什么

当创建一个有多个品牌的网站或平台时，它们看起来很可能是相似的。风格会在颜色、间距、字体等方面有所不同，但我们设计的框架可以保持不变。如果是这种情况，我们可以通过最少的返工将现有产品重新命名为新产品。

## 我们将要讨论的内容

1.  品牌结构
2.  Webpack 安装
3.  后处理

# 品牌结构

让我们从一个简单的文件夹结构开始。我们将 src 文件夹分成子文件夹，每个子文件夹对应一个品牌。每个品牌都有自己的索引文件。为了简单起见，我们假设我们正在使用 SASS-loader，并在 *index.js* 文件中导入我们的 *index.scss* 文件。

```
src
|---general
|   |---index.scss
|   |---index.js
|
|---brand-1
|   |---index.scss
|   |---index.js
|
|---brand-2
    |---index.scss
    |---index.js
```

接下来，我们在常规模块中创建了一个基本的卡片设计，我们可以对其进行品牌重塑。CSS 的整个思想是级联的，这允许我们通过在更具体的层次上定义属性来覆盖它们。这带来了被覆盖的冗余 CSS 规则和可能的特殊性冲突的问题。然而，使用 SASS，我们可以定义变量。当我们覆盖这些变量时，旧值会在构建过程中被删除。真好。

## 一般风格

让我们为通用卡定义一些基本变量和基本样式。

src/general/_variables.scss

src/general/_card.scss

## 品牌风格

现在，在我们的两个品牌中，我们希望在卡片中重复使用这个代码。因此，首先我们将创建我们的品牌特定的 *_variables.scss* 文件，并覆盖我们想要更改的值。

src/brand-1/_variables.scss

然后将它和一般的卡片样式一起导入到我们的索引文件中。

src/brand-1/index.scss

编译完成后，生成的样式表将具有通用的 card 样式，但是对于我们覆盖的变量，这些变量将是特定于品牌的。定制可以不仅仅是变量，但是为了简单起见，我将自己限制于此。

现在，不同于每个品牌在运行时为推翻通用规则而争斗，这是在构建时决定的。

# Webpack 安装

接下来，我们来看看 Wepback 是如何配置的。这里的关键是，我们希望每个品牌有一个单独的捆绑包，而不必组合捆绑包来重用样式。

我们使用多个入口文件，以便 Webpack 将我们的代码分割成单独的包。我们使用[minicsextractplugin](https://webpack.js.org/plugins/mini-css-extract-plugin/)从 JavaScript 包中提取 CSS 文件。

这将在 *dist* 目录中为我们列表中的每个品牌创建一个品牌文件夹。品牌文件夹将包含带有更名 CSS 的样式表，重用通用源代码。

# 后处理

现在，我们可以为每个品牌单独生成一个包，我们可能希望进行一些后处理或后构建步骤。考虑到性能，并且知道 Webpack 在观察变化时会为所有品牌创建新的输出文件，我们希望将自己限制在已经变化的块上。

在另一篇文章中，我写了如何过滤掉已经更新的块，你可以在这里查看。使用[ProcessUpdatedChunksPlugin](https://www.npmjs.com/package/process-updated-chunks)，我们可以定义我们想要对已经更新的代码块做什么。

希望这能帮助你在项目中做一些决定，感谢阅读。