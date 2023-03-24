# 升级到 Gatsby 3.0

> 原文：<https://levelup.gitconnected.com/upgrading-to-gatsby-3-0-864773decf1b>

![](img/80520b2578a32953aaa09c53cee594aa.png)

来自 unspash，由 Lucas Benjamin 绘制

来自我的[博客](https://fek.io/blog/upgrading-to-gatsby-3-0)

我把我的网站升级到了最新版本的[盖茨比 JS](https://www.gatsbyjs.com) 。如果你不熟悉 [Gatsby](https://www.gatsbyjs.com) 或 [JAMSTACK](https://jamstack.org/) ，它是一个使用 [React](https://reactjs.org) 框架创建静态网站的框架。上个月发布了盖茨比 3.0，这是自 2018 年 2.0 以来的首次重大升级。

他们在他们的文档中包含了一个 [Gatsby 3.0 迁移指南](https://www.gatsbyjs.com/docs/reference/release-notes/migrating-from-v2-to-v3/)，但是我想涵盖我为了将 mu 站点升级到 3.0 所做的更改。

我做的第一步是为升级创建一个 git 分支，如果升级失败的话，我可以将它销毁。一旦创建了新的分支，我就按照迁移指南中的说明进行操作。第一步是升级节点 package.json 文件中的依赖项。我运行了以下命令；

在我升级了 gatsby 模块之后，我通过运行以下命令来检查哪些依赖项需要升级；

> npm 已过时

此命令将为您提供项目中哪些依赖项需要升级的报告；

**突变变化**

有许多需要代码升级的突破性变化。一个例子是*导航到*功能已经被重命名为仅*导航*。这里有一个例子:

我遇到的一个问题是如何导入 css 样式模块，这是我在他们的指南中没有发现的。在 Gatsby 中编写组件时，您可以只为一个单独的模块编写样式模块。例如，如果你有一个名为 *header.js* 的 header 组件，你可以为这个名为 *header.module.css* 的组件设置一组样式。以前，您可以像这样在模块中导入样式；

现在必须使用` * '通配符导入它；

**Graphql 升级**

Gatsby 早期版本中的 Graphql 不需要导入才能使用 graphql 函数。我没有将它导入我的“gatsby_node.js”文件。为了使用 graphql 函数，我必须添加这个导入；

我还使用了一个额外的 frontmatter 变量来为“cover_image”指定一个额外的变量。这让我可以在每篇文章的标题中指定一个独特的封面图片。我可以通过在“gatsby_node.js”文件中添加以下模式来添加它；

**Node.js 版本需求**

我目前正在使用 [Netlify](http://netlify.com) 来托管我的网站。Netlify 默认为 Node v10，但是 Gatsby 3.0 现在要求您至少拥有不低于 12.13.0 的最低版本 Node.js。您可以使用`. nvmrc '文件在 Netlify 上指定新版本的节点。我创建了一个如下图所示的:

> 14.16.0

**总结**

这次升级并不是完全没有痛苦，但是我能够按照他们的指导在大约一个小时内完成升级。盖茨比增加了很多新东西，你现在可以通过升级来利用它们。我希望这篇文章对你的升级有所帮助。