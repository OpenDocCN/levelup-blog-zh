# 带有 AWS Amplify、AppSync、GraphQL 和 Vue.js 的实时购物清单应用程序

> 原文：<https://levelup.gitconnected.com/realtime-shopping-list-app-with-aws-amplify-appsync-graphql-and-vue-js-94f9d45fe9d2>

![](img/c6a1b58007084d492fc07c63c50177c6.png)

照片由 [PhotoMIX 有限公司](https://www.pexels.com/@wdnet?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)从 [Pexels](https://www.pexels.com/photo/vegetables-stall-868110/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄

与 Firebase 和 Azure 等其他无服务器提供商相比，AWS 是出了名的难学。有太多的服务和配置选择，以至于启动并运行一个应用程序可能是一项艰巨的任务。

这就是 AWS Amplify 大放异彩的地方。只需几个终端命令，您就可以在不到一个小时的时间内运行整个后端系统。我们所说的带认证的 API 比你早上准备的时间还要短😮。

# 视频教程

视频教程

# 入门指南

如果您还没有帐户，请前往 [AWS](https://aws.amazon.com/) 创建一个帐户。接下来，我们需要安装 CLI。打开您的终端并运行以下命令:

```
npm i -g @aws-amplify/cli
```

# 应用程序设置

在本文中，我们将使用 Vue.js 和 AWS Amplify 构建一个购物清单。它将提供认证和 GraphQL API。如果您从未使用过 [GraphQL](https://graphql.org/) (我以前没有使用过)，请不要担心。Amplify 和 AppSync 使它变得超级简单，因为它编写了我们检索和操作数据所需的查询。

## Vue 项目

要创建 Vue 项目，请打开您的终端，cd 到您选择的目录，并运行以下命令:

```
npx @vue/cli create amplify-shopping-list
```

这些是我在创建新项目时选择的 CLI 选项:

1.  请选择一个预置:
    **手动选择特性**
2.  从列表中:
    **通天塔、路由器、棉绒/格式化程序**
3.  对路由器使用历史模式？
    Y**Y**
4.  选择短绒/格式器配置
    **ESLint+beauty**
5.  摘附加皮棉功能
    T21【皮棉上存
6.  你更喜欢把 Babel，ESLint 等的配置放在哪里？？
    **在专用配置文件**中

接下来，在您喜欢的代码编辑器中打开项目。我将使用 VS 代码。打开一个新终端(Terminal => New Terminal)并运行以下命令:

```
npm i bootstrap-vue aws-amplify aws-amplify-vue
```

## AWS 放大器

现在，我们将设置我们的 AWS IAM 用户，我们将使用该用户以编程方式创建我们将在应用程序中使用的服务。在终端中运行，并按照说明操作:

```
amplify configure
```

接下来，我们将在项目中初始化 amplify。在终端中运行以下命令:

```
amplify init
```

**选择以下选项:**

1.  输入项目名称:
    **回车选择默认**
2.  输入环境的名称: **dev**
3.  选择你的默认编辑器:
    **Visual Studio 代码**
4.  选择你正在构建的应用类型:
    **javascript**
5.  你用的是什么 javascript 框架:
    **vue**
6.  源目录路径:
    **回车选择默认** (src)
7.  分发目录路径:
    **回车选择默认** (dist)
8.  构建命令:
    **回车选择默认的** (npm.cmd run-script build)
9.  开始命令:
    **回车选择默认** (npm.cmd run-script serve)
10.  是否要使用 AWS 配置文件？:
    Y**Y**
11.  选择您在最后一步中设置的帐户

现在 AWS amplify 已经在项目中初始化，让我们用下面的命令添加我们的 API 和身份验证:

```
amplify add api
```

**选择以下选项:**

1.  请从下列服务中选择一项:
    **GraphQL**
2.  提供 API 名称: **回车默认(amplifyshoppinglist)**
3.  选择 API 的默认授权类型: **Amazon Cognito 用户池**
4.  您想使用默认的身份验证和安全配置吗？**默认配置**
5.  您希望用户如何登录？
    **用户名**
6.  您想配置高级设置吗？
    **不，我完成了。**
7.  您想为 GraphQL API 配置高级设置吗:
    **不，我完成了**
8.  你有带注释的 GraphQL 模式吗？
    **N**
9.  **。你想要一个引导模式创建吗？
    Y**Y****
10.  什么最能描述您的项目:
    **带有字段的单个对象(例如，带有 ID、名称、描述的“Todo”)**
11.  您想现在编辑模式吗？
    **n**

## 应用程序接口

设置完成后，您应该在项目的根目录下有一个新的“amplify”文件夹。在该文件夹中，导航到/back end/API/{ your _ project _ name }/schema . graph QL。该文件决定了我们将在 GraphQL API 中使用的数据模型。在该文件中粘贴以下内容:

```
type ShoppingListItem @model @auth(rules: [{allow: owner}]) {
id: ID!
itemName: String!
}
```

这将建立一个 DynamoDb 表，其中的列与上述字段相匹配。它还将为我们的 GraphQL API 设置查询、变更和订阅，只有创建数据的所有者才能访问这些 API(@ auth section)。我们需要做的就是保存所有内容，在 VS 代码中打开一个新的终端，并运行以下命令:

```
amplify push
```

## **选择以下选项:**

1.  您想为新创建的 GraphQL API:
    **Y** 生成代码吗
2.  选择代码生成语言目标:
    **javascript**
3.  输入 graphql 查询、突变和订阅的文件名模式
    **回车默认为(src\graphql\**\*)。**
4.  您是否希望生成/更新所有可能的 GraphQL 操作—查询、变异和订阅
    **Y**
5.  输入最大语句深度[如果模式深度嵌套，则从默认值增加]
    **按 Enter 键选择默认值(2)**

这将需要一些时间来运行，但完成后，你会看到你有一些新的文件夹和文件:

## src/graphql 文件夹

当您更改您的 amplify schema.graphql 时，该文件夹会自动创建和更新。它包含我们稍后将使用的查询、变更和订阅。

## . graphqlconfig.yml

这个文件控制上面的代码生成选项，这些选项是我们在创建 API 时选择的。如果您想更改代码的文件路径或语言，您可以在这里进行更改。

# Vue 项目

在我们的 main.js 文件中，配置 Vue 项目以使用 Amplify 和 BootstrapVue:

在 main.js 中导入 Amplify

## 证明

好了，现在我们终于可以开始编码了🙌！在我们的 views 文件夹中，创建一个新文件 Auth.vue。这将处理我们应用程序中的所有认证流程，Amplify 已经神奇地通过他们的认证器组件和事件总线为我们处理了所有这些，我们可以监听这些动作。

在您的 Auth.vue 中粘贴以下代码:

授权用户

## 购物单

既然我们已经设置了用户身份验证，现在是创建购物列表的时候了。在 views 文件夹中，创建一个新文件，并将其命名为 ShoppingList.vue。我试图用注释来解释代码中发生了什么。

ShoppingList.vue

## 路由器

现在我们已经设置好了所有页面，我们需要配置我们的路由，并在路由器中添加一个路由保护。在 src/router/index.js 中添加以下代码:

Vue 路由器

现在你有了一个实时购物清单应用程序。这只是一个简单的应用程序，还有很大的改进空间。如果您改进了这个应用程序或有任何其他问题，请在下面的评论中提出。