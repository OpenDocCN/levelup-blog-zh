# REST APIs 使用 go-swagger、Lambda 函数和 API 网关

> 原文：<https://levelup.gitconnected.com/rest-apis-using-go-swagger-lambda-functions-and-the-api-gateway-b9c0b8c5712b>

![](img/9687539ca682076be1a6af197ff527c7.png)

由[郑崔浩](https://unsplash.com/@hearten_jit?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

AWS 提供对 [Swagger/OpenAPI](https://swagger.io/docs/specification/about/) 定义的 REST APIs 以及 [go 运行时](https://docs.aws.amazon.com/lambda/latest/dg/golang-handler.html)的支持；然而，还不完全清楚如何最好地使用它们，特别是如果你习惯于在非 AWS 环境中使用 go-REST API 的 [go-swagger](https://github.com/go-swagger/go-swagger) 。在这里，我描述了 [API 网关](https://aws.amazon.com/api-gateway/)、go [Lambda](https://aws.amazon.com/lambda/) 函数和 go-swagger 模块如何协同工作。这里涉及两个具体问题:

*   如何在从 API 网关调用的 Lambda 函数中使用 go-swagger
*   从 API 网关的角度和 go-swagger 的角度来看，请求验证是如何执行的。

github repo 中提供了一个简单的例子，说明如何将这些技术结合使用。

**上下文**

Swagger/OpenAPI 已经成为用高级工具为许多语言和平台定义 API 的标准方法。在 go 上下文中，go-swagger 是广泛使用的 Swagger/OpenAPI 标准 v2.0 的成熟实现，它支持 REST API 的数据验证和封送，并提供从 API 定义派生的 go 数据模型。类似地，AWS API 网关支持创建在 Swagger/OpenAPI 文件(2.0 版和 3.0 版)中定义的 REST APIs:以这种方式创建 API 将在 API 网关中创建适当的端点和数据模型。当然，仍然有必要将它链接到 API 实现——在本例中是 Lambda 函数。

**Glue:链接 API 网关、Lambda 函数和 go-swagger**

在 Lambda 函数中使用 go-swagger 的基本变体非常简单，与非 Lambda 函数的上下文非常相似。API 端点、参数和模型在 Swagger 文件中定义，服务器 REST API 的代码由 go-swagger 工具生成；这就需要绑定到实际的实现中。

Lambda 函数的特殊之处在于，入口点现在是一个特定的处理程序，它将一个`APIGatewayProxyRequest`作为输入并返回一个`APIGatewayProxyResponse.` AWS 在其 [aws-lambda-go-api-proxy](https://github.com/awslabs/aws-lambda-go-api-proxy) 工具中提供了一个`httpadaptor`模块，该模块可以将这些 AWS 特定的数据类型映射到任何标准 go HTTP 服务器所期望的通用 HTTP 请求。这是支持 go-swagger 集成所必需的粘合剂。

下面显示了一个简化的变体，稍微复杂一点的示例在 [github repo](https://github.com/seanrmurphy/lambda-swagger-test) 中提供。

```
package mainimport (
...snip...
)var httpAdapter *httpadapter.HandlerAdapterfunc init() {
    swaggerSpec, err := loads.Embedded(restapi.SwaggerJSON,     restapi.FlatSwaggerJSON)
    if err != nil {
        panic(err)
     } api := operations.NewLambdaGoSwaggerTestAPIAPI(swaggerSpec)
    api.OpenGetAPIIdentifierHandler =     open.GetAPIIdentifierHandlerFunc(handlers.GetApiIdentifier)
    server := restapi.NewServer(api)
    server.ConfigureAPI() httpAdapter = httpadapter.New(server.GetHandler())
}// Handler handles API requests
func Handler(req events.APIGatewayProxyRequest) (events.APIGatewayProxyResponse, error) {
    return httpAdapter.Proxy(req)
}func main() {
    lambda.Start(Handler)
}
```

在上面的例子中，只有一个端点——`GetAPIIdentifier`——这与它在`handlers.GetApiIdentifier()`(不包括在上面)中的实现绑定在一起；这个绑定是通过设置`api.OpenGetAPIIndentifierHandler`变量来完成的。

与非 Lambda 函数的情况一样，`swagger.yaml` REST API 端点和 Go handler 函数之间的链接是通过`operationId`指定的，从下面的 swagger 定义片段中可以看出:

```
paths:
  /:
    get:
      tags:
      - open
      summary: API Identifier endpoint
      **operationId: getApiIdentifier**
      description:
        Endpoint which returns the API version and the running backend version
```

go-swagger 执行一些命名转换，以确保名称与 go 的典型 camelcase 命名兼容。

然后，创建 REST API 的工作流是使用 go-swagger 生成代码存根，并将它们绑定到实现 API 逻辑的函数，类似于上面所示；这就创建了将作为 Lambda 函数上传的可执行文件。API 网关配置和到 Lambda 函数的链接可以使用 [SAM](https://aws.amazon.com/serverless/sam/) 工具来完成，如 [github repo](https://github.com/seanrmurphy/lambda-swagger-test) 中所示——这提供了一种链接 API 定义(在 Swagger 文件中)、API 网关配置和 Lambda 函数的直接方式。当然，基于例如[云形成](https://aws.amazon.com/cloudformation/)的其他解决方案也可以用于此。

**请求验证**

以这种方式使用这些技术导致了一些功能重复的问题:go-swagger 自动生成的代码和 API 网关都执行请求验证。在 go-swagger 的情况下，当将数据映射到已定义的数据模型时，总是执行请求验证，如果请求不符合预期，就会产生错误；在 API 网关的情况下，它是可以启用的可选功能。

API 网关请求验证提供了两个基本功能:参数验证(主要确保查询参数存在)和请求主体验证(可用于确保 HTTP 请求的主体与特定的数据模型一致)；在 Swagger 上下文中，这个数据模型通常在 Swagger 文件中定义。这种验证可以通过 AWS 对 Swagger 文件的特定扩展来触发，如下面的代码片段所示。

```
swagger: '2.0'
# define two validators - basic and params-only
**x-amazon-apigateway-request-validators:
  basic:
    validateRequestBody: true
    validateRequestParameters: true
  params-only:
    validateRequestBody: false
    validateRequestParameters: true
# default is no validation
x-amazon-apigateway-request-validator: NONE**paths:
  /query-param/simple-response:
    get:
      produces:
      - application/json
      **x-amazon-apigateway-request-validator: params-only**
      parameters:
      - in: query
        name: query-param
        type: integer
        required: true /body-param/simple-response:
    post:
      produces:
      - application/json
      consumes:
      - application/json
 **x-amazon-apigateway-request-validator: basic**
      parameters:
      - in: body
        name: bodyParam
        schema:
          $ref: '#/definitions/InputObject'
        required: true
```

在上面的例子中，默认情况下没有验证——`NONE`验证器，但是可以执行`params-only` 或`body`验证，前者验证请求查询参数，后者验证主体内容。

使用 API 网关的请求验证功能的好处是，它可以减少 Lambda 函数调用的数量，因为无效的 REST API 调用可以被 API 网关本身阻止；最终，这可以降低成本。还值得注意的是，在 API 网关中发生的请求验证过程与 go-swagger 中的这种验证之间存在一些小的差异:go-swagger 中失败的验证返回一个 [HTTP 422(不可处理的实体)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/422)，而 API 网关生成的验证返回 [HTTP 400(错误请求)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/400)；此外，API Gateway 执行更轻量级的参数验证，只检查它们是否存在，而 go-swagger 执行适当的基于类型的验证。

更多细节请见本 [github repo](https://github.com/seanrmurphy/lambda-swagger-test) 。