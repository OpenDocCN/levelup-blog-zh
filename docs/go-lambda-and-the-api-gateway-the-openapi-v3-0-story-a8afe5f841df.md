# Go、Lambda 和 API 网关 OpenAPI v3.0 的故事

> 原文：<https://levelup.gitconnected.com/go-lambda-and-the-api-gateway-the-openapi-v3-0-story-a8afe5f841df>

![](img/588c04bf2b54f1d2888b1007542b8089.png)

[维克托·卢](https://unsplash.com/@cazat69?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在[之前的一篇文章](https://medium.com/me/stats/post/b9c0b8c5712b)中，我描述了广泛使用的 [go-swagger](https://github.com/go-swagger/go-swagger) 模块如何在 Lambda/API 网关环境中使用。go-swagger 对此解决方案的一个限制是，它只支持[Swagger/open API v 2.0](https://swagger.io/specification/v2/)(open API 规范 2—OAS 2)；随着 [OpenAPI v3.0](https://swagger.io/specification/) (OpenAPI 规范 3 — OAS3)支持变得越来越重要，探索如何实现 OAS3 解决方案变得很有意思。一个具体的驱动因素是 [AWS API 网关 HTTP API](https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api.html) 仅[支持 OAS 3](https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-open-api.html)；不支持 OAS2。

因此，这篇文章考虑了以下几点:

*   如何使用 Go 实现 OAS3 API
*   如何使用 HTTP API 将其部署到 AWS

示例代码在[本 github 报告](https://github.com/seanrmurphy/lambda-openapi3-test)中提供。

**Go 支持 OAS3**

Go 生态系统包含许多为 OAS3 提供支持的项目。kin-openapi 项目提供了解析 OAS3 规范时可以使用的基本原语——因此，它是一个强大的工具箱，可以在其上开发其他 OAS3 解决方案。我选择了 [oapi-codegen](https://github.com/deepmap/oapi-codegen) 项目，因为它支持基于 OAS3 规范生成 OAS3 兼容的后端。

oapi-codegen 是一个相对较新的项目——它比 go-swagger 更轻量级，并且不为 OAS3 规范中定义的每个端点的有效返回代码生成特定的数据类型；焦点似乎更多地放在让某些东西工作上，即使没有提供所有的功能，并在必要时对其进行迭代。

oapi-codegen 在用法上也有很大的不同；与 go-swagger 不同，它不考虑生成的代码应该如何组织，只是将输出发送到 stdout 然后，开发人员必须选择它应该放在哪里。像 go-swagger 一样，它支持生成不同的输出——例如客户端、服务器存根和类型——尽管 oapi-codegen 与`go generate`工作流集成得更好，并且支持配置文件的定义，这些文件指定了生成的代码应该放在哪里。

与 go-swagger 不同，oapi-codegen 的请求验证没有那么全面；当前版本不支持 POST 主体内容的验证——它当然会执行数据的去 marshalling，从 eg 提供的 JSON 转换为 Go `struct`,但是如果输入数据中缺少一些字段，框架不会自动生成错误响应。(在[这个 PR](https://github.com/deepmap/oapi-codegen/pull/245) 中已经有关于这个问题的工作，可能很快会合并)。

oapi-codegen 支持与不同的、更现代的 Go HTTP 服务器集成；[呼应](https://github.com/labstack/echo)和[驰](https://github.com/go-chi/chi)，具体来说。

**在本地实施 OAS 3 API**

在下文中，我们将考虑一个简单的 OAS3 文件，它只在`/`上提供一个端点，并定义了一个模式。

```
openapi: 3.0.1
paths:
  /:
    get:
      operationId: getApiIdentifier
      produces:
      - application/json
      responses:
        '200':
          schema:
            $ref: '#/components/schemas/SimpleMessageResponse'components:
  schemas:
    SimpleMessageResponse:
      required:
      - message
      properties:
        message:
          type: string
```

使用 oapi-codegen，会生成以下代码，

```
// auto-generated code// SimpleMessageResponse defines model for SimpleMessageResponse.
type SimpleMessageResponse struct {
  Message string `json:"message"`
}type ServerInterface interface {
  // API Identifier endpoint
  // (GET /)
  GetApiIdentifier(ctx echo.Context) error
}
```

在上面自动生成的代码中，模型`SimpleMessageResponse`是从 OAS3 文件中定义的模型生成的；`ServerInterface`由 OAS3 文件中定义的路径生成——通过 OAS3 文件中定义的`operationId` 将`/`路径映射到被调用的`GetApiIdentifier`函数。

```
// code to provide API implementationtype Api struct {
}func NewApi() *Api {
  return &Api{}
}func (*Api) GetApiIdentifier(ctx echo.Context) error {
  str := "oapi-codegen Lambda integration API (OpenAPI v3 version) - version 1.0" r := SimpleMessageResponse{
    Message: str,
  } return ctx.JSON(http.StatusOK, r)
}
```

基本的 oapi-codegen 方法是，开发人员必须创建一个`struct` ，它提供自动生成的`interface`的实现。在上面的例子中，`Api`结构提供了这一点，而`GetApiIndentifier`只是返回一个带有 JSON 编码消息的 HTTP OK。

如下所示的`main()`函数可用于实例化 API 注意对`RegisterHandlers()`的调用将`Api` 结构中定义的函数链接到路由器中定义的相关路径。

```
func main() {
  a := api.NewApi() e = echo.New()
  api.RegisterHandlers(e, a) e.Logger.Fatal(e.Start(fmt.Sprintf("0.0.0.0:%d", 8080)))
}
```

这些部分可以放在一起实现一个简单的 OAS3 兼容 API。

请参见[示例报告](https://github.com/seanrmurphy/lambda-openapi3-test)中稍微复杂一点的示例。

**使用 HTTP API 将 API 部署到 AWS**

要将 API 作为 Lambda 函数部署到 AWS，必须修改代码库，以便它可以使用一个事件——触发 Lambda 函数的事件——并将其转换为 HTTP 请求，然后传递给 API 逻辑。OAS2 的实施也是如此；AWS 提供的 [Go API 代理模块可以用来做这个绑定。](https://github.com/awslabs/aws-lambda-go-api-proxy)

一个在这个特定案例中出现但在 OAS2 工作中没有出现的问题涉及到在使用[路径参数](https://swagger.io/docs/specification/describing-parameters/)的情况下从事件到 HTTP 请求的映射。更具体地说，在转换之后，HTTP 请求包含一个`Path`和一个`Resource`；`Path`包含完整的路径名，包括 API 网关中定义的阶段名，`Resource`包含端点名，包括参数名而不是参数值(例如`/endpoint/{param-name}`而不是`/endpoint/1234`，其中 1234 是参数值)。这两者都不是 API 所期望的；因此，需要一些小的字符串操作来合并`Path`和`Resource`以获得 API 期望的端点形式。

一旦构建了可执行文件，就可以将它部署到 AWS。和以前一样，我们使用 [SAM](https://aws.amazon.com/serverless/sam/) 进行部署，但是当然也可以使用 [CloudFormation](https://aws.amazon.com/cloudformation/) 。

需要对之前的案例进行一些小的修改:

*   API 类型必须从`[AWS::Serverless::Api](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-resource-api.html)`更改为`[AWS::Serverless::HttpApi](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-resource-httpapi.html)`，类似地，链接到 Lambda 函数的事件必须从`[Api](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-property-function-api.html)`事件更改为`[HttpApi](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-property-function-httpapi.html)`事件
*   OAS3 API 规范也必须稍加修改——与 REST API 变体的一个具体区别是, [HTTP API 要求需要定义一个](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-swagger-extensions-integration.html)`[payloadFormatVersion](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-swagger-extensions-integration.html)`,而这在 REST API 变体中是不必要的

通过这些适度的修改，使用 HTTP API 设计、实现和部署 OAS3 兼容的 API 到 AWS 成为可能。

如果你想亲自尝试一下，请查看 github repo 。