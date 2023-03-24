# 带有 Node.js 和 Express 的 API:使用 OpenAPI 3 规范自动验证 API 请求

> 原文：<https://levelup.gitconnected.com/build-an-api-with-node-js-4573d3520cf>

![](img/a54960ac9cc90edee4183ec62987bf8c.png)

Web APIs 是当今应用程序的核心。它们提供的接口很容易被任何平台上用任何编程语言编写的应用程序使用。他们通过展示易于使用的直观界面，使复杂的技术变得简单，使应用程序开发人员能够快速编织令人难以置信的体验。

为了利用和理解一个 API，文档成为一个关键的必需品。像 [OpenAPI 3](https://github.com/OAI/OpenAPI-Specification) 这样的规范使得 API 能够以一种标准格式来描述，这种标准格式可以容易地呈现，例如作为交互式的 HTML 文档。

在本文中，我们将看到如何使用 OpenAPI 规范来**自动验证 API 请求！**

我们将使用 Node.js 和 Express 构建 API 服务器。我们将利用[express-open api-validator](https://github.com/cdimascio/express-openapi-validator)使用 OpenAPI 3 规范自动验证 API 请求。

让我们开始吧。

# 为我们的简单 API 创建一个 Express 应用程序

首先让我们创建一个简单的 Express 应用程序。

![](img/81a23d8665254c417789eb0b0bc8c5cc.png)

运行上述代码将启动一个 API 服务器，该服务器公开以下路由:

*   GET /v1/pets
*   POST /v1/pets
*   GET /v1/pets/:id

注意:*API 返回值是人为设计的，与本教程无关。*

# 创建一个 OpenAPI 规范来描述我们的 API

现在我们已经编写了简单的 API，让我们添加一些验证。但是，我们将通过创建一个 OpenAPI 3 规范来描述我们的 API，而不是编写一堆验证代码。

*(我假设你知道如何创建 OpenApi 规范，因此我将只描述相关的片段。如果你想看完整的规格，请点击* [*这里*](https://github.com/cdimascio/express-openapi-validator/blob/master/openapi.yaml) *)。*

让我们要求 GET /v1/pets 的请求必须提供查询参数 **limit** 。让我们也要求这个极限是一个大于零的整数**。**

**![](img/aa821d068484e6391989f0d5ef3d3c9d.png)**

**我们还要求 POST /v1/pets 的请求必须提供一个包含必填字段的 JSON 主体， **name** 。**

**![](img/aa0a94cd31151ac6c439265825d0f5d2.png)**

**我们还将把 NewPets 组件添加到我们的 OpenAPI 3 规范中。**

**![](img/8f74291975a3c4e5667bc38f4138d6e6.png)**

# **将自动请求验证与 Express-OpenAPI-Validator 集成**

****最后**，我们将做一些小的代码调整，使我们的 API 服务器能够使用我们的 OpenAPI 3 规范自动验证 API 请求。**

**代码调整包括以下内容:**

1.  **要求[express-open API-validator](https://github.com/cdimascio/express-openapi-validator)—一个根据 OpenAPI 3 规范自动验证 Express 中定义的路由的包**
2.  **将 OpenApiValidator 安装到我们的 express 应用程序中**
3.  **提供一个快速错误处理程序来定制我们的错误响应**

**完成这些更改后，我们的最终代码如下:**

***(注意，步骤 1、2 和 3 表示已经添加的新代码)***

**![](img/a29b7a0b6a059dc7e14d65d9451c38b8.png)**

**启动服务器，然后…**

# **尝试一下**

**让我们用 **curl** 执行一些 API 请求，并观察自动请求验证的运行。**

**让我们试试 **GET /v1/pets****

**![](img/e108713de99ffd2141bb3e50a2cdb802.png)**

**退货:**

**![](img/84748bf7ea8860ac78daba58ae606ce9.png)**

**让我们试试 **POST /v1/pets****

**![](img/91494d0bb7fc2c66a5c5b498a9302d62.png)**

**返回**

**![](img/0694679204afdd2c22a302b0e6c52f5f.png)**

**这个例子的完整源代码可以在[这里](https://github.com/cdimascio/express-openapi-validator/blob/master/example/app.js)找到。**

**如果你理解了[express-open API-validator](https://github.com/cdimascio/express-openapi-validator)，**

**Github 上的 [Star](https://github.com/cdimascio/express-openapi-validator) it！**

**[](https://gitconnected.com/learn/node-js) [## 学习 Node.js -最佳 Node.js 教程(2019) | gitconnected

### 前 33 个 Node.js 教程-免费学习 Node.js。课程由开发人员提交和投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/node-js)**