# NestJS:具有 gRPC、API 网关和身份验证的微服务—第 2/2 部分

> 原文：<https://levelup.gitconnected.com/nestjs-microservices-with-grpc-api-gateway-and-authentication-part-2-2-d67dc8e3b86a>

## 以打字打的文件

## NestJS 应用程序的第二部分，包括 gRPC、API 网关、认证和验证

![](img/2b9467643786a71cc9ddc8fb6078ccf8.png)

欢迎阅读我关于如何使用 NestJS plus API 网关和认证开发多个微服务的分步指南的第二部分。

因为第一部分很重，所以我们不要浪费太多时间！

如果您没有阅读第 1 部分:

[](/nestjs-microservices-with-grpc-api-gateway-and-authentication-part-1-2-650009c03686) [## NestJS:具有 gRPC、API 网关和认证的微服务—第 1/2 部分

### 分步指南:带有 gRPC、API 网关、认证和验证的 NestJS 应用程序

levelup.gitconnected.com](/nestjs-microservices-with-grpc-api-gateway-and-authentication-part-1-2-650009c03686) 

第 2 部分从这里开始！

# 认证服务(grpc-nest-auth-svc)

在通过编码 API 网关、设置数据库和提供我们的原型文件作为共享存储库做了大量准备之后，我们终于开始了我们的第一个微服务。

github:[https://github.com/hellokvn/grpc-nest-auth-svc](https://github.com/hellokvn/grpc-nest-auth-svc)

首先，我们需要在我们的代码编辑器中打开项目，所以回到您的终端中存储项目的目录。

```
$ cd grpc-nest-auth-svc
$ code .
```

## 安装依赖项

让我们安装一些我们将需要的依赖项。

```
$ npm i @nestjs/microservices @nestjs/typeorm @nestjs/jwt @nestjs/passport passport passport-jwt typeorm pg class-transformer class-validator bcryptjs
$ npm i -D @types/node @types/passport-jwt ts-proto
```

## 项目结构

像往常一样，我将继续创建最终的文件夹和文件结构。为了简单起见，我们这里只讨论一个模块，称为`auth`。

```
*$ nest g mo auth && nest g co auth* --no-spec
$ mkdir src/auth/filter && mkdir src/auth/service && mkdir src/auth/strategy
$ touch src/auth/filter/http-exception.filter.ts
$ touch src/auth/service/auth.service.ts
$ touch src/auth/service/jwt.service.ts
$ touch src/auth/strategy/jwt.strategy.ts
$ touch src/auth/auth.dto.ts
$ touch src/auth/auth.entity.ts
```

## 添加脚本

此外，我们需要向我们的`package.json`添加一些脚本，以基于我们刚刚完成的共享 proto 项目生成我们的 protobuf 文件。类似于我们在 API 网关中所做的。

让我们简单地将这 5 行代码添加到我们的`package.json`文件的`scripts`属性中。

> *用你的 Github 用户名替换* ***你的用户名*** *。*

package.json

让我们运行这些脚本吧！

```
$ npm run proto:install && npm run proto:auth
```

所以在这些步骤之后，我们的项目应该看起来像这样:

![](img/07f4f1a9324ee4ee730c04eb7278b2a3.png)

现在让我们开始编码。

## HTTP 异常过滤器

由于我们将使用数据传输对象文件进行有效负载验证，我们需要捕获 HTTP [异常](https://docs.nestjs.com/exception-filters)，因为我们将使用的包 [class-validator](https://docs.nestjs.com/techniques/validation) 会在请求的有效负载无效的情况下抛出 HTTP 异常。因为我们不想从 gRPC 服务器抛出 HTTP 异常，所以我们捕捉这些异常，并将它们转换成普通的 gRPC 响应。

让我们给`src/auth/filter/http-exception.filter.ts`添加一些代码

## 授权 DTO /验证

让我们给`src/auth/auth.dto.ts`添加一些代码

## 授权实体

让我们给`src/auth/auth.entity.ts`添加一些代码

## JWT 服务

让我们给`src/auth/service/jwt.service.ts`添加一些代码

## JWT 战略

让我们给`src/auth/strategy/jwt.strategy.ts`添加一些代码

## 授权服务

让我们给`src/auth/service/auth.service.ts`添加一些代码

## 授权控制器

让我们把`src/auth/auth.controller.ts`从

到

## 授权模块

让我们把`src/auth/auth.module.ts`从

到

## 应用模块

让我们把`src/app.module.ts`从

到

## 引导程序

让我们把`main.ts`从

到

## 运行我们的微服务

```
$ npm run start:dev
```

![](img/e5516874304e785d59a5d377084e17cd.png)

太好了！一切按预期运行。现在，我们完成了身份认证微服务，只剩下 2 个微服务。由于订单微服务有点依赖于产品微服务，我们接下来将进行产品微服务。

这两种微服务都将更容易开发，就像它们在 API 网关上的端点一样，因为它们只需要处理传入的数据。

# 产品服务(grpc-nest-产品-服务)

github:[https://github.com/hellokvn/grpc-nest-product-svc](https://github.com/hellokvn/grpc-nest-product-svc)

这个命令完成后，我们在代码编辑器中打开`grp-nest-product-svc`项目。

```
$ cd grpc-nest-product-svc
$ code .
```

## 安装依赖项

让我们安装一些我们将需要的依赖项。

```
$ npm i @nestjs/microservices @grpc/grpc-js @grpc/proto-loader @nestjs/typeorm typeorm pg class-transformer class-validator
$ npm i -D @types/node ts-proto
```

## 项目结构

像往常一样，我将继续创建最终的文件夹和文件结构。为了简单起见，我们这里只讨论一个模块，称为`product`。

```
*$ nest g mo product && nest g co product* --no-spec *&& nest g s product* --no-spec
$ mkdir src/product/entity
$ touch src/product/product.dto.ts
$ touch src/product/entity/product.entity.ts
$ touch src/product/entity/stock-decrease-log.entity.ts
```

## 添加脚本

此外，我们需要向我们的`package.json`添加一些脚本，以基于我们刚刚完成的共享 proto 项目生成我们的 protobuf 文件。类似于我们在 API 网关中所做的。

让我们简单地将这两行代码添加到我们的`package.json`文件的`scripts`属性中。

> *用你的 Github 用户名替换* ***你的用户名*** *。*

package.json

让我们运行这些脚本吧！

```
$ npm run proto:install && npm run proto:product
```

所以在这些步骤之后，我们的项目应该看起来像这样:

![](img/8c2cb2815bfd7fcc22ff30972560a77b.png)

现在，该编码了。

## 产品实体

首先，我们创建我们的产品实体。

让我们给`src/product/entity/product.entity.ts`添加一些代码

## StockDecreaseLog 实体

此外，我们必须创建我们的 StockDecreaseLog 实体。在这里，我们保存订单 ID 和产品 ID 上绑定的每个减少操作。这是因为我们希望防止由不可预测的错误导致的单个订单创建的多次减少。

让我们给`src/product/entity/stock-decrease-log.entity.ts`添加一些代码

## 产品 DTO /验证

为了验证，我们创建 3 个 dto，实现每个端点的接口。

让我们给`src/product/product.dto.ts`添加一些代码

## 产品服务

因为我们有 3 个端点，所以我们需要为每个端点提供 3 个服务。你很快就会明白发生了什么，因为他们的名字都是自我描述的。

让我们把`src/product/product.service.ts`从

到

在第 49 行，我们将检查产品是否已经因某个特定订单而减少了一次。这是出于等幂的原因。想象一下，我们因为一些 bug 对同一个订单调用了两次 decreaseStock，然后我们得到了错误的产品库存。我们将通过将订单 ID 绑定到该方法来防止这种情况。所以即使偶然调用这个方法两次，股票也会是正确的。

> ***什么是幂等？***
> 
> *幂等性是一种属性，保证对同一个操作的重复调用不会导致服务状态的任何改变，也不会导致任何额外的副作用。*

## 产品控制器

此外，每个端点都需要一个位于控制器内部的方法。

让我们把`src/product/product.controller.ts`从

到

## 产品模块

现在，我们需要简单地将产品实体添加到我们的模块中，因为我们将在产品服务中使用它。

让我们把`src/product/product.module.ts`从

到

## 应用模块

在这个微服务中，我们也需要一个数据库连接。

让我们把`src/app.module.ts`从

到

## 引导程序

最后，我们需要在端口 50053 上注册我们的微服务。

让我们把`main.ts`从

到

现在我们可以运行我们的产品微服务

```
$ npm run start:dev
```

![](img/9e227d693ba65e8b4315ab7db3774aef.png)

恭喜你！我们的产品微服务已经完成。

# 订单服务(grpc-nest-order-svc)

github:[https://github.com/hellokvn/grpc-nest-order-svc](https://github.com/hellokvn/grpc-nest-order-svc)

我们终于推出了最后一项微服务。我会尽可能简单，所以最后一轮请和我在一起。同样，在您的终端上，返回到我们存储本指南所有项目的目录，然后:

```
$ cd grpc-nest-order-svc
$ code .
```

## 安装依赖项

让我们安装一些我们将需要的依赖项。

```
$ npm i @nestjs/microservices @grpc/grpc-js @grpc/proto-loader @nestjs/typeorm typeorm pg class-transformer class-validator
$ npm i -D @types/node ts-proto
```

## 项目结构

让我们创建一个精益的项目结构。

```
*$ nest g mo order && nest g co order* --no-spec *&& nest g s order* --no-spec
$ mkdir src/order/proto
$ touch src/order/order.dto.ts
$ touch src/order/order.entity.ts
```

## 添加脚本

此外，我们需要向我们的`package.json`添加一些脚本，以基于我们刚刚完成的共享 proto 项目生成我们的 protobuf 文件。类似于我们在 API 网关中所做的。

让我们简单地将这 4 行代码添加到我们的`package.json`文件的`scripts`属性中。

> *用你的 Github 用户名替换* ***你的 _ 用户名*** *。*

package.json

让我们运行这些脚本吧！

```
$ npm run proto:install && npm run proto:all
```

所以在这些步骤之后，我们的项目应该看起来像这样:

![](img/6ff80193fe22fe3d3dfb869106f9ee29.png)

太好了！现在让我们编码。

## 订单实体

像往常一样，我们从我们的实体开始。请记住，为了简单起见，每个订单只能存储一种产品。价格将是订购时产品的价格。

让我们给`src/order/order.entity.ts`添加一些代码

## 订单 DTO /验证

为了进行验证，我们创建了一个类来实现我们生成的 protobuf 文件的接口。数量至少需要为 1，我想这是不言自明的。

让我们给`src/order/order.dto.ts`添加一些代码

## 订单服务

订单服务有点不同，因为这是我们第一次从另一个微服务中调用一个微服务。我们将该产品称为微服务两次。首先，我们将检查该产品是否存在，然后，我们将减少该产品的库存来创建订单。

让我们把`src/order/order.service.ts`从

到

## 订单控制器

在最后一个控制器上，我们需要创建一个方法来接收来自 API 网关的传入请求。

让我们把`src/order/order.controller.ts`从

到

## 订单模块

由于我们要连接到产品微服务，我们需要注册一个客户端。

现在，让我们把`src/order/order.module.ts`从

到

## 应用模块

同样，我们需要连接到我们在本文第 1 部分中创建的数据库。

现在，让我们将`src/app.module.ts`从

到

## 引导程序

最后一次，我们需要在 NestJS 中初始化我们的微服务。

让我们把`main.ts`从

到

现在，我们可以运行这个微服务。

```
$ npm run start:dev
```

![](img/aa51c0a13c0957cceb51ba9d0c9e073d.png)

# 测试

现在让我们测试我们的端点。请记住，您需要运行所有 3 个微服务以及您的 API 网关。您可以使用软件如[邮差](https://www.postman.com/)、[失眠](https://insomnia.rest/)，或简单地通过[卷曲](https://curl.se/)命令在您的终端。

您已经创建了一个失眠文档，其中包含了您可以导入的所有端点。[这里的](https://api.npoint.io/a7914567b7584228cd7a)是 JSON 文件。

## 用户寄存器

```
$ curl -X POST http://localhost:3000/auth/register -H "Content-Type: application/json" -d '{"email": "elon@gmail.com", "password": "12345678"}'**Server Response:** { "status": 201 }
```

## 用户登录

```
$ curl -X PUT http://localhost:3000/auth/login -H "Content-Type: application/json" -d '{"email": "elon@gmail.com", "password": "12345678"}'**Server Response:** { "status": 200, "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwiZW1haWwiOiJoZWxsb2tldmludm9nZWxAZ21haWwuY29tIiwiaWF0IjoxNjQ3NTMzMjczLCJleHAiOjE2NzkwNjkyNzN9.w14jPT72_sfbdPIPXxEmSdopn8TXS-EDMJ3HalXT9Kw" }
```

## 创建产品

```
$ curl -X POST http://localhost:3000/product -H "Content-Type: application/json" -d '{"name": "Test A", "sku": "A00001", "price": 100, "stock": 5}'**Server Response:** { "status": 200, "id": 1 }
```

## 创建订单

```
$ curl -X POST http://localhost:3000/order -H "Content-Type: application/json" -d '{"productId": 1, "quantity": 1}'**Server Response:** { "status": 200, "id": 1 }
```

感谢阅读我的文章。我希望你能学到一些新的东西，了解 gRPC 背后的理念。如果你有任何问题让我知道。

干杯！

我希望你喜欢读这篇文章。如果你愿意支持我成为一名作家，考虑注册[成为一名媒体成员](https://medium.com/@hellokevinvogel/membership)。每月只需 5 美元，你就可以无限制地使用 Medium。

想支持我？[给我买杯咖啡。](https://www.buymeacoffee.com/hellokevinvogel)

# 阅读更多

[](/nestjs-microservices-with-grpc-api-gateway-and-authentication-part-1-2-650009c03686) [## NestJS:具有 gRPC、API 网关和认证的微服务—第 1/2 部分

### 分步指南:带有 gRPC、API 网关、认证和验证的 NestJS 应用程序

levelup.gitconnected.com](/nestjs-microservices-with-grpc-api-gateway-and-authentication-part-1-2-650009c03686) [](https://blog.bitsrc.io/solid-principles-in-typescript-153e6923ffdb) [## 带打字稿的固体原理(2022)

### TypeScript 对用 JavaScript 编写干净的代码产生了巨大的影响。但是总有办法…

blog.bitsrc.io](https://blog.bitsrc.io/solid-principles-in-typescript-153e6923ffdb) [](https://javascript.plainenglish.io/3-reasons-why-typescript-is-better-than-javascript-76443330f248) [## TypeScript 优于 JavaScript 的 3 个原因

### 是时候停止在 TypeScript 上使用 JavaScript 了

javascript.plainenglish.io](https://javascript.plainenglish.io/3-reasons-why-typescript-is-better-than-javascript-76443330f248) [](https://betterprogramming.pub/how-microsoft-wants-to-destroy-typescript-1f1a53b18de6) [## 微软如何摧毁 TypeScript

### 微软的提议将类型和关键字作为注释来处理

better 编程. pub](https://betterprogramming.pub/how-microsoft-wants-to-destroy-typescript-1f1a53b18de6)