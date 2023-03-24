# 如何提高你的 CSS 学习

> 原文：<https://levelup.gitconnected.com/how-to-boost-your-css-learning-115bd577aeb9>

![](img/3edcdcf35fea38932e20ab4780d2f11c.png)

照片由[多梅尼科·洛亚](https://unsplash.com/@domenicoloia?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

## 这里有一些指导方针，教你如何理解那些奇怪的行为，并最终在 CSS 方面做得更好！

不像很多开发者，我没有 CS 学位。我改学了艺术。我总是有审美天赋。鉴于我的视觉艺术背景，CSS 从一开始就是我相处最多的语言。它提供的即时视觉反应是一种强烈的激励。我开始试验，用脑袋撞它所有怪异的行为，最后，我爱上了它！❤️

我想分享一些我用经验收集的，帮助我熟悉 CSS 的概念和技巧，让你更好地理解这种“不可预测”的语言背后的逻辑。

![](img/3ba1453dadf4e8f4513b3a542f36fdbf.png)

由[克里斯托弗·高尔](https://unsplash.com/@cgower?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 期待意想不到的事情

关于这门语言，我们需要了解的第一件事是，所有的规则都是相互关联的。每一个行动，你都需要期待一个反应。在我们写在文档中的不同规则之间有很强的相互依赖性。通常一种规则的应用排除了另一种规则的应用。最常见的例子是静态定位的元素将忽略 top、right、bottom、left 和 z-index 属性的任何给定值。

```
div {
  position: static; /* default from user agent style sheet */ /* these properties below won't have any effect */
  top: 100px;
  left: 100px;
  z-index: 999999;
}
```

如果我们给出任何不同的值(如相对值、绝对值、固定值),这些值将根据我们选择的值应用于目标。

```
div {
  position: fixed;
  top: 100px;
  left: 100px;
  z-index: 1; /* it's not necessary anymore to give a huge number */ /* now they will be relative to the window */
}
```

同样的问题也涉及到我们在文档中放置 HTML 元素的方式，一些容器的规则会影响它的子容器或兄弟容器。以 display 属性为例，值设置为`block`，一个元素将占据整个水平空间，将所有其他元素推到下面一行。这是`div`标签元素的默认行为，要改变它，你可以传递一个设置为`inline-block`的显式显示属性，这样我们的元素将水平对齐。

```
.box {
  display: inline-block; /* now the elemtens will display next to each other */
}
```

这里有一个笑话完美地描述了这种行为:

> “两个 CSS 属性走进一个酒吧。
> 一个完全不同的酒吧里的高脚凳翻倒了。”
> 
> 托马斯·富克斯。

一旦我们接受了规则和元素之间的这些**关系**，就很容易理解为什么有些事情没有像预期的那样工作。

![](img/e807c0d69052de385e7cf7f65cd67f3f.png)

## 尝试，尝试，再尝试

所以你不知道 CSS 是怎么工作的，就试试吧！
尝试不同规则的**组合**在不同情况下的应用，你可以找出哪个属性影响哪个属性，以及导致特定结果的组合。

当我开始的时候，每当我遇到一个吸引我眼球的很酷的网页，我都会尝试自己复制它的设计。你也应该试着这样做！复制一个网页，一个功能，或者任何你想到的东西。练习是学习所有小技巧和建立自信的最好方法，你会学到无数有趣的特性。

要快速做到这一点，您可以在 [Codepen.io](https://codepen.io/) 上创建一个新的“笔”，或者为自己准备一个带有“ [gulp](https://gulpjs.com/) ”的样板文件，用于服务和查看您的文件(这里有一个[示例](https://github.com/cferdinandi/gulp-boilerplate))，这样您就可以编写代码并立即看到结果。

![](img/c352388e0a6e77e73594deb08ad0b161.png)

由 [Austin Distel](https://unsplash.com/@austindistel?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## CSS 怎么调试？

知道如何调试 CSS 有助于你的学习曲线。确切地知道你的风格将如何被应用会提高你的开发速度，但是在开始时绝对肯定地预测它并不那么容易。通常有些样式不能像预期的那样应用，所以你需要尝试不同的组合来解决问题，你最终会在浏览器和编辑器之间来回切换，浪费很多时间。

幸运的是，这有一个解决方案。浏览器 **DevTools** 允许我们直接在网页上进行修改(试试 Chrome DevTools 的快速[入门](https://developers.google.com/web/tools/chrome-devtools/css))。我们可以轻松地检查应用于任何元素的 CSS 规则，只需关注它，并通过即时的视觉响应以我们喜欢的方式更改它们，这样我们就可以获得我们最喜欢的结果。在这里，你有一个关于 Chrome DevTools 提供的所有特性的[完整指南](https://developers.google.com/web/tools/chrome-devtools/css/reference)。

一旦你对你的风格满意了，你就需要对你的源文件进行同样的修改，但是如果你做了很多修改，你可能会忘记所有的修改。DevTools 提供了一个“ **Changes** ”部分，它将显示您编辑的 CSS 代码行的摘要。Firefox 对这个特性有最好的实现，允许你一次或者一行一行的导出修改。非常得心应手！

![](img/25311a62c372de89c493f27792d9eafe.png)

照片由[jeshoots.com](https://unsplash.com/@jeshoots?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 游戏改变者:Flexbox

起初，我的布局方法是对所有事情都使用 position**absolute**。这是一种将任何元素准确放置在我希望的位置的简单方法。但是当我改变屏幕尺寸时，一切看起来都不对劲。所以大多数时候我需要编写媒体查询来修复它。

当我最终遇到 [**Flex-box**](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) 时，我写 CSS 的方式完全改变了！Flex-box 是一种设计快速灵活布局的简单方法，而且它总是反应灵敏。要初始化它，您只需要将值" **flex** "赋给 display 属性，从容器中，您可以轻松地以您喜欢的方式分配所有的子元素。

```
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  /* common way to center elements inside a container */
}
```

对于更复杂的二维布局， [**网格**](https://css-tricks.com/snippets/css/complete-guide-grid/) 变得相当方便。它给了我们很大的自由度来定位我们的元素，遵循一个行列系统。您可以通过一些简单的规则来决定每个元素应该包含多少行或多少列，并构建基于网格的布局。在下面的示例中，我们将空间划分为 12 个相同大小的列。根据类名，我们可以为布局中的一个元素分配 2、3 或 4 列。

```
.grid-col-12 {
  *grid-template-columns*: repeat(12, minmax(0,1fr));
}.col-span-2 {
 *grid-column*: *span* 2 / *span* 2;
}.col-span-3 {
 *grid-column*: *span* 3 / *span* 3;
}.col-span-4 {
  *grid-column*: *span* 4 / *span* 4;
}
```

这两个布局系统还可以非常有效地协同工作，这样我们就可以构建我们的响应式网页。

## 结论

在我看来，每个开发人员都应该至少具备 CSS 的基本工作知识。在意大利语中，我们说“ *l'occhio vuole la sua parte* ”，意思是项目的视觉效果很重要，它是项目本身的一部分。CSS 是一种令人惊叹的语言，一旦你知道一些小技巧，使用起来也会非常愉快。
所以，不要对 CSS 生气，因为你不知道会发生什么，相反，开始热爱它吧！❤️

如果你现在准备学习 CSS，看看这个[指南](/essential-keys-to-crack-css-6f938bb9d4bf)来破解它！

引用链接列表:

[](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) [## Flexbox | CSS-技巧完全指南

### 我们的 CSS flexbox 布局综合指南。这份完整的指南解释了 flexbox 的一切，重点是所有…

css-tricks.com](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) [](https://css-tricks.com/snippets/css/complete-guide-grid/) [## Grid | CSS-Tricks 完全指南

### 去拿海报！大量参考这个指南？在办公室的墙上钉一份副本。桌面 Chrome Firefox IE Edge Safari 57…

css-tricks.com](https://css-tricks.com/snippets/css/complete-guide-grid/) [](https://github.com/cferdinandi/gulp-boilerplate) [## cferdinandi/gulp-样板文件

### 用 Gulp 构建 web 项目的样板文件。使用 Gulp 4.x。特性 Concatenate、minify 和 lint JavaScript…

github.com](https://github.com/cferdinandi/gulp-boilerplate) [](https://developers.google.com/web/tools/chrome-devtools/css) [## 开始查看和更改 CSS | Chrome DevTools

### 完成这些交互式教程，学习使用 Chrome DevTools 查看和更改页面 CSS 的基础知识…

developers.google.com](https://developers.google.com/web/tools/chrome-devtools/css) [](https://developers.google.com/web/tools/chrome-devtools/css/reference) [## CSS 参考| Chrome DevTools | Google 开发者

### 在这个全面的 Chrome DevTools 功能参考中发现与查看和更改 CSS 相关的新工作流…

developers.google.com](https://developers.google.com/web/tools/chrome-devtools/css/reference) [](https://developer.mozilla.org/en-US/docs/Tools/Page_Inspector/How_to/Examine_and_edit_CSS) [## 检查和编辑 CSS

### 您可以在检查器的“CSS”面板中检查和编辑 CSS。规则视图列出了适用于所选…的所有规则

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Tools/Page_Inspector/How_to/Examine_and_edit_CSS)