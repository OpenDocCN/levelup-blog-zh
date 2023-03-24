# 采用 gRPC、API 网关和身份认证的微服务—第 2/2 部分

> 原文：<https://levelup.gitconnected.com/microservices-in-go-with-grpc-api-gateway-and-authentication-ba36cc32d167>

## 在 Go 中创建 3 个微服务和 1 个 API 网关(2022 年)

![](img/3da3f408220f9dd4edcc0f27414400c3.png)

这是我的文章系列[的第 2 部分，gRPC、API 网关和认证](/microservices-with-go-grpc-api-gateway-and-authentication-part-1-2-393ad9fc9d30)中的微服务。

让我们继续教程。

# 产品微服务(go-grpc-产品-服务)

github:[https://github.com/hellokvn/go-grpc-product-svc](https://github.com/hellokvn/go-grpc-product-svc)

这将是三个微服务中的第二个。在这里，我们将有三个特点。

*   创造产品
*   根据产品的 ID 查找产品
*   通过产品 ID 和订单 ID 减少产品库存

> ***请*** *到您终端里面的* `*go-grpc-product-svc*` *！*

## 初始化项目

```
$ go mod init github.com/YOUR_USERNAME/go-grpc-product-svc
```

## 安装模块

```
$ go get github.com/spf13/viper
$ go get google.golang.org/grpc
$ go get gorm.io/gorm
$ go get gorm.io/driver/postgres
```

## 项目结构

我们需要建立这个项目。与 API 网关本身相比，认证微服务将会很小。

**文件夹**

```
$ mkdir -p cmd pkg/config/envs pkg/db pkg/models pkg/pb pkg/services
```

**文件**

```
$ touch Makefile cmd/main.go pkg/config/envs/dev.env pkg/config/config.go$ touch pkg/pb/product.proto pkg/db/db.go pkg/models/stock_decrease_log.go pkg/models/product.go pkg/services/product.go
```

项目结构如下所示:

![](img/6d956b0aae101974da49cb1c0e3473df.png)

## 生成文件

现在，是时候再次编码了。像往常一样，我们从 Makefile 开始来缩短我们的命令。

让我们给`Makefile`添加代码

## 原型文件

要生成 protobuf 文件，我们需要一个产品原型文件。在这里，我们已经宣布了三个特点:`CreateProduct`、`FindOne`、`DecreaseStock`

让我们给`pkg/pb/product.proto`添加代码

## 生成 Protobuf 文件

现在，我们可以使用以下命令生成 protobuf 文件:

```
$ make proto
```

## 环境变量

像往常一样，我们需要定义环境变量。

让我们给`pkg/config/envs/dev.env`添加代码

## 配置

要加载这些环境变量，我们需要用 Viper 模块初始化它们。

让我们给`pkg/config/config.go`添加代码

## 库存减少日志模型

对了，这是唯一一个包含两个型号的微服务。为了幂等性，我们需要记录所有的股票下跌。

**什么是幂等？**

幂等性是一种属性，它保证对同一操作的重复调用不会导致服务状态的任何变化，也不会导致任何附加的副作用。

这意味着，我们需要确定，我们彼此只减少一次库存。想象一下，由于某种原因，我们意外地为同一订单减少了两次库存，这将导致不一致的数据。

让我们给`pkg/models/stock_decrease_log.go`添加代码

## 乘积模型

而且，我们需要一个产品模块本身。

让我们给`pkg/models/product.go`添加代码

## 数据库连接

同样，我们需要连接到我们在第一部分中创建的数据库。

让我们给`pkg/db/db.go`添加代码

## 产品服务

这里，我们需要处理所有传入的 gRPC 请求。从第 59 行开始的函数`DecreaseStock`很有趣，因为在那里，我们检查股票是否已经减少了一个特定的订单 ID，以保持数据一致，以防我们意外地调用这个端点两次。这在微服务领域是一个非常重要的话题。

让我们给`pkg/services/product.go`添加代码

## 主文件

最后但同样重要的是，我们需要引导应用程序。

让我们添加代码到`cmd/main.go`

现在我们可以用以下代码运行这个应用程序:

```
$ make server
```

# 订单微服务(go-grpc-订单-服务)

github:[https://github.com/hellokvn/go-grpc-order-svc](https://github.com/hellokvn/go-grpc-order-svc)

这将是三个微服务中的最后一个。在这里，我们将有一个特点。

*   按用户 ID 和产品 ID 创建订单

> ***请*** *前往* `*go-grpc-order-svc*` *内您的终端！*

## 初始化项目

```
$ go mod init github.com/YOUR_USERNAME/go-grpc-order-svc
```

## 安装模块

```
$ go get github.com/spf13/viper
$ go get google.golang.org/grpc
$ go get gorm.io/gorm
$ go get gorm.io/driver/postgres
```

## 项目结构

我们需要建立这个项目。与 API 网关本身相比，认证微服务将会很小。

**文件夹**

```
$ mkdir -p cmd pkg/config/envs pkg/client pkg/db pkg/models pkg/pb pkg/services
```

**文件**

```
$ touch Makefile cmd/main.go pkg/config/envs/dev.env pkg/config/config.go$ touch pkg/pb/product.proto pkg/pb/order.proto pkg/db/db.go pkg/models/order.go pkg/services/order.go pkg/client/product_client.go
```

项目结构如下所示:

![](img/c68941b99605243138c0fc87de9f6378.png)

## 制作文件

同样，我们需要编码一个 Makefile，这是我们需要的最后一个。

让我们给`Makefile`添加代码

## 订单原型文件

让我们给`pkg/pb/order.proto`添加代码

## 产品原型文件

这很特别，因为订单微服务包括来自产品微服务的产品原型。因为每当我们创建一个订单时，我们都需要调用产品微服务，原因有两个。

首先，我们需要检查一个产品是否真的存在。其次，我们需要根据订单请求的产品数量减少产品库存。

让我们给`pkg/pb/product.proto`添加代码

## 生成 Protobuf 文件

同样，让我们通过运行以下命令来增强两个 protobuf 文件:

```
$ make proto
```

#

## 环境文件

让我们给`pkg/config/envs/dev.env`添加代码

## 配置

让我们给`pkg/config/config.go`添加代码

## 订单模型

让我们给`pkg/models/order.go`添加代码

## 数据库连接

让我们给`pkg/db/db.go`添加代码

## 产品微服务客户端

我说过，我们需要连接到产品微服务。为此，我们需要创建一个客户端。

让我们给`pkg/client/product_client.go`添加代码

## 订单服务

让我们给`pkg/services/order.go`添加代码

## 主文件

最后，我们需要一个引导。

让我们给`cmd/main.go`添加代码

太好了！我们已经完成了所有微服务和 API 网关。让我们测试一切，但首先，您需要确保通过在每个项目中运行以下命令来运行 API 网关和所有三个微服务:

```
$ make server
```

# 测试所有端点

你可以用软件测试每一个端点，比如失眠症，Postman 或者简单地使用 cURL，我们在本教程中就是这么做的。

## 注册新用户

首先，我们需要创建一个用户。

```
$ curl --request POST \
  --url http://localhost:3000/auth/register \
  --header 'Content-Type: application/json' \
  --data '{
 "email": "elon@musk.com",
 "password": "12345678"
}'
```

## 注册

其次，我们需要登录，以便接收 JSON Web 令牌。

```
$ curl --request POST \
  --url [http://localhost:3000/auth/login](http://localhost:3000/auth/login) \
  --header 'Content-Type: application/json' \
  --data '{
 "email": "[elon@musk.com](mailto:elon@musk.com)",
 "password": "12345678"
}'
```

响应**很重要**，因为我们**需要**来进一步请求响应**令牌**。

**回应**

```
{
 "status": 200,
 "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2ODAxOTE2OTQsImlzcyI6ImdvLWdycGMtYXV0aC1zdmMiLCJJZCI6MiwiRW1haWwiOiJlbG9uQG11c2suY29tIn0.CfMns46bQuhVw_SBrKfRMFQx9MchlDWMxpEPP79wFzU"
}
```

## 创建产品

现在，我们需要将令牌添加到请求头来创建产品。

```
$ curl --request POST \
  --url http://localhost:3000/product \
  --header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2ODAxOTE2OTQsImlzcyI6ImdvLWdycGMtYXV0aC1zdmMiLCJJZCI6MiwiRW1haWwiOiJlbG9uQG11c2suY29tIn0.CfMns46bQuhVw_SBrKfRMFQx9MchlDWMxpEPP79wFzU' \
  --header 'Content-Type: application/json' \
  --data '{
 "name": "Product A",
 "stock": 5,
 "price": 15
}'
```

## 精品一品

要找到一个产品，我们需要在 URL 上添加产品的 ID。

```
$ curl --request GET \
  --url http://localhost:3000/product/1 \
  --header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2ODAxOTE2OTQsImlzcyI6ImdvLWdycGMtYXV0aC1zdmMiLCJJZCI6MiwiRW1haWwiOiJlbG9uQG11c2suY29tIn0.CfMns46bQuhVw_SBrKfRMFQx9MchlDWMxpEPP79wFzU'
```

## 创建订单

要创建订单，我们需要添加产品 ID 和数量。

```
$ curl --request POST \
  --url http://localhost:3000/order \
  --header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE2ODAxOTE2OTQsImlzcyI6ImdvLWdycGMtYXV0aC1zdmMiLCJJZCI6MiwiRW1haWwiOiJlbG9uQG11c2suY29tIn0.CfMns46bQuhVw_SBrKfRMFQx9MchlDWMxpEPP79wFzU' \
  --header 'Content-Type: application/json' \
  --data '{
 "productId": 1,
 "quantity": 1
}'
```

**恭喜恭喜！**

感谢您阅读我关于如何在 Go 中开发微服务的文章系列的第二部分。我希望你能学到一些新东西。

干杯！

我希望你喜欢读这篇文章。如果你愿意支持我成为一名作家，可以考虑注册[成为一名媒体成员](https://medium.com/@hellokevinvogel/membership)。每月只需 5 美元，你就可以无限制地使用 Medium。

想支持我？给我买杯咖啡。

# 接下来阅读

[](/nestjs-microservices-with-grpc-api-gateway-and-authentication-part-1-2-650009c03686) [## 带有 gRPC、API 网关和身份验证的 TypeScript 中的微服务

### 分步指南:带有类型脚本、gRPC、API 网关、身份验证和验证的 NestJS 应用程序

levelup.gitconnected.com](/nestjs-microservices-with-grpc-api-gateway-and-authentication-part-1-2-650009c03686) [](/transactions-in-relational-databases-with-postgres-examples-256abc44f0b9) [## 关系数据库中的事务与 Postgres 示例

### 什么是数据库中的事务以及如何使用它们！

levelup.gitconnected.com](/transactions-in-relational-databases-with-postgres-examples-256abc44f0b9) [](/devops-why-you-should-use-terraform-667f0411e383) [## 为什么您应该使用 Terraform

### DevOps:什么是 Terraform，为什么它这么好

levelup.gitconnected.com](/devops-why-you-should-use-terraform-667f0411e383)