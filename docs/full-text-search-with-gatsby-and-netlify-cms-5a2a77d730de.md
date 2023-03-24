# 使用 Gatsby 和 Netlify CMS 进行全文搜索

> 原文：<https://levelup.gitconnected.com/full-text-search-with-gatsby-and-netlify-cms-5a2a77d730de>

![](img/9f5d0970541967dd24a33d21d89520ff.png)

全文搜索是指在全文数据库中搜索单个文档或集合的技术。全文搜索不同于基于元数据或数据库中表示的部分原始文本(如标题、摘要、选定部分或参考书目)的搜索。

与使用其他方法相比，全文搜索允许用户检索更高质量的结果。在全文搜索过程中，搜索引擎在尝试匹配搜索条件时会检查每个存储文档中的所有单词。

实现对 Gatsby 的全文搜索很容易设置。我尝试过不同的插件，但决定使用 Flexsearch(最容易实现和设置)。

# 先决条件

在这个例子中，我展示了一个 Gatsby 网站的实现，它使用 Netlify CMS 来管理内容。使用其他 CMS 时，实现可能会有所不同。

首先，我们需要安装`gatsby-plugin-flexsearch`

```
$ yarn add gatsby-plugin-flexsearch
```

之后，我们必须初始化`gatsby-config`中的插件，并配置我们想要包含的属性。

```
{
      resolve: 'gatsby-plugin-flexsearch',
      options: {
        languages: ['en'],
        type: 'MarkdownRemark',
        fields: [
          {
            name: 'id',
            indexed: false,
            resolver: 'id',
            store: true,
          },
          {
            name: 'html',
            indexed: true,
            resolver: 'internal.content',
            attributes: {
              encode: 'balance',
              tokenize: 'strict',
              threshold: 0,
              resolution: 3,
              depth: 3,
            },
            store: true,
          },
          {
            name: 'title',
            indexed: true,
            resolver: 'frontmatter.title',
            attributes: {
              encode: 'extra',
              tokenize: 'full',
              threshold: 1,
              resolution: 3,
            },
            store: true,
          },
          {
            name: 'description',
            indexed: true,
            resolver: 'frontmatter.description',
            attributes: {
              encode: 'icase',
              tokenize: 'forward',
              threshold: 2,
              depth: 3,
            },
            store: true,
          },
          {
            name: 'type',
            indexed: false,
            resolver: 'frontmatter.type',
            store: true,
          },
          {
            name: 'slug',
            indexed: false,
            resolver: 'fields.slug',
            store: true,
          },
          {
            name: 'layout',
            indexed: false,
            resolver: 'frontmatter.layout',
            store: true,
          },
        ],
      },
    },
```

在上面的示例中，我声明了要由 flex search 索引的自定义属性(id、布局和 slug ),这些属性对于显示结果片段非常重要，并且必须可以从搜索组件中获得。

重新启动开发服务器后，flex 搜索索引和商店将附加到浏览器的窗口对象。

# 带代码片段的搜索组件

现在，我们的盖茨比网站将启用灵活搜索。到目前为止，一切顺利！现在，我们必须让用户能够对我们的内容进行搜索。

为此，我们需要一个搜索组件！最重要的部分是搜索功能本身。如果你想查看完整的组件[，看看这个要点](https://gist.github.com/mrkaluzny/f53a0323621a670e86a51dad7c78b804)

```
getSearchResults(query) {
    *var* index = window.__FLEXSEARCH__.en.index
    *var* store = window.__FLEXSEARCH__.en.store
    *if* (!query || !index) {
      *return* []
    } *else* {
      *var* results = []
      Object.keys(index).forEach((idx) => {
        results.push(...index[idx].values.search(query))
      }) results = Array.from(*new* Set(results)) *var* nodes = store
        .filter((node) => (results.includes(node.id) ? node : *null*))
        .map((node) => node.node) *return* nodes
    }
  }
```

为了搜索我们的文档，我们可以创建一个简单的函数，它接受用户的输入，并使用 Flexsearch 索引和存储来执行搜索。

首先，我们为结果创建一个数组。然后，我们可以检查查询用户搜索的每个索引，并返回匹配的值。然后，我们使用结果数组来过滤 Flexsearch 商店，并返回与我们的查询匹配的对象。该函数返回包含与查询匹配的文档信息的节点。

然后，这些节点可用于呈现每个搜索结果的片段。你可以在[我们上个月做的酒神网站](https://dionysus.events/)上查看完整的实现

嘶！如果你正在寻找一个有顺风和 Netlify CMS 的了不起的盖茨比，你必须看看 [Henlo](https://henlo.netlify.app/)