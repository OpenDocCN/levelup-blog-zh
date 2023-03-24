# 通过建立自己的博客网站来学习盖茨比

> 原文：<https://levelup.gitconnected.com/learn-gatsby-by-building-your-own-blog-website-ff5aaa8e90b8>

## 静态渲染方法介绍。

![](img/76598a4cf2ed8ddf1a5947964c8bbb0a.png)

尼克·莫里森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

*我什么时候应该用盖茨比做我的网站？比起 Create React App，使用 Gatsby 有什么好处？如果我想建立自己的博客网站，从哪里开始？*在接下来的几分钟内，你将找到这些问题的答案。

Gatsby 是一个基于 React 和 GraphQL 的伟大框架，它帮助我们构建高速网站。我将演示如何建立一个基本的博客网站，并教授框架的原则。

该项目将创建一个参考我的—[miroslavpillar.com](http://miroslavpillar.com)。相信我，你也能做到！

**注:** *为了能跟得上这个故事，不迷路，你至少要有一点 Javascript 的基础知识和 React。*

# 静态渲染简介

基于盖茨比的网站以速度著称，因为盖茨比采用的是静态渲染。这是一种渲染方式，包括在构建时为站点的每个 URL 生成 HTML。

静态呈现的优点是能够快速响应请求，因为不需要动态生成请求。

一般来说，这意味着提前为每个 URL 生成一个单独的 HTML 文件，这样你就可以将世界各地的文件存储在一个 CDN 上，这让你的网站的响应速度快得离谱。

然而，静态渲染，以及盖茨比，也可以有其缺点。

# 考虑项目中的静态渲染

由于上面提到的原因，当构建非动态(静态)网站时，Gatsby 是一个很好的选择，在这种网站中，视图不会经常改变，并且您可以预测所有可能的请求。这可能是博客网站、文档、新闻等。

使用 Gatsby 的很好的例子来自我们熟悉的公司，例如:

*   【React 官方网站，
*   [博朗电子商务](https://ca.braun.com/en-ca)或
*   [Airbnb 工程&数据科学](https://airbnb.io/)。

## 静态渲染的其他替代方案

如果你有很多用户生成的内容，或者你不能预测所有可能的请求或者每个请求的响应变化，你应该考虑其他使用*服务器端呈现(SSR)* 的框架，比如 [Next.js](https://nextjs.org/) 。

SSR 和静态呈现的主要区别在于，SSR 是按需发生的，因为用户请求每个文件。

然而，如果出于 SEO 的目的需要为每个页面提供特定的 HTML，静态呈现和 SSR 是很好的选择。建立社交网络或电子商务网站就是很好的例子。

另一方面，如果你正在构建的东西与 SEO 无关，网站应该是动态的，那么最好的选择是最熟悉的*客户端渲染(CSR)* ，例如[Create React App](https://create-react-app.dev/) 。

# 建立 Gatsby 启动包

现在，当你知道盖茨比是如何工作的，我们就可以开始真正的工作了。为了在终端中使用 Gatsby 的命令，我们首先需要安装 Gatsby CLI:

```
npm install -g gatsby-cli
```

为了生成新的 Gatsby starter，我们可以编写以下命令:

```
gatsby new gatsby-blog
```

安装完所有文件后，可以用`cd gatsby-blog`进入目录，开始探索整个包。与 Create React App 不同，Gatsby 使用了几个特定的命令和文件，您应该很熟悉。

例如，要在开发者服务器上本地运行项目，您可以使用`gatsby develop`,默认情况下它将在端口 8000 上运行。

**注意:** *请记住，当代码在开发环境中正常运行但在构建过程中抛出异常时，可能会出现差异，例如“* [*窗口未定义*](https://www.gatsbyjs.org/docs/debugging-html-builds/) *。”因此，最好的预防措施是在部署到生产环境之前构建代码。*

您可以通过以下命令构建和模拟生产环境:

```
gatsby build && gatsby serve
```

Gatsby 在端口 9000 上启动一个本地 HTML 服务器来测试您构建的站点。

## 盖茨比和 GraphQL

Gatsby 使用 GraphQL 来管理数据。它是一种查询语言，可以替代 REST API。主要区别在于，使用 GraphQL，您可以获得所有可用的数据，并且可以使用查询选择您需要的数据。我不会深入解释，但是你可以在他们的网站上阅读 GraphQL 是如何工作的。

在 Gatsby 中，您可以通过访问以下地址来访问 GraphQL 并生成查询:

```
http://localhost:8000/__graphql
```

# 探索包

让我们多关注一下包中包含的特定文件。在开发过程中，你可能只会接触到`src`文件夹中的内容:

*   `components`文件夹用于存储单独的 React 组件。`layout.js`是树形结构中最上层的组件。`seo.js`用于在项目中设置 SEO 配置。
*   您可以将所有图像保存在`images`文件夹中，这样它们将得到静态服务。
*   在`pages`中保存了所有的网页模板。`index.js`是用户访问您的网站时显示的第一页。
*   `gatsby-browser.js`、`gatsby-config.js`、`gatsby-node.js`、`gatsby-ssr.js`在《盖茨比》中都有具体的用途，下面就一一描述。

## 盖茨比浏览器. js

该文件允许您响应浏览器中的操作，并将您的站点包装在附加组件中。 [Gatsby 浏览器 API](https://www.gatsbyjs.org/docs/browser-apis) 为您提供了许多与 Gatsby 客户端交互的选项。

例如，如果您使用一个服务工作器来缓存数据，您可以实现`onServiceWorkerUpdateReady`函数来识别出一个新版本并更新内容。

## 盖茨比-配置. js

这个文件定义了你的站点的元数据、插件和其他常规配置。您可以定制许多[站点配置选项](https://www.gatsbyjs.org/docs/gatsby-config)。在大多数情况下，如果您在项目中安装了一个新的 Gatsby 插件，您将会打开这个文件。总是需要将所有插件放在`plugins` 数组中。

最重要的插件之一是`gatsby-source-filesystem`，它让我们可以访问我们网站的 graphQL 查询中的所有查询。但在这个故事的后面会有更多的内容。

## 盖茨比节点

该文件中的代码在构建站点的过程中运行一次。您可以使用它动态地创建页面，在 GraphQL 中添加节点，或者在构建生命周期中响应事件。

## 盖茨比-ssr.js

文件`gatsby-ssr.js`允许您在 Gatsby 和 Node.js 进行服务器端渲染(SSR)时改变静态 HTML 文件的内容。

# 初始配置

首先，我们需要更新我们的文件夹结构。在`src`中，让我们创建两个新文件夹:

*   `markdown-posts`，我们将在其中存储所有未来用 [Markdown 语言](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)写的帖子。
*   我们可以在这里为每篇文章存储模板，因为你肯定不想每次都创建一个新的 React 组件。

出于测试目的，我们还需要创建一些帖子。在我的例子中，我在`markdown-posts`文件夹中创建了 2 个新文件。你可以用[这个生成器](https://jaspervdj.be/lorem-markdownum/)生成测试模板。

在这些文件中，我还添加了 frontmatter 数据`title`、`date`、`description`，类似于每个帖子的元数据。我们以后会需要它们。

现在，为了生成每个帖子的网页，我们需要将这些降价文件转换成 HTML。为此，我们可以安装插件`gatsby-transformer-remark`:

```
npm install gatsby-transformer-remark
```

正如我之前所说，你安装的每个插件都需要添加到`gatsby-config.js`中。你可以这样做:

```
// gatsby-config.js
...
{
    resolve: `gatsby-source-filesystem`,
    options: {
        name: `images`,
        path: `${__dirname}/src/images`,
    },
},
**`gatsby-transformer-remark`,**
`gatsby-transformer-sharp`,
...
```

现在我们准备将 Markdown 文件转换成 HTML，但是我们还不能访问 GraphQL 中的 posts 数据。所以在我们的`gatsby-config.js`中，我们需要添加这些行:

```
**{
    resolve: `gatsby-source-filesystem`,
    options: {
        name: `markdowns`,
        path: `${__dirname}/src/markdown-posts`,
    },
},**
`gatsby-transformer-remark`,
...
```

# 索引. js

这个组件是第一页，当用户访问网站时会显示。在我们的例子中，这里我们想要显示我们所有帖子的列表，在点击特定的帖子后，它将重定向到完整的内容。

您可以删除默认`index.js`的全部内容，并用以下代码替换它:

Index.js 组件

这里有几个你应该注意的亮点:

*   因为 Gatsby 使用的是 GraphQL，所以我们用特定的语法(第 27–43 行)提取所需的 posts(节点)数据。在`allMarkdownRemark.edges`中可以访问节点，我们从每个节点获取所有需要的数据。之后，我们可以使用 props `data`访问这些数据。
*   您可以在`http://localhost:8000/__graphql`中生成适当的结构
*   变量`posts`是我们的节点数组，我们映射并显示在视图中。
*   每个帖子都被包裹在`Link`中，这是我们从 gatsby 进口的。它有 prop `to={node.fields.slug}`，基本上就是我们节点的 URL。我们稍后将回到这一点。

现在，我们唯一缺少的是每个节点的页面。

# 创建 URL

首先，我们需要能够生成每个节点的 URL。在《盖茨比》中，它被称为鼻涕虫。我们可以通过在`gatsby-node.js`中编写一个函数来为每个节点创建一个 slug。

从降价文件生成节点的函数

如果创建了一个新节点，它将检查是否存在`MarkdownRemark`文件，以便创建一个新的 URL。这个 URL 作为`slug`存储在 GraphQL 库中的`fields`属性下，所以我们可以访问它。你可以在 Gatsby 的[节点 API 文档中读到这个函数。](https://www.gatsbyjs.org/docs/node-apis/#onCreateNode)

# 生成页面

既然我们正在生成 URL，我们也应该生成单独的 HTML 页面。在`gatsby-node.js`中，让我们用一个函数`createPages`来实现这个目的:

用于生成每个节点的页面的功能

下面是对正在发生的事情的简要描述:

*   `createPages`是节点 API 中的另一个[函数，它在节点生成和 GraphQL 模式创建完成后立即创建页面。](https://www.gatsbyjs.org/docs/node-apis/#createPages)
*   在变量`blogPostTemplate`中，我们存储了博客模板`blog-post.js`的绝对路径，这个模板我们还没有创建。
*   在`posts`中，我们用一个属性定义了一个对象数组——slug。我们将从 GraphQL 异步获取它。
*   我们通过`posts`映射并调用动作`createPage`，动作有参数`path`、`component`和`context`。可以阅读 `[createPage](https://www.gatsbyjs.org/docs/actions/#createPage)`的[完整文档。](https://www.gatsbyjs.org/docs/actions/#createPage)

# 为每个节点创建一个模板

我保证——这是拥有一个功能性博客网站的最后一步。

我们需要有一个模板为我们的职位，它具有相同的结构和风格，它只会在内容上有所不同。因此，让我们在`templates`文件夹中创建一个新的 React 组件`blog-post.js`。

所有帖子的模板

*   在这个组件中，我们用`markdownRemark`获取节点，包括用 slug 获取`fields`。
*   此外，我们需要一个完整的 HTML 内容，这在`html`查询中是可用的。
*   这个组件有两个道具:GraphQL 的`data`和`pageContext`，从这两个道具我们可以访问 slug。
*   该组件返回通常的 JSX 内容——您可以根据自己的喜好对结构进行编码。
*   每个帖子的全部内容都在这里实现:

```
<div dangerouslySetInnerHTML={{ __html: post.html }} />
```

虽然在 React 中不建议使用这种技术，但在 Gatsby 中是安全的，因为内容是静态提供的。

我们的模板并不漂亮，但这不是这个故事的主题。在 Gatsby 中，您可以安装所有熟悉的样式库，如样式组件、材质 UI、引导程序等。你可以随意定制。选项是无限的。

你可以在这里查看完整的 GitHub 回购协议。

# 摘要

就是这样！

您拥有了一个功能齐全的博客网站，可以在每个新节点上生成 URL 和页面。当然，你可以从很多方面改进它。

但是现在你知道盖茨比是怎么工作的了。在非动态网站中，这个框架是一个很好的选择，在这种网站中，视图不会经常改变，并且您可以预测所有可能的请求。 [React 官方文档](https://reactjs.org/docs/getting-started.html)就是一个贴切的例子。

请记住，在设置项目时，静态渲染不是唯一的选择。这完全取决于项目的目的、目标、规模和开发团队。

感谢阅读！