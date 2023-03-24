# Apollo GraphQL 类星体框架

> 原文：<https://levelup.gitconnected.com/quasar-framework-with-apollo-graphql-b25be59bf72a>

![](img/caebaae2e052bbd2774aa06c63e0c66f.png)

如果您想让 GraphQL 与 Qausar 框架一起工作，那么这篇文章非常适合您。

但是在进入真正的商业领域之前，你应该知道这两种技术的基础，为此我建议阅读我以前关于 [Quasar](https://medium.com/better-programming/an-introduction-to-the-quasar-framework-3fed8bc92f6d) 和 [GraphQL](https://medium.com/@ansertechgeek/graphql-a-data-query-language-e22e2d2f8eeb) 的文章。

我们将要使用的软件包是:

*   [Vue-阿波罗](https://apollo.vuejs.org/)
*   [类星体阿波罗应用扩展](https://github.com/quasarframework/app-extension-apollo)

让我们开始开发。

## 设置 Quasar 项目以使用 Vue-Apollo:

要使用 Quasar，您需要使用以下命令安装其 CLI:

```
$ yarn global add @quasar/cli
# or
$ npm install -g @quasar/cli
```

现在使用 Quasar CLI 创建一个新项目，使用:

`$ quasar create quasar-graphql-app`

按照所有的说明，你将有一个 quasar 项目准备好所有必要的软件包来运行这个项目。

是时候在项目中集成 GraphQL 了。

Quasar 遵循一个非常强大的[应用程序扩展系统](https://quasar.dev/app-extensions/introduction)来无痛地注入具有各种依赖关系的库。Quasar 的 GraphQL 也可以作为扩展使用。让我们使用以下代码将这个惊人的扩展添加到我们的项目中:

`$ quasar ext add @quasar/apollo`

只需按照简单的说明来完成安装过程。

顺便说一下，在以后的任何时候，您也可以使用以下命令删除这个扩展:

`$ quasar ext remove @quasar/apollo`

## 启用 Quasar App 使用`gql`标签:

为了让 vue-apollo 组件使用`gql`标签，你必须打开一个特殊的转换，这样 [vue-loader](https://vue-loader.vuejs.org/) 就不会在那些新标签上失败。将下面的代码添加到您的`quasar.conf.js`文件的`build`属性中。

```
chainWebpack (chain, { isServer, isClient }) {
    chain.module.rule('vue')
      .use('vue-loader')
      .loader('vue-loader')
      .tap(options => {
        options.transpileOptions = {
          transforms: {
            dangerousTaggedTemplateString: true
          }
        }
        return options
      })
  }
```

为了在您的项目中使用`.gql`或`.graphql`文件，您需要向`chainWebpack`添加另一个规则，如下所示。

```
chain.module.rule('gql')
   .test(/\.(graphql|gql)$/)
   .use('graphql-tag/loader')
   .loader('graphql-tag/loader')
```

注入上述代码后，项目就可以用 Qausar 运行 GraphQL 了。

如果您注意到项目的文件夹结构，您应该在根级别看到`quasar.extensions.json`。这告诉 Quasar 您已经安装了 Apollo 应用程序扩展，它保存了您的 GraphQL API 端点 URI 的输入。

```
{
  "@quasar/apollo": {
    "graphql_uri": "http://api.example.com"
  }
}
```

在您的项目中，您还应该看到一个名为“apollo”的文件夹，其中包含文件`apollo-client-config.js`和`apollo-client-hooks.js`。

Apollo 客户端配置选项可以根据需求添加到配置文件中，而您可以将自定义代码添加到 hooks 文件中，例如在应用程序初始化之前或之后进行处理。

这就是您的 Quasar 项目准备对您的 apollo-graphql 服务器进行 GraphQL 查询和修改的全部内容。最好的部分是，在使用 GraphQL 时，您不需要 [Vuex](https://vuex.vuejs.org/) 或任何其他状态管理库。

学到了新东西？评论和反馈总能让作者开心。编码快乐！