# 采用 gRPC、API 网关和身份认证的微服务—第 1/2 部分

> 原文：<https://levelup.gitconnected.com/microservices-with-go-grpc-api-gateway-and-authentication-part-1-2-393ad9fc9d30>

## 在 Go 中创建 3 个微服务和 1 个 API 网关(2022 年)

![](img/5a27f41f61bb3fab435bc15ea973aba1.png)

我们将开发 3 个微服务和 1 个 API 网关来处理传入的 HTTP 请求。gRPC 会将 HTTP 请求转发给这些微服务。此外，我们处理 JWT 认证。

本文将分为两部分。点击[此处](/microservices-in-go-with-grpc-api-gateway-and-authentication-ba36cc32d167)进入第二部分。

# 我们要建造什么？

我们打算建立一个小型的电子商务系统。我们开发的项目和我在我的[微服务和 TypeScript](/nestjs-microservices-with-grpc-api-gateway-and-authentication-part-1-2-650009c03686) 系列文章中开发的项目一样。唯一的区别是，我们将在 Go 中对所有内容进行编码，因此，可能会有一些自然的差异。

![](img/d1e58f0d3e4defd0bc5a2b915b4a6148.png)

# 应用基础设施

本系列文章由两部分组成:

1.  **API 网关:**处理传入的 HTTP 请求
2.  **认证服务:**提供**注册**、**登录**等功能，并由 JWT 生成**令牌**
3.  **产品服务:**提供**添加产品**、**减少库存**、**查找产品**等功能
4.  **订单服务:**我们在这个微服务中提供的唯一特性是**创建订单**

每个微服务将是一个独立的项目。

由于这个系列文章已经花了很多阅读时间，我想保持这些微服务简单。所以我们不会处理 Docker、截止日期或超时。

在这一部分的最后，我们可以使用 1 个 API 网关和 1 个微服务来创建一个新用户。但是需要对 Go 和 gRPC 有一个比较好的理解。

我们开始吧！

# 创建数据库

```
$ psql postgres
$ CREATE DATABASE auth_svc;
$ CREATE DATABASE order_svc;
$ CREATE DATABASE product_svc;
$ \l
$ \q
```

这就是`\l`应该有的样子。如您所见，我们已经创建了 3 个数据库。

![](img/44c74e59d0ce424ebf93c0054f835c24.png)

# 创建项目

首先，我们需要创建我们的项目。我建议创建一个工作空间目录来存储您的 Go 项目。入口目录由您决定。

```
$ mkdir go-grpc-project
$ cd go-grpc-project
$ mkdir go-grpc-api-gateway go-grpc-auth-svc go-grpc-order-svc go-grpc-product-svc
```

我们将从 API 网关开始。

```
$ cd go-grpc-api-gateway
```

# API 网关

github:[https://github.com/hellokvn/go-grpc-api-gateway](https://github.com/hellokvn/go-grpc-api-gateway)

从 API 网关开始可能会很无聊，但实际上，这是有意义的，因为我们可以在完成后立即结合 API 网关测试每个微服务。

## 初始化项目

```
$ go mod init github.com/YOUR_USERNAME/go-grpc-api-gateway
```

## 安装模块

```
$ go get github.com/gin-gonic/gin
$ go get github.com/spf13/viper
$ go get google.golang.org/grpc
```

## 项目结构

我们需要建立这个项目。我更喜欢一开始就创建我们需要的所有文件夹和文件。所以要小心，那是很多的。

**文件夹**

```
$ mkdir -p cmd pkg/config/envs pkg/auth/pb pkg/auth/routes pkg/order/pb pkg/order/routes pkg/product/pb pkg/product/routes
```

**文件**

```
$ touch Makefile cmd/main.go pkg/config/envs/dev.env pkg/config/config.go$ touch pkg/auth/pb/auth.proto pkg/auth/routes/login.go pkg/auth/routes/register.go pkg/auth/client.go pkg/auth/middleware.go pkg/auth/routes.go$ touch pkg/product/pb/product.proto pkg/product/routes/create_product.go pkg/product/routes/find_one.go pkg/product/client.go pkg/product/routes.go$ touch pkg/order/pb/order.proto pkg/order/routes/create_order.go pkg/order/client.go pkg/product/routes.go
```

**项目结构**现在应该是这样的:

![](img/6763b9e220251a31146b816b5d10e649.png)

现在，让我们编码。

# Protobuf 文件

首先，我们需要添加所有三个 Protobuf 文件，每个微服务一个。

## 授权微服务的原型

我们要构建的第一个 protobuf 文件是用于认证微服务的。如您所见，我们将定义三个端点

*   注册
*   注册
*   验证(JSON Web 令牌)

让我们给`pkg/auth/pb/auth.proto`添加代码

## 订单微服务的原型

订单微服务将只处理一个任务，即创建订单。为此，我们需要产品 ID(我们稍后会得到)、数量和用户 ID。

让我们给`pkg/order/pb/order.proto`添加代码

## 产品微服务的原型

与订单微服务沟通的准备工作已经完成，现在需要为产品微服务做同样的工作。

## 原型文件

这一次，我们预计有三个终点

*   创建产品
*   找到一种产品
*   减少产品库存

让我们添加代码到`pkg/product/pb/product.proto`

# 生成文件

接下来，我们接触 Makefile。这里，我们添加两个命令来执行其他命令。听起来可能很奇怪，但可以这么说，这是一条捷径。

因此，我们不再每次都键入`protoc pkg/**…`，而是键入`make proto`。

让我们给`Makefile`添加代码

现在，我们可以基于刚刚创建的 proto 文件生成 protobuf 文件。

```
$ make proto
```

控制台中的输出应该非常简单，如下所示:

![](img/af5a6610c5a05d396c2315bb28348f31.png)

protobuf 文件将在每个`.proto`文件旁边生成。像这样:

![](img/2083de5d3c643622be238024098a59b7.png)

## 环境变量

我们需要定义一些环境变量。

让我们给`pkg/config/envs/dev.env`添加代码

## 配置

在这个文件中，我们将数据从环境文件中提取到 API 网关中。

让我们给`pkg/config/config.go`添加代码

API 网关的一般配置已经完成。现在我们需要对稍后将要编码的三个微服务的客户端进行编码。

# 授权微服务的端点

现在，我们将对尚不存在的 Auth 微服务的实现进行编码。但是因为我们在这里定义了 protobuf 文件，所以我们知道每个微服务的请求和响应是什么样子的。

## 注册路线

如果用户想要注册一个帐户，他将向我们的 API 网关发送一个请求，在这里，我们得到这个请求，并将其转发给认证微服务。这个微服务会返回一个响应。同样，我们还没有对认证微服务进行编码，但是我们知道这个微服务对数据的期望以及它将返回什么作为响应。

这里发生的事情是，我们创建一个名为`RegisterRequestBody`的结构，在这里我们绑定 HTTP 请求体，然后我们将它绑定到将要发出的 gRPC 请求的请求体。

让我们给`pkg/auth/routes/register.go`添加代码

> ***小心！*** *在大多数 Go 文件中，你需要用你的项目名替换项目名。在这里，您可以在第 8 行看到我的模块名。更换它。*

## 登录路线

登录路径与注册路径非常相似。首先，我们绑定 HTTP 请求主体，然后我们将这个主体发送给认证微服务。

微服务的响应将是 JSON Web Token，这个 Token 是我们即将编码的其他路由所需要的。

让我们添加代码到`pkg/auth/routes/login.go`

## 认证微服务客户端

为了与认证微服务通信，我们需要拨打它。请记住，我们将这个微服务的 URL 保存在我们的环境文件中，我们用`config.go`文件对它进行了初始化。现在，我们可以访问这些数据。

让我们添加代码到`pkg/auth/client.go`

## 认证中间件

我们需要保护产品和订购微服务免受未经授权的请求。这意味着，在某些路线上，我们只允许登录用户访问我们受保护的微服务。

所以我们在这里做的比看起来要简单。我们从 HTTP 请求头中获取 JSON Web 令牌，然后，我们通过认证微服务**验证**这个令牌。记住，我们之前在`auth.proto`文件中定义了一个`validate`端点。

如果令牌有效，我们将让请求通过。否则，我们将抛出一个未经授权的 HTTP 错误。

让我们给`pkg/auth/middleware.go`添加代码

## 初始化路线

为了能够到达我们刚刚编码的路线，我们需要注册它们。

让我们给`pkg/auth/routes.go`添加代码

# 订单微服务的端点

所以现在我们需要为订单微服务做同样的事情。

## 创建订单路线

这个路由再次类似于我们之前编码的注册和登录路由。获取 HTTP 请求体，并将此数据转发给订单微服务。

让我们给`pkg/order/routes/create_order.go`添加代码

## 订单微服务客户端

订单微服务也需要有一个客户端。

让我们给`pkg/order/client.go`添加代码

## 初始化路线

为了能够到达我们刚刚编码的路线，我们需要注册它们。

让我们给`pkg/order/routes.go`添加代码

# 产品微服务的端点

## 创建产品路线

这条路线与之前的路线相似。

让我们给`pkg/product/routes/create_product.go`添加代码

## 找到一条路线

这是我们第一次得到一条路线的参数。我们很快就在 URL 中定义了这个参数。但是在这里，我们得到了这个名为`id`的参数，它将从一个字符串转换为一个整数，因为我们在`product.proto`中将它定义为一个整数。

让我们给`pkg/product/routes/find_one.go`添加代码

因此，我们没有为我们在`product.proto`中定义的`DecreaseStock`端点创建路由。这是因为这个端点不能从 API 网关直接到达。我们只能从我们将在本系列文章的第 2 部分中编写的订单微服务到达这个特定的端点。

## 产品微服务客户端

同样，我们需要定义与产品微服务通信的客户端。

让我们给`pkg/product/client.go`添加代码

## 初始化路线

同样，我们需要注册刚刚创建的路由。

让我们给`pkg/product/register.go`添加代码

## 主文件

最后但同样重要的是，我们需要引导应用程序。记住，我们注册了路由，所以现在我们需要在启动应用程序时调用这些寄存器。

让我们给`cmd/main.go`添加代码

就是这样！**API 网关完成了！**

```
$ make server
```

终端中的输出应该是这样的:

![](img/1d5cdc43cf0b9b37938959b6793ccf35.png)

我们继续第一个微服务。

# 授权微服务(go-grpc-auth-svc)

github:[https://github.com/hellokvn/go-grpc-auth-svc](https://github.com/hellokvn/go-grpc-auth-svc)

这将是我们将要编码的三个微服务中的第一个。我知道，这篇文章已经很长很费时间了，所以我尽量保持简单。有些事情，比如我们如何处理环境文件，将是相似的。

> ***请*** *到* `*go-grpc-auth-svc*` *你的终端里面去！*

## 初始化项目

```
$ go mod init github.com/YOUR_USERNAME/go-grpc-auth-svc
```

## 安装模块

```
$ go get github.com/spf13/viper
$ go get google.golang.org/grpc
$ go get gorm.io/gorm
$ go get gorm.io/driver/postgres
$ go get golang.org/x/crypto/bcrypt
$ go get github.com/golang-jwt/jwt
```

## 项目结构

我们需要建立这个项目。与 API 网关本身相比，认证微服务将会很小。

**文件夹**

```
$ mkdir -p cmd pkg/config/envs pkg/db pkg/models pkg/pb pkg/services pkg/utils
```

**文件**

```
$ touch Makefile cmd/main.go pkg/config/envs/dev.env pkg/config/config.go$ touch pkg/pb/auth.proto pkg/db/db.go pkg/models/auth.go pkg/services/auth.go pkg/utils/hash.go pkg/utils/jwt.go
```

项目结构如下所示:

![](img/f70df612a66accad620be70166004573.png)

## 生成文件

这里，我们需要一个`Makefile`来缩短我们的命令。

让我们给`Makefile`添加代码

## 原型文件

我们在两端都需要相同的 proto 文件，在微服务端和 API 网关端。

让我们给`pkg/pb/auth.proto`添加代码

## 生成 Protobuf 文件

我们需要生成 protobuf 文件。

```
$ make proto
```

## 环境变量

这里我们需要的变量当然是 gRPC 服务器的端口，然后我们需要数据库的 URL，我们需要 JWT 的密钥。

让我们给`pkg/config/envs/dev.env`添加代码

## 配置

现在，我们需要为这个微服务创建一个`config.g` o 文件。

让我们添加代码到`pkg/config/config.go`

## 数据库模型

在这个模型中，我们在我们的`auth_svc`数据库中创建一个表。记住，我们在 PostgreSQL 中创建了一个名为`auth_svc`的数据库。

让我们给`pkg/models/auth.go`添加代码

## 数据库连接

在这里，我们连接到这个数据库。

一旦我们启动这个应用程序，`db.AutoMigrate`函数将自动为我们创建表格。

让我们给`pkg/db/db.go`添加代码

## 哈希助手

在这个文件中，我们有两个用 bcrypt 编码密码的函数，然后我们还有一个验证函数。

让我们给`pkg/utils/hash.go`添加代码

## JWT 助手

在这个助手中，我们基于我们在前面的`dev.env`文件中定义的密钥来生成和验证 JSON Web 令牌。

让我们给`pkg/utils/jwt.go`添加代码

## 授权服务

在这里，我们将对 Auth 微服务的业务逻辑进行编码。记住，我们在 API 网关中创建的认证路由会将请求转发到这个文件。

让我们给`pkg/services/auth.go`添加代码

## 主文件

最后但同样重要的是，我们需要引导这个微服务。

让我们给`cmd/main.go`添加代码

就这样，我们终于完成了第 1 部分，包括 API 网关和第一个用于身份验证的微服务。

现在，我们可以生成 protobuf 文件并运行服务器。

```
$ make server
```

控制台中的输出如下所示:

![](img/69ada6532c01b20f6f5354a371ade994.png)

# 测试授权微服务和 API 网关

现在，我们可以向 API 网关发送两个 HTTP 请求。确保两个应用程序都已启动并正在运行。端口 3000 上的 API 网关和端口 52001 上的身份验证微服务。

## 注册新用户

```
curl --request POST \
  --url http://localhost:3000/auth/register \
  --header 'Content-Type: application/json' \
  --data '{
 "email": "elon@musk.com",
 "password": "1234567"
}'
```

## 登录用户

```
curl --request POST \
  --url [http://localhost:3000/auth/login](http://localhost:3000/auth/login) \
  --header 'Content-Type: application/json' \
  --data '{
 "email": "[elon@musk.com](mailto:elon@musk.com)",
 "password": "1234567"
}'
```

对这个请求的响应非常重要，因为这个请求将在我们完成了本系列文章第 2 部分中的另一个微服务之后，立即返回我们需要的 JSON Web 令牌。

**响应**将如下所示:

```
{
 "status": 200,
 "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2ODAxMTk5NzAsImlzcyI6ImdvLWdycGMtYXV0aC1zdmMiLCJJZCI6MiwiRW1haWwiOiJlbG9uQG11c2suY29tIn0.oDirdoryUDBaOtbAUzVgtwTeakNi9SptPyW7ftobdrI"
}
```

**第二部分:**

[](/microservices-in-go-with-grpc-api-gateway-and-authentication-ba36cc32d167) [## 采用 gRPC、API 网关和身份验证的微服务

### 在 Go 中创建 3 个微服务和 1 个 API 网关的第 2 部分

levelup.gitconnected.com](/microservices-in-go-with-grpc-api-gateway-and-authentication-ba36cc32d167) 

干杯！

我希望你喜欢读这篇文章。如果你愿意支持我成为一名作家，可以考虑注册[成为一名媒体成员](https://medium.com/@hellokevinvogel/membership)。每月只需 5 美元，你就可以无限制地使用 Medium。

想支持我？给我买杯咖啡。

# 分级编码

感谢您成为我们社区的一员！升级正在改变技术招聘。 [**在最好的公司**找到你最完美的工作](https://jobs.levelup.dev/talent) **。**

[](https://jobs.levelup.dev/talent) [## 提升——改变招聘流程

### 🔥让软件工程师找到他们热爱的完美角色🧠寻找人才是最痛苦的部分…

作业. levelup.dev](https://jobs.levelup.dev/talent)