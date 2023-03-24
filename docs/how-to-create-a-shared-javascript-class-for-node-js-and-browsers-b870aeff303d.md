# 如何为 Node.js 和浏览器创建一个共享的 JavaScript 类

> 原文：<https://levelup.gitconnected.com/how-to-create-a-shared-javascript-class-for-node-js-and-browsers-b870aeff303d>

## 使用 ES6 模块在前端和后端之间共享代码

![](img/9e208e969d58043fd555b1c341cd9cf9.png)

由 [Ferenc Almasi](https://unsplash.com/@flowforfrank?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

自从几年前我开始在一些后端应用程序中使用 Node.js 以来，我一直在努力在节点和浏览器环境之间共享代码。使代码适应任一环境所需的更改非常小，这经常导致我拥有同一个文件的两个几乎相同的版本的情况。

几周前，我正在计划一个新项目，如果没有 Node 和浏览器之间的代码共享，这几乎是不可能的。因此，我决定再次调查，我终于能够找到一个解决这个问题的好办法。

# 共享的 JavaScript 类

让我们从创建一个非常简单的共享类开始:

该类看起来与可以在节点环境或浏览器中使用的普通 JavaScript 类非常相似。唯一重要的细节是定义前面的关键字`export`。创建 JavaScript 类时，也可以使用较新的`[class](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)`语法，但我习惯使用原型。

# 使用 Node.js 中的类

现在让我们看看如何在节点环境中使用共享类:

同样，这看起来像普通的 Node.js 代码。导入类，创建一个对象，并调用`print()`函数。

这里值得注意的一点是`SharedClass.mjs`和`SharedClassNode.mjs`文件的`.mjs`扩展名。这表明文件是 ES6 模块。如果您不想将文件命名为`.mjs`，您也可以将`“type”: “module”`添加到您的`package.json`文件中。

# 在浏览器中使用类

在浏览器 JavaScript 中使用我们的共享类也很简单，我们只需要担心一个细节。当导入 JavaScript 文件时，我们需要将`type=”module”`添加到脚本标签中，以便导入和导出语句在浏览器中可用。然而，这伴随着一些警告。当我们想要使用共享类时，我们只能在其他`type=”module”`脚本中这样做，并且我们需要像在节点环境中那样显式地导入它。另外，`type=”module”`脚本中的代码有自己的作用域，它不会被添加到全局作用域中。

随着 JavaScript 在前端和后端开发中越来越受欢迎，能够在不同环境之间共享代码变得非常重要。在这个问题上挣扎了很多年后，我终于深入研究了一下，并找到了一个好的解决办法。我还没有在一个更大的项目中使用它，但我相信它将消除对同一个文件的多个版本的需要，并在未来使事情变得容易得多。

# 资源

*   [JavaScript 模块](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules)

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)