# Vue.js 入门

> 原文：<https://levelup.gitconnected.com/getting-started-with-vue-js-fe0d2ac38d07>

![](img/fa33ce9c6342a43baa79f13c6c6c5967.png)

Vue.js 徽标

如今，为网络设计东西要求网站具有交互性和快速反应能力。有很多研究表明，如果一个网站的加载和更新时间太长，用户很可能会离开。这就是前端软件开发的用武之地。让网站快速响应的最流行的编码语言是 JavaScript。因为 JavaScript 已经变得如此流行，现在有大量的框架可以让你的网站变得易于交互。可以说最流行的是 React.js，这也是我在训练营中被教导使用的。但是我决定再学一门，至少看看自学如何使用一个框架是什么感觉。所以我决定调查一下 [Vue.js](https://vuejs.org/) 。

我听说的一件事是，因为我知道 React，学习另一个框架并不困难，因为它们都有相似的概念。就我所见，那基本上是真的。React 和 Vue 都有办法跟踪影响网页显示内容的某种“状态”。使用这种状态，React 和 Vue 都有办法向 html 中添加内容，因此您可以循环遍历列表，并让列表中的每个内容的 html 元素显示在页面上。他们都有办法根据条件让某样东西只出现在网页上。它们都有办法让某些元素对您指定的事件做出反应。

与 React 相比，将 Vue 添加到项目中非常容易，您只需要将 Vue 在其指南中提供的脚本添加到您的 HTML 文件中:

```
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
```

另一个比较是，在 React 中，所有东西都是用 JSX 编写的，这是 JavaScript 的语法扩展，是为了与 React 一起使用而创建的。我个人喜欢 JSX，但对一些人来说可能很难掌握，所以 Vue 在这方面也更适合初学者。Vue 没有自己的语法，只是用 HTML 和 JavaScript。

我通过他们网站上的[指南](https://vuejs.org/v2/guide/)自学了 Vue。类似于 React 的 create-react-app，他们有办法用 [vue-cli](https://cli.vuejs.org/) 为一个完整的项目搭建你需要的所有结构的新项目。但是从学习 Vue 开始，我决定用一个非常基本的简单结构的项目来测试所有的概念。它只有三个文件:*index.html*、 *index.js* 和*index . CSS*(CSS 文件不是必需的，我只是添加了它，这样我就可以设计一些东西了)。index.html 的*文件看起来像这样:*

```
<html>
  <head>
    <link rel="stylesheet" href="index.css">
    **<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js">
    </script>**
  </head>
  <body>
    . . .    
  <script src="index.js"></script>
  </body>
</html>
```

你也可以将所有的 JavaScript 放在 HTML 文件中`body`标签底部的`script`标签中，而不是链接到 *index.js* 文件中，但是我认为这样会使 HTML 文件变得冗长和复杂，所以我更喜欢将所有的 JavaScript 放在一个单独的 *index.js* 文件中。要在我的浏览器中看到 index.html 的文件，我只需在我的终端中运行这个命令:

```
// ♥ > open index.html
```

开始使用 Vue 需要的主要东西是 Vue 实例。一个基本的例子，用经典的“你好，世界！”，会是这样的:

```
var hello = new Vue({
  el: '#hello',
  data: {
    message: 'Hello, World!'
  }
})
```

Vue 实例的`data`部分是一个对象，其中包含您想要使用的数据的键和值，Vue 会在它发生变化时跟踪它。在 Vue 实例中，您可以直接从实例访问数据对象中的任何内容，如下所示:

```
hello.message  // => *"Hello, World!"*
```

您使用`el`将实例附加到一个 HTML 元素，在本例中是一个 id 为*hello*的元素。这样，在 HTML 元素中你可以直接使用`message`来引用它:

```
<div id="hello">
  {{ message }}
</div>
```

双括号是我们将数据从 Vue 实例链接到 DOM 的方式，因此如果您打开控制台并将`hello.message`更改为等于其他值，您将立即看到页面更新为包含新值。

但是仅仅能够在屏幕上显示来自 Vue 实例的一些数据，并且仅仅能够通过控制台改变它并不是很有趣。当你开始添加*指令*和添加函数以便你可以与屏幕上出现的数据交互时，Vue 变得更加有用。在我的下一篇博文中，我将深入介绍什么是 Vue 指令的基础知识，Vue 中可用的一些指令，以及如何使用它们。