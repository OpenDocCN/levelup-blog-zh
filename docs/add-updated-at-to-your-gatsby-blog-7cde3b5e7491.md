# 将“更新于”添加到您的盖茨比博客中

> 原文：<https://levelup.gitconnected.com/add-updated-at-to-your-gatsby-blog-7cde3b5e7491>

![](img/d7246a08127993af7a73481bbe4994e2.png)

*照片由* [*吉列尔莫·Á·阿尔瓦雷斯*](https://unsplash.com/@guillermoalvarez?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)*[*Unsplash*](https://unsplash.com/s/photos/updated-at?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)*

*建立网站声誉的一个很好的建议是保持你的内容更新。许多网站，尤其是博客，经常这样做。我也时不时地这样做，但我不会向我的读者展示这些信息。*

*同样重要的是，我不能显示我的博客文章在搜索引擎上的更新程度。如果你想让搜索引擎尽可能在搜索结果的顶部显示你的博客文章，显示文章更新的时间会很有用。因此，你不仅要告知用户文章的相关性，还要提高文章的搜索引擎优化。*

*![](img/82499f481b3a4bda913509737b758321.png)*

*如果你写的是经常变化的主题(JavaScript *khm-khm*)，你可能想保持那些帖子的新鲜感。当然，有一些永恒的作品并没有从显示更新时间中受益。如果你觉得你有这样的帖子，也许最好不要显示修改时间。*

*很长时间以来，我一直想在我的博客文章中显示“更新于”,最终我做到了。莫妮卡·伦特最近一期的时事通讯给了我灵感，她提到了如何快速做到这一点，但没有太多具体的细节。*

*请继续关注，因为我们将通过几个解决方案来使用 Gatsby 显示您的博客帖子的最后修改或更新日期。*

# *显而易见的(手动)解决方案*

*一个简单的解决方案是在你的前面添加一个字段，如下所示:*

```
*---
title: Top 5 JavaScript Libraries in 2021 
published: 2021-01-04 
updated: 2021-02-09 
--- Hey everyone, let us go through some of the top 5 JS libraries this year.*
```

*如果你注意到，我们有两个日期字段。一个字段是 published，它告诉我们帖子是何时发布的。然后，我们有了更新字段，它将显示帖子更新或修改的时间。我将该字段命名为 updated，但您可以释放您内心的创造力，想出一个更适合您的名称。*

*使用这种手动方法既愉快又简单，但是它有一个缺点。每次编辑博文都要记得更新，这就给出错留下了空间。*

*如果我们能以某种方式使整个过程自动化，那就更好了。幸运的是，我们正朝那个方向前进，系好安全带。*

# *不太明显的(自动化)解决方案*

*如果我们想摆脱每次编辑博客文章时不断更新前台日期字段的痛苦，我们可以使用 Git。幸运的是，Git 记录了日期、时间以及每次提交时修改了哪些文件。Git 中的所有这些信息对我们来说就像音乐一样，因为这正是我们所需要的。*

*但是，我们如何将这些信息“拉入”到盖茨比中呢？我们需要修改`gatsby-node.js`并动态添加一个新字段。如果你是一个初学者，或者你有点害怕接触`gatsby-node.js`，我建议你看看我的博客文章“[从头开始建立盖茨比博客](https://pragmaticpineapple.com/setting-up-gatsby-blog-from-scratch)”。在那里，我们与`gatsby-node.js`一起深入动态地做事情。或者你可以坚持到博文的结尾，在那里我们展示了一个更简单的解决方案。*

*为了生成一个新的字段，从 Git 中提取上次修改时间，我们必须将下面的代码添加到我们的`gatsby-node.js`中:*

```
*const { execSync } = require("child_process")

exports.onCreateNode = ({ node, actions }) => {
  // ...

  if (node.internal.type === "MarkdownRemark") {
    const gitAuthorTime = execSync(
      `git log -1 --pretty=format:%aI ${node.fileAbsolutePath}`
    ).toString()
    actions.createNodeField({
      node,
      name: "gitAuthorTime",
      value: gitAuthorTime,
    })
  }

  // ...
}*
```

*我们在这里做的是告诉 Gatsby 在创建节点时将`gitAuthorTime`字段添加到节点中。我们使用`execSync`来执行一个返回作者日期的`git log`命令。Git 命令并没有看起来那么复杂，所以让我们来分解一下:*

*   *`git log`返回提交日志*
*   *`git log -1`返回最新的提交日志*
*   *`git log -1 --pretty=format:%aI`以严格的 ISO 8601 格式返回最新提交[作者日期](https://git-scm.com/docs/pretty-formats#Documentation/pretty-formats.txt-emaIem)。在 [its 文档](https://git-scm.com/docs/pretty-formats)中有一堆选项*
*   *`git log -1 --pretty=format:%aI ${node.fileAbsolutePath}`返回上面提到的所有内容，但针对一个特定的文件。*

*太棒了，现在我们已经向节点添加了一个`gitAuthorTime`字段，我们可以在我们的博客文章模板中简单地查询它:*

```
*query($slug: String!) {
  markdownRemark(fields: { slug: { eq: $slug } }) {
    # ...
    fields {
      gitAuthorTime
    }
    # ...
  }
}*
```

*稍后我们可以在我们的道具中访问它，就像这样:*

```
*import React from "react"

const BlogPost = (props) => {
  const { gitAuthorTime } = props.data.markdownRemark.fields

  render(<p>Updated at: ${gitAuthorTime}</p>)
}

export default BlogPost*
```

*酷，但是如果你不想配置`gastby-node.js`呢？别再找了，有一个 Gatsby 插件，你猜对了。*

# *简单(自动化)的解决方案*

*有一个[Gatsby-transformer-info](https://www.gatsbyjs.com/plugins/gatsby-transformer-gitinfo)插件可以为我们从 Git 获取信息。使用插件会对我们有所帮助，所以我们不必在`gatsby-node.js`中编写和维护自定义解决方案。*

*安装插件并运行 Gatsby 服务器后，`File`节点上会出现几个新字段。这种方法有一个问题，我们查询的是`markdownRemark`，而不是 GraphQL 查询博客文章中的`file`。*

*幸运的是，这不是一个大问题，因为`File`是`MarkdownRemark`节点的父节点。这意味着我们可以从插件中提取这些新字段，如下所示:*

```
*query($slug: String!) {
  markdownRemark(fields: { slug: { eq: $slug } }) {
    # ...
    parent {
      ... on File {
        fields {
          gitLogLatestDate
        }
      }
    }
    # ...
  }
}*
```

*如果你感到困惑，不要担心，我也是。我们在这里使用了 GraphQL 中的一个[内联片段](https://graphql.org/learn/queries/#inline-fragments)。一个`MarkdownRemark`节点的父节点可以是一个`File`，所以我们做了`... on File`，这样我们就可以访问`File`的字段。它不像前面的例子那样简洁，我们直接将字段添加到`MarkdownRemark`中，但是它仍然是好的。*

*然后我们可以像这样在我们的道具中得到`gitLogLatestDate`:*

```
*import React from "react"

const BlogPost = (props) => {
  const { gitLogLatestDate } = props.data.markdownRemark.parent.fields

  render(<p>Updated at: ${gitLogLatestDate}</p>)
}

export default BlogPost*
```

# *关闭*

*希望你成功地设置了这篇博文之后的修改/更新时间。我计划很快发布另一篇相关的博文，解释如何进一步提高你博客的 SEO。如果你对这样的内容感兴趣，可以考虑订阅[时事通讯](https://pragmaticpineapple.com/newsletter)。*

*此外，请在下面的 Twitter 上与您的朋友和同事分享:*

*直到下一个，干杯。*

**原载于 2021 年 2 月 8 日*[*https://pragmaticpineapple.com*](https://pragmaticpineapple.com/add-updated-at-to-your-gatsby-blog/)*。**