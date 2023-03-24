# 什么是顺风 CSS，为什么你应该使用它

> 原文：<https://levelup.gitconnected.com/what-is-tailwind-css-and-why-you-should-use-it-soumyas-blog-d103f870071a>

![](img/d6e9d0d67b3fbabb2852a2becd9813de.png)

> 最初发布于 [**soumya.dev**](https://soumya.dev/what-is-tailwind-css) 。

Tailwind CSS 是一个实用优先的 CSS 框架，这意味着它有各种各样的实用类来帮助创建布局和快速创建定制设计。

我一直很喜欢 Adam([Tailwind CSS](https://tailwindcss.com/)creator)的 [streams](https://www.youtube.com/playlist?list=PL7CcGwsqRpSMgVc5NxXUpqmGOS9s1YrWF) ，他用 Tailwind CSS 创建了看起来很棒、设计很好的布局。现在我无法想象未来的项目不使用 Tailwind CSS。

我的[博客](https://soumya.dev/blog)是用 Tailwind CSS 搭建的。😉

# 它与 Bootstrap 或布尔玛等有什么不同？？🤔

大多数用户界面框架，如引导，材料用户界面，布尔玛等。拥有预先设计的 UI 组件，如卡片、按钮、导航条、提醒。您使用这些组件，并在这些组件的基础上创建设计。

但是在 Tailwind CSS 中，你不会得到一套预先设计好的组件。你得到了公用事业类。您可以结合这些来创建您的布局和组件。

# 这与其他框架相比有何优势？

1.  如果你使用其他框架，比如说 Bootstrap，你的网站感觉像 Bootstrap-y。这意味着你可以很容易地看出它是使用 Bootstrap 构建的(这当然不是问题)，但它看起来与成千上万使用相同框架构建的其他网站非常相似。这对于 Tailwind CSS 来说不是问题，因为它没有给你预定义的组件来使用。您可以任意组合使用 tailwind 实用程序类来创建自己的 UI 组件。见下文如何做到这一点。
2.  要克服上述问题，您有一个解决方案:**覆盖样式。如果你以前曾经这样做过，你会知道这很快就会失控。这不是顺风 CSS 的问题。**
3.  你解决了计算机科学中最难的事情之一:命名事物，这里是组成类名。如前所述，Tailwind CSS 为您提供了实用程序类，您可以使用它们来创建自己的样式，而无需担心类名称的发明。
4.  **Responsive by design:** 如果使用 Tailwind CSS，就不需要编写自定义样式来处理不同屏幕尺寸的响应。您可以使用 Tailwind 的响应工具轻松处理它。我们将在下面看到。
5.  **伪类:**我们可以在悬停、聚焦等元素上设置样式。类似于我们在顺风 CSS 中的响应式设计。
6.  **还有很多！** —新功能不断增加，如黑暗模式支持、动画等。

# 顺风 CSS 入门

使用 Tailwind CSS 最简单的方法就是抓起 CDN 开始玩。对于严肃的项目，我推荐通过 npm 安装它，我在 CDN 方法下面已经描述过了。

# 使用 CDN:

将 CDN 添加到您的 HTML:

```
<link
  href="<https://unpkg.com/tailwindcss@^1.0/dist/tailwind.min.css>"
  rel="stylesheet"
/>
```

# 通过 npm 安装:

1.  为了使用它，你必须首先通过 npm 安装 Tailwind CSS。

2.创建一个 tailwind 配置文件:这个文件用于根据您的喜好配置和定制 tail wind:`npx tailwind init` 这将在您的项目的根目录下创建`tailwind.config.js`。

3.将 Tailwind CSS 添加到您的 CSS 中:创建一个包含以下内容的 CSS 文件并导入。

4.用 PostCSS 使用 Tailwind:Tailwind CSS 是一个 PostCSS 插件，这意味着你必须在你的项目根目录下创建一个`postcss.config.js`文件:

在生产中，要减小 CSS 包的大小，建议使用清除未使用的 CSS。你可以在这里找到更多信息。

# 使用顺风 CSS

如前所述，Tailwind CSS 为我们提供了实用程序类。

例如，要赋予元素`display: flex`属性，我们只需向元素添加`flex`类。很简单，对吧？

类似地，有许多不同颜色的类，flexbox，CSS 网格，间距，排版，边框样式，过渡等等。

我在下面描述了 Tailwind CSS 提供给我们的一些工具类:

**颜色:**我们可以使用在[默认调色板](https://tailwindcss.com/docs/customizing-colors#default-color-palette)中指定的从 100-900 的不同阴影的红色、蓝色、黄色和其他颜色。我们还可以通过修改`taiwind.config.js`文件来添加或删除调色板中的颜色。

**边距和填充:**使用边距，我们使用`mx-1`、`my-3`、`m-4`分别对左右、上下、四周应用边距。数字 1、3 和 4 遵循顺风的默认间距比例。

**响应:**我们可以很容易地增加我们网站的响应度。假设我们希望一个段落在小屏幕上使用粗体，在大屏幕或更大的屏幕上使用半粗体，我们可以通过下面的 HTML 很容易地做到这一点:

提示:为了非常容易地调试所有屏幕尺寸的响应样式，我使用了[sizy 浏览器](https://link.soumya.dev/sizzy)。

**显示:**用于控制一个元素的`display`属性。例如，要制作一个项目`display:block`，我们给这个元素一个类`block`。同样，我们使用类`flex`、`grid`、`table`等。为元素赋予各自的样式。

还有很多不同的实用程序类我没有介绍，但是你可以在[文档](https://tailwindcss.com/docs/)中找到它们。

让我们通过一些例子来了解更多关于 Tailwind CSS 的知识:

# 示例:

a. **HTML:**

变成了:

[CodePen — Tailwind CSS 示例 1](https://codepen.io/geekysrm/embed/XWKKNKd?default-tab=result&theme-id=dark)

**解释:**

`px-4`和`py-2`分别在水平轴和垂直轴上给出填充。

`font-semibold`将字体粗细 600 应用于按钮文本。

`bg-blue-400`从顺风[帕莱特](https://tailwindcss.com/docs/customizing-colors#default-color-palette)给它一个 400 阴影的蓝色。

最后，`rounded`给出按钮`border-radius: 0.25rem;`。

b. **HTML** (这个例子取自文档本身):

变成了:

[CodePen — Tailwind CSS 示例 2](https://codepen.io/geekysrm/embed/ExyyNgg?default-tab=result&theme-id=dark)

c. **HTML** (博客文章列表[我的博客](https://soumya.dev/blog)):

变成了:

[CodePen — Tailwind CSS 示例 3](https://codepen.io/geekysrm/embed/YzWWpNe?default-tab=result&theme-id=dark)

**提示:**使用 [TailwindCSS cheatsheet](https://nerdcave.com/tailwind-cheat-sheet) 快速浏览顺风类📝。

# 将顺风 CSS 与 React 一起使用:

在您的站点中为可重用组件重复相同的实用程序类是非常痛苦的。要解决这个问题，您可以将常见的样式提取到一个 React 组件(或者您选择的前端框架中的一个组件)中，使用 props 使其动态化并重用它。

**简单卡组件示例:**

*用法:*

*卡组件:*

[这里有一个带 React + Tailwind CSS 的 CodeSandbox](https://codesandbox.io/s/github/arnorhaux/tailwindcss-react-boilerplate?file=/src/components/app.js) 供你玩转！

如果您想将 Tailwind CSS 与 Emotion 或 styled-components 之类的 CSS-in-JS 库一起使用，您可以遵循我的[指南](https://soumya.dev/tailwindcss-gatsby-styled-emotion)。

我希望这些例子能让你理解 Tailwind CSS 的各种属性，以及它与 Bootstrap 等传统 CSS 框架的不同之处。我相信一旦你开始在你的项目中使用 Tailwind CSS，你就会爱上它，就像我一样。

# 订阅 Soumya 的发展思路

订阅我的时事通讯，获取定期文章和发展思路

# 编码快乐！👨‍💻

如果这篇文章对你有帮助，并且你想帮助我创作更多这样的教程/视频，请考虑在[*https://coffee.soumya.dev/*](https://coffee.soumya.dev/)支持我

想要收件箱里有更多这样的帖子吗？在这里订阅我的[时事通讯](https://tinyletter.com/geekysrm)和我的 [YouTube 频道](https://link.soumya.dev/youtube)。