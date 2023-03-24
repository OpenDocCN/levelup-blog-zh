# 如何在盖茨比中动态创建页面

> 原文：<https://levelup.gitconnected.com/how-to-create-pages-dynamically-in-gatsby-ab7b73af3790>

![](img/56d75d8e2ea552a1dbcd8170abfc7b75.png)

照片由 [Kira auf der Heide](https://unsplash.com/@kadh) 拍摄

在这篇文章中，我们将探讨如何从 Gatsby 中的数据(markdown 文件)以编程方式创建页面。

为了从 markdown 动态创建这些页面，我们将研究 Gatsby 的 Node `createPages` API。

然而，在我们这样做之前，我们需要安装几个插件来获取和转换 markdown 文件，以便 Gatsby 能够理解 markdown 文件的路径及其内容，并能够创建这些页面。

要创建页面，我们需要做以下 3 个步骤，使其动态发生。

# 装置

通过安装以下插件

```
npm install --save gatsby-source-filesystem gatsby-transformer-remark
```

## 1.读取降价文件并转换它们

我们将通过传递一个包含变量和文件路径的 options 对象来告诉 Gatsby 我们的文件系统中的内容路径。

此外，我们可以使用`gatsby-transformer-remark`将这些 markdown 文件内容转换为 HTML，将 frontmatter 元数据转换为 frontmatter，这将把这些文件转换为包含 MarkdownRemark 节点类型的边，并将每个 frontmatter 字段转换为 GraphQL 字段，我们可以稍后在页面/组件中查询这些字段，并在构建时映射它们。

# 引导程序

为了读取 markdown 文件路径并将它们转换成 GraphQL 字段，我们需要在安装两个插件后将下面的配置添加到我们的`gatsby-config.js`中。

```
//gatsby-config.js
 `gatsby-transformer-remark`,
{
      resolve: `gatsby-source-filesystem`,
      options: {
        name: `posts`,
        path: `${__dirname}/content/posts`, // for the path you can change it based on your preferred folder structure
      },
},
```

## 2.为降价文件创建页面组件(模板)

我们需要创建一个页面模板组件，该组件将在构建时使用 GraphQL 与数据(可用的 graphql 字段，这些字段通过 gatsby-transformer-remark 进行了转换)进行映射，以动态创建这些页面。

在下面的内容中，你可以看到我为博客文章使用的文章模板的一个简化的例子。**注意:您可以创建任意多的模板**

# 例子

```
import React from 'react';
import { graphql } from 'gatsby';

const Post = ({ data }) => {
  const { markdownRemark } = data;
  const { title, date } = markdownRemark.frontmatter;
  const { html, timeToRead } = markdownRemark;

  return (
    <div>
      <div className="post-wrapper">
        <Title>{title}</Title>
        <div>
          <p>{date}.</p>
          <p>{timeToRead} min read.</p>
        </div>
        <div className="blogpost" dangerouslySetInnerHTML={{ __html: html }} />
      </div>
    </div>
  );
};
```

下面我们可以看到特定的数据(GraphQL 字段)正在被提取，并在上面的模板组件中使用现成的 GraphQL 查询。

```
export const query = graphql`
  query($pathSlug: String!) {
    markdownRemark(frontmatter: { path: { eq: $pathSlug } }) {
      html
      frontmatter {
        title
        date
      }
      timeToRead
    }
  }
`;
```

## 3.使用 gatsby 的节点`createPages` API 以编程方式创建页面

要创建新页面，我们需要执行以下步骤

*   使用 createPages API 为页面生成 slug(路径)
*   通过将内容(转换后的降价文件)映射到页面组件模板来创建页面

在`gatsby-node.js`中，我们导出 createPages API 并传递给它两个参数，`graphql`用于查找文件，`actions`其中 createPages 是它的一个属性。

由于文件创建的异步性质，createPages 函数将返回给我们一个新的承诺(或者，您可以按照 gatsby docs 中的示例使用回调)。

**注意:为了创建这些页面，我们需要使用“path.resolve”访问页面模板。**

要将 promise 的状态更改为 fulfilled，我们需要通过调用 graphql 来解决这个问题，并向它传递我们对 allmarkdownremark 的查询，其中包含我们希望帖子所在位置的路径字段。(这个路径字段是我们在 markdown frontmatter 元数据中设置的)。

此外，我们将一个函数传递给`.then`,一旦状态变为“已完成”,这个函数将被调用，我们将结果值传递给这个函数。结果将包含一个与我们的查询匹配的数据对象，正如您在屏幕截图中看到的那样。

在函数体中，我们将使用 foreach 迭代每个文件节点(边是指向文件系统节点的路径)，以提取 frontmatter 中那些节点的路径。

您可以通过控制台日志节点参数，查看查询中每个 markdown 文件返回的值，该值类似于下面的屏幕截图，显示了我们可以从节点内容中得到的结果。

![](img/e919bde9104de0b3d3b0f3c5563b0ce2.png)

有了可用的路径值，现在我们可以调用“createPage”操作，该操作接受 3 个参数:页面 url 的路径、要呈现的组件(页面模板组件)和将作为属性对页面组件可用的上下文对象。
当我们将上下文对象传递给模板组件时，我们希望模板知道名为 pathslug 的文件的路径，因为 path 是保留关键字，pathSlug 的值将是节点 frontmatter 提供的字段。在 createPage 调用之后，我们将解析 promise，如下例所示。

# 配置

```
exports.createPages = ({ graphql, actions }) => {
  const { createPage } = actions;
  return new Promise((resolve, reject) => {
    // to create the page we need access to the blog post template
    const postTemplate = path.resolve('src/templates/postTemplate.js');
    resolve(
      graphql(
        `
          {
            allMarkdownRemark {
              edges {
                node {
                  frontmatter {
                    path
                  }
                }
              }
            }
          }
        `
      ).then(result => {
        if (result.errors) {
          console.log(result.errors);
          reject(result.errors);
        }
        result.data.allMarkdownRemark.edges.forEach(({ node }) => {
          // you can see node value in the screenshot
          const path = node.frontmatter.path;

          createPage({
            path,
            component: postTemplate,
            context: {
              /*
              the value passed in the context will be available for you to use in your page queries as a GraphQL variable, as per the template snippet */
              pathSlug: path,
            },
          });
          resolve();
        });
      })
    );
  });
};
```

在下面的 graphql 中运行时，您可以在上面的代码片段中看到 graph QL 查询的结果

![](img/3f76a4f8290cf6d5cdc37202f24b67d2.png)

# 如果你觉得这篇文章有帮助

您可能会喜欢这些其他文章:

[](/how-to-create-pages-dynamically-in-gatsby-using-mdx-66faa175944e) [## 如何使用 MDX 在 Gatsby 中动态创建页面

### 在本帖中，我们将探讨如何在 Gatsby 中使用 MDX 以编程方式创建页面。

levelup.gitconnected.com](/how-to-create-pages-dynamically-in-gatsby-using-mdx-66faa175944e) [](/syntax-highlighting-in-gatsby-mdx-f0187ce51f4f) [## Gatsby MDX 中的语法高亮

### 这篇文章展示了如何使用 prism-react-renderer 为 Gatsby MDX 文件启用语法高亮显示

levelup.gitconnected.com](/syntax-highlighting-in-gatsby-mdx-f0187ce51f4f)