# 使用 Express 和 TypeScript 创建 Node.js API

> 原文：<https://levelup.gitconnected.com/creating-a-node-js-api-with-express-and-typescript-549fba5f5a33>

![](img/cc8b99eb77e324686afd3e93cc54afbc.png)

詹姆斯·哈里逊在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本教程中，我们将使用 Express 和 TypeScript 带给我们的所有功能创建一个 Node.js API。

我们将从初始化项目和安装一些依赖项开始。我将在本教程中使用 yarn，但是如果你喜欢的话，也可以随意使用 npm。

```
mkdir new-project
cd new-project
yarn init -y
```

首先我们创建我们的项目文件夹，打开它并用 *yarn init -y* 初始化我们的项目，创建一个 *package.json* 文件。现在我们必须安装一些依赖项:

```
yarn add express
yarn add typescript ts-node nodemon -D
yarn add @types/express @types/node -D
```

我们从安装 express 开始。然后，我们安装 *typescript* 和 *ts-node* 库来为我们的项目添加 typescript 支持，并安装 *nodemon* 来使我们的服务器监听修改(这样我们就不必在每次对某个文件进行更改时重新启动我们的项目)。最后，我们会安装一些*类型的*来避免一些后患。

安装好库之后，现在我们必须创建我们的 *tsconfig.json* 。这是定义 TypeScript 配置的文件，比如它应该检查哪些文件夹以及应该将文件构建到哪个文件夹。

在项目文件夹中创建 *tsconfig.json* 文件:

tsconfig.json

我们需要做的最后一个修改是对我们的 *package.json* 文件的修改。我们将添加脚本来运行我们的开发项目(使用 *nodemon* 和 *ts-node* )并构建和启动已构建的文件。

修改您的 *package.json* 文件，使其看起来像(但不一定要完全相等)如下所示。重点关注*剧本*部分:

package . JSON—您的文件不必完全像这样。只需关注脚本部分

我们正在创建三个脚本:

*   “构建”将运行 *tsc* 命令来编译我们的*。ts* 文件转换成*文件。我们在*ts config . JSON*(*dist*文件夹)上定义的文件夹内的 js* 文件；
*   “start:prod”将启动 *dist* 文件夹中的 *server.js* 文件；
*   “start:dev”将用 *nodemon* (他将运行 *ts-node* )启动我们的项目，让我们的服务器监听对我们项目文件夹中文件的任何修改。

在安装和设置好一切之后，我们终于可以创建我们的 API 了。我们将首先创建一个 *src* 文件夹来包含我们项目中的所有代码。

在 *src* 文件夹中创建 *app.ts* 文件:

应用程序

App 类用于使用 express 实例化服务器。这种方式增加了复杂性，但使代码更容易维护。从导入中我们可以看到，我们需要从一个 *routes.ts* 文件中导入*条路线*，所以让我们创建它。

在 *src* 文件夹下创建 *routes.ts* 文件:

routes.ts

一如既往，我们有我们的“你好世界”。在这个文件中，我们将添加我们将在 API 中使用的每条路线。现在，最后一步是创建启动服务器的文件。从 *package.json* 脚本中我们知道文件名将是 *server.ts* ，所以…

在 *src* 文件夹中创建 *server.ts* 文件:

server.ts

我们准备好出发了。只需在您的 [http://localhost:3333/](http://localhost:3333/) 上运行 start 命令并进行测试

```
yarn start:dev
```

如果您想部署您的应用程序，您只需运行:

```
yarn build
yarn start:prod
```

仅此而已。我们使用 Express 和 TypeScript 开发了我们的服务器。当然，我们必须做一些额外的步骤，但这绝对值得花时间，因为 TypeScript 添加了一些可怕的保护和可用性层。

如果您想验证最终代码，[请在此处](https://github.com/phcarvalho/medium-posts/tree/main/01-base_api_with_ts)检查。如果您想继续开发这个 API，添加一个数据库和路由来创建、读取、更新和删除(CRUD)记录，请查看我的另一个教程，它是这个教程的延续:

[](/adding-and-using-a-database-on-a-typescript-api-with-typeorm-41c49336eff5) [## 使用 TypeORM 在 TypeScript API 上添加和使用数据库

### 在本教程中，我们将把 TypeORM 添加到我们的 API 中，我们将使用它来创建一个数据库来保存产品…

levelup.gitconnected.com](/adding-and-using-a-database-on-a-typescript-api-with-typeorm-41c49336eff5)