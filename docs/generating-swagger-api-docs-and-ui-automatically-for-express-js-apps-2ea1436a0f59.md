# 为 Express.js 应用程序自动生成 Swagger API 文档和 UI

> 原文：<https://levelup.gitconnected.com/generating-swagger-api-docs-and-ui-automatically-for-express-js-apps-2ea1436a0f59>

![](img/3dda1098be9a43b344fd9cddcc25873e.png)

如果你正在编写你的 express js API，你可能会遇到这样的情况:你的路由数量在增长，在你的团队内部或团队外部，公开地或私下地交流你的路由及其输入和输出变得越来越困难。

解决这个问题的一个常见的选择是用 swagger 或 open API docs 这样的标准来记录你的 API。尽管这确实可以产生很好的标准化文档，但是您的团队必须管理，并且文档可能会偏离您正在部署的实际 API。

为了解决这个问题，我想提出一个很好的解决方案，允许您自动为您的 API 生成最新的 swagger 文档，文档与路线一致，这样就可以很容易地看到路线何时发生了变化，以及文档是否相应地发生了变化。像这样的解决方案也使得用 API 提供您的文档变得容易，使您部署的 API 的状态更容易以他们可能已经熟悉的标准格式与他人交流。

# 生成 Swagger 文档

现在，我假设您正在使用 express js 运行您的 API，但是其他框架也有类似的解决方案。为了自动生成 API 文档，我们将使用 swagger-autogen 包来生成 JSON 格式的 swagger 文档。

[](https://www.npmjs.com/package/swagger-autogen) [## 斯瓦格-自动发电机

### 这个模块执行 Swagger 文档的自动构造。该模块能够识别…

www.npmjs.com](https://www.npmjs.com/package/swagger-autogen) 

你可以通过这个链接阅读更多关于 swagger doc 标准的内容，微软这样的公司用它来交流他们众多 API 中的一些。

[](https://swagger.io/) [## 面向团队的 API 文档和设计工具| Swagger

### 使用我们的开源专业工具集，为用户、团队和企业简化 API 开发。了解如何…

swagger.io](https://swagger.io/) 

例如，为 express 设置了一个简单的 API 服务器，其中包含一些基本路由。

```
const express = require('express')
import { handleErrorResponse } from "../../common/response"; // Generic handler for req, res
const app = express()
const port = 3000app.get("/calculate", async (req, res, next) => {
  res.setHeader('Content-Type', 'application/json')
  try {
    return res.status(200).json({ createdAt: new Date().toISOString(), result: 100 });
  } catch (err) {
    return handleErrorResponse(res, err);
  }
});app.listen(port, () => {
  console.log(`Example app listening at [http://localhost:${port}`](http://localhost:${port}`))
})
```

我们希望用这样一个大摇大摆的配置文件来配置我们要记录的应用程序。最好是定义您的应用程序名和您的 API 需要的任何重要的头，以便与使用该文档的人进行交流。

```
const config = {
  info: {
    title: "Your cool API name",
    description: "A description for your cool API",
  },
  host: `localhost:${process.env.API_PORT}`,
  securityDefinitions: {
    api_key: {
      type: "apiKey",
      name: "api-key",
      in: "header",
    },
  },
  schemes: ["http"],
  definitions: {
    "server side error": {
      $status: "ERROR",
      $msg: "some error message",
      error: {
        $message: "Error message caught",
        $name: "Error name",
        stack: "Error stack",
      },
    },
    "calculation": {
      $createdAt: "2020-03-31T00:00:00.000Z",
      $result: 100,
    },
  },
};
```

您将在本文档中注意到，在定义的键下，这些可能看起来像一般的返回类型或对象。现在请记住这些，以便在本文稍后实现它们时使用。

通过简单地在我们的服务器启动脚本中添加下面一行，我们可以在每次启动服务器时为我们的 API 生成新的 swagger 文档。

```
const swaggerAutogen = require("swagger-autogen")();const outputFile = "./swagger-output.json";
const endpointsFiles = ["./app.js"];
const config = {}swaggerAutogen()(outputFile, endpointsFiles, config).then(async () => {
  await import('./index.js'); // Your express api project's root file where the server starts
});
```

如果你看这个 ***。/swagger-output.json，*** 您会注意到，在这个简单的 API 实现示例中，路由将被自动选取。然而，随着您的设置变得越来越复杂，您肯定希望用您自己的信息和对它的修正来覆盖一些自动检测到的 API 文档。

# 记录 API 路线

随着您的 API 在构建过程中变得越来越复杂，您可能会发现，在启动时，您的 API 的一些编程方面可能不会被自动检测到并填写在 API 文档中。此时，您将需要使用 swagger docs inline comments 语法来记录您的路由部分，例如元数据，如路由路径参数或接受的头的标记和描述。

```
const express = require('express')
const handleErrorResponse = require("../../common/response"); // Generic handler for req, res
const app = express()
const port = 3000app.get("/calculate", async (req, res, next) => {
  /*
    #swagger.tags = ["calculations"]
    #swagger.description = 'Fetch a calculation result'
  */

 // Recognizes the 'consumes' automatically
  res.setHeader('Content-Type', 'application/json')try {
    /* #swagger.responses[200] = {
      description: "Category configs fetched successfully",
      schema: { $ref: "#/definitions/calculation"
    } */
    return res.status(200).json({ createdAt: new Date().toISOString(), result: 100 });
  } catch (err) {
    /* #swagger.responses[500] = {
      description: "Unknown server side error",
      schema:  { $ref: "#/definitions/server side error" }
    } */
    return handleErrorResponse(res, err);
  }
});app.listen(port, () => {
  console.log(`Example app listening at [http://localhost:${port}`](http://localhost:${port}`))
})
```

在我工作的工程团队中。我发现让这种文档更接近实现会导致它有更高的机会保持最新，因为工程可能会回到代码库并更新实现，并且当它是内联的并且接近它所记录的实现时，也会看到有文档要更新。

# 记录路线参数

Swagger autogen 尽最大努力根据使用情况自动生成一些属性。在下面的例子中，swagger 将根据您声明的路由路径和传入的参数来获取您的参数使用情况。如果您想要向 routes 参数描述符添加额外的细节，或者如果它们由于任何原因没有被自动选取，我还包括了行内注释阶段。

```
app.get('/users/:id', (req, res) => {
        ...
        //  #swagger.parameters['id'] = { description: 'User ID' }
        ...
    })app.post('/books', (req, res) => {
        ...
        /*  #swagger.parameters['obj'] = {
                in: 'body',
                type: 'object',
                description: 'Book data'
        } */
        users.addUser(req.body)
        ...
    })app.post('/users', (req, res) => {
        ...
        /*  #swagger.parameters['obj'] = {
                in: 'body',
                description: 'Add a user',
                schema: { $ref: '#/definitions/AddUser' }
        } */
        ...
    })app.get('/users', async (req, res) => {
        /*  #swagger.parameters['item'] = {
                in: 'query',
                description: 'Any item...'
        } */
        let test = req.query.item
    });
```

# 引用和重用公共对象

如果您还记得，在您的 swagger 配置中，我们已经在定义的键下声明了一些公共对象。现在，这些对象将是您的通用返回类型响应和数据模型，它们将在您的 API 中使用。在下面的例子中，你可以看到如何使用***#/definitions/<definition-key>***语法从配置文件中引用它们。

```
const express = require('express')
const handleErrorResponse = require("../../common/response"); // Generic handler for req, res
const app = express()
const port = 3000app.get("/calculate", async (req, res, next) => {
  /*
    #swagger.tags = ["calculations"]
    #swagger.description = 'Fetch a calculation result'
  */

 // Recognizes the 'consumes' automatically
  res.setHeader('Content-Type', 'application/json')try {
    /* #swagger.responses[200] = {
      description: "Calculation fetched successfully",
      schema: { $ref: "#/definitions/calculation"
    } */
    return res.status(200).json({ createdAt: new Date().toISOString(), result: 100 });
  } catch (err) {
    /* #swagger.responses[500] = {
      description: "Unknown server side error",
      schema:  { $ref: "#/definitions/server side error" }
    } */
    return handleErrorResponse(res, err);
  }
});app.listen(port, () => {
  console.log(`Example app listening at [http://localhost:${port}`](http://localhost:${port}`))
})
```

老实说，通过重用这些公共对象，您节省了大量用于记录 API 的样板代码；你还需要花时间来定义一些公共模型，这些模型可以是你的 API 的输入或输出，这将有助于提高你的 API 的效率或简化 API。

在我自己的上一个项目中，我发现我们几乎所有的数据返回都返回了稍微不同的键命名约定，并且很快被指向一个重构，如果所有的键名称在模型和端点中遵循相同的模式，那么可以完成这个重构，使 API 探索变得更加自然。

# 为您生成的招摇博士服务

这样，您就可以从 API 实现旁边的内联 swagger 定义中自动生成整个 API 文档。你可能会问，我现在还能用我拥有的这个大摇大摆的文档做些什么。既然您已经在标准文档中清晰地定义了 API 定义，那么一件方便的事情就是您可以轻松地将它注入到其他工具中，以便更容易地探索 API。

例如，如果您想通过一个 UI 展示您的 API 及其所有路线，您可以使用 swagger-UI-express 包在熟悉的 swagger UI 中自动提供您生成的 swagger 文档。

[](https://www.npmjs.com/package/swagger-ui-express) [## 斯瓦格-ui-express

### 这个模块允许你基于 swagger.json 从 express 提供自动生成的 swagger-ui 生成的 API 文档…

www.npmjs.com](https://www.npmjs.com/package/swagger-ui-express) ![](img/8c6f959bd1074e32a5787cb67c8efb41.png)

然后，您可以很容易地设置一个途径，将您的 API 文档的这个 UI 提供给任何想要了解您的 API 的当前部署版本的能力的人。

```
const router = require('express').Router();
const swaggerUi = require('swagger-ui-express');
const swaggerDocument = require('./swagger.json');router.use('/api-docs', swaggerUi.serve);
router.get('/api-docs', swaggerUi.setup(swaggerDocument));
```

有了正式的文档和 UI，任何人都没有理由不知道 API 中有什么，以及如何在最好的情况下与它通信，这样就可以省去一些关于所支持的 API 的常见问题。

# 最后的想法

将您的文档放在您的 API 实现代码旁边使得更新和保存您的文档变得更加容易，这在几乎所有的开发环境中都是有用的。

通过利用 swagger 标准，您有机会轻松地交流您的 API 当前规范，进行 API 客户端的配置。正如我们在上面的 swagger UI 中看到的，如果您的工具支持 swagger 文件输入，那么您已经准备好使用您生成的 swagger 文档了。

如果您想看看完整的 swagger docs 示例是什么样的，您可以看看下面的公共 API 示例，它向您展示了共享这些文档对于快速交流您的项目以及每个开发人员可能需要的详细程度是多么强大。

 [## Swagger UI

### 编辑描述

petstore.swagger.io](https://petstore.swagger.io/) 

# 进一步连接

*   如果你正在考虑购买一份中等订阅，你可以通过我的推荐链接来帮我。
*   查看我在[媒体](https://medium.com/@aaron-kt-berry)上的其他文章，如果你想了解最新消息，请通过[电子邮件](https://aaron-kt-berry.medium.com/subscribe)订阅。
*   如果你想聊天，请在 Twitter 或 LinkedIn 上联系我，如果你想雇佣我，我在 Codementor 上。