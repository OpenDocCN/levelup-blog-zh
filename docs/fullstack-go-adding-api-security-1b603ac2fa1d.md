# Fullstack Go:添加 API 安全性

> 原文：<https://levelup.gitconnected.com/fullstack-go-adding-api-security-1b603ac2fa1d>

![](img/42b9b04420725c7c47d5a03bffb18e37.png)

由 [Siarhei Horbach](https://unsplash.com/@srhhrbch?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在[之前的一篇文章](https://medium.com/@sean_24982/go-frontend-backend-revisited-leveraging-swagger-on-the-frontend-caa60087d65b)中，我描述了如何将 [Swagger](https://swagger.io/) 支持添加到我的示例 Go fullstack 应用程序中。在这里，我描述了如何增强该解决方案以提供 API 安全性。

添加 API 安全性涉及完整的前端重写和对基于 [AWS API Gateway](https://aws.amazon.com/api-gateway/) 的后端的一些合理且有据可查的更改: [AWS Cognito](https://aws.amazon.com/cognito/) 被选为安全解决方案的基础，因为我已经在 [AWS](https://aws.amazon.com/) 生态系统中工作。

**支持 OAuth 流时 vecty 的限制**

之前的前端是用`[vecty](https://github.com/gopherjs/vecty)` Go 前端框架写的；这当然是一个很好的框架，证明了(对我来说)是一个很好的前端开发的切入点。`vecty`有许多积极的方面 DOM 元素的管理是高效的，组件模型是经过深思熟虑的，最终结果肯定是一种反应式的前端体验；然而，由于它的作者仍然认为它是试验性的，因此在某个时候限制必然会出现——我在试图增加安全性时遇到了一个重大的限制。

我想要使用的安全模型是一个标准的 [OAuth](https://oauth.net/2/) 授权授予模型，利用[代码交换的证明密钥(PKCE)](https://oauth.net/2/pkce/) 。PKCE 是为没有应用程序后端服务构成 OAuth 流程的情况而设计的；移动和网络应用是主要的用例。PKCE 流的基本特征是没有客户端秘密，因为客户端是不受信任的终端设备。PKCE 与众所周知的 OAuth grant 流程并无本质区别；相反，它是一种适应——它仍然需要登录到一个身份验证提供者，使用一个代码重定向到一个已知的回调 URI，然后该代码可用于获得一个可用于访问安全资源的访问令牌。

如上所述，PKCE 要求使用回调；在 PKCE 的情况下，假设这个回调是在客户端处理的——例如，对于移动应用，可以使用由设备操作系统处理的自定义 URI 方案(例如`myapp://`)。对于没有后端支持的 web 应用程序，这必须由前端路由器来处理。

目前，`vecty`还没有内置对前端路由的支持；`[vecty-router](https://github.com/marwan-at-work/vecty-router)`项目部分解决了这个问题，但是我遇到了当回调被触发时如何使用它来导航到另一个 URI 的问题——以前没有被渲染的组件将不能被正确渲染。为此，我开始寻找替代解决方案。

**输入 vugu…**

`[vugu](https://www.vugu.org/)` 是另一个前端 Go 框架，它受到了 [Vue.js](https://vuejs.org/) 的强烈启发，但是[主要由于 Go](https://blog.gopheracademy.com/advent-2019/writing-go-user-interfaces-with-vugu/) 的设计而有所不同。不像`vecty` , `vugu`文件包含 HTML、CSS 和 Go 的混合——这可能会让你的编辑器更加混乱，但确实会使应用程序逻辑的表示更加简洁。`vugu`获取应用程序的描述，并将其解析成一组本地 Go 文件，然后这些文件可以被编译成 [WebAssembly](https://webassembly.org/) 并在浏览器中运行。

`vugu`确实对路由提供了一些支持，尽管它还是相当新的:它包括一个层次模型，其中嵌套的组件可以根据浏览器中 URI 的变化进行更新:路由器还使浏览器 URI 能够被应用程序更新，这可以导致页面内容的重新呈现。

由于`vugu`具有很强的前端偏向性，它带有一个开发服务器，该服务器不执行任何显式的路由处理，而只是简单地提供相同的内容，而不管哪个端点被调用:因此开发服务器的所有路由处理都在前端执行。

**在 vugu 中实现 Todo 应用程序**

第一步是使用`vugu`重新实现基于 `[vecty](https://github.com/seanrmurphy/go-vecty-swagger)` [的](https://github.com/seanrmurphy/go-vecty-swagger)[待办事项应用](https://github.com/seanrmurphy/go-vecty-swagger)。总的来说，这非常简单；在`vugu`模型中，页面元素上的[动作可以直接链接到 Go 函数](https://www.vugu.org/doc/dom-events)，这使得实现比`vecty`中使用的方法更简单，后者使用动作、监听器和调度程序。

`vugu`有一些[好的原语](https://www.vugu.org/doc/files/markup)，使得有条件地显示一些 HTML 内容变得容易；还有一些很好的循环结构，可以很容易地显示项目列表——在这种情况下，这对于显示待办事项很有用。`vugu`的一个很好的设计特性是支持在渲染之前调用一个特定的函数(用`BeforeBuild()`)，这样在进入时间关键的渲染循环之前，数据可以被处理或者从服务器中检索。

使用`vugu` devserver 也是一种很好的体验，它增加了工作流的节奏——与其他前端框架一样，应用程序的更改会被 devserver 自动拾取，并在浏览器中重新加载以进行测试和验证。

**增加安全性—集成 OAuth 流程**

要使用 OAuth PKCE 流，必须向前端添加逻辑，以使用适当的查询参数导航到身份验证服务，并处理回调 URI。

PKCE 的工作原理是在前端创建一个所谓的*代码验证器*和相关的挑战，并将后者传递给认证服务器。认证后，认证服务器用一个代码重定向到回调。在我最初的尝试中，我试图使用`http://localhost:8844/callback`作为回调 URI，并将其重定向回主登录页面——然而`vugu`路由器仍在发展中，当呈现不同 URI 的内容时，我遇到了更改为新 URI 的问题。出于这个原因，我只是使用主登录页面作为回调 URI，并添加逻辑来解析返回的`code` 。然后，前端使用代码和代码验证器从认证服务获得访问令牌；然后在调用 API 时可以使用这个访问令牌。

```
func (c *Root) createCognitoURI(clientName string, p CognitoParameters) (u url.URL) {
  u = url.URL{
    Scheme: "https",
    Host:   clientName + ".auth.<AWS-REGION>.amazoncognito.com",
    Path:   "oauth2/authorize",
    Opaque: "//" + clientName + ".auth.<AWS-REGION>.amazoncognito.com/oauth2/authorize",
  }
  v, _ := query.Values(p)
  u.RawQuery = v.Encode()
  return
}func (c *Root) Login() {
  challenge := LoginData.CodeVerifier.CodeChallengeS256()
  challengeMethod := "S256" clientName := "<CLIENT-NAME>"
  clientID := "<CLIENT-ID>" p := CognitoParameters{
    ResponseType:        "code",
    ClientID:            clientID,
    RedirectURI:         "[http://localhost:8844](http://localhost:8844/callback)",
    State:               "initial-state",
    IdentityProvider:    "COGNITO",
    IDPProvider:         "",
    Scope:               "profile",
    CodeChallengeMethod: challengeMethod,
    CodeChallenge:       challenge,
  } q := c.createCognitoURI(clientName, p) window := js.Global().Get("window")
  window.Call("open", q.String(), "_self")
}
```

当前的解决方案是在登录页面上呈现一个登录按钮——单击该按钮会导致为认证服务器创建一个适当的 URI，其中包括查询参数，包括上面提到的质询、客户端 ID、回调 URI 和状态参数(参见代码片段)。登录后，浏览器以`code`为查询参数重定向回登陆页面；然后使用存储在前端应用程序中的代码获得访问令牌，并用于从安全 REST API 获得待办事项列表。

一个不明显的地方是，代码验证器必须在 OAuth 流中保持不变——它不能简单地存储在前端应用程序中，因为当控制权传递给身份验证服务器时，它会丢失。在这个实现中，这一点通过在导航到身份验证服务之前使用 LocalStorage 来存储代码验证器，然后在返回时再次访问它来解决。

**保护后端**

之前的不安全后端是使用触发 Lambda 函数的 [AWS API 网关](https://aws.amazon.com/api-gateway/)实现的——API 是在一个 Swagger 文件中定义的。AWS 提供了关于如何使用不同方法保护 Swagger 定义的 API 的[文档，其中一种方法是使用他们的 Cognito 服务。](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-integrate-with-cognito.html)

该方法包括以下基本步骤:

*   在 Cognito 中创建新的[用户池](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html)
*   添加一个特定于此前端的新客户端—请注意，客户端不能有密码，否则此客户端不支持 PKCE。
*   为此用户池添加执行身份验证所需的域。(显然，为此提供您自己的域名是可能的，但是这里我们假设使用一个带有 URI 的域名就足够了)。
*   将用户添加到用户池—这可以通过命令行或 web 界面来完成；请注意，可以在创建时配置用户，以便用户已经过验证
*   修改 Swagger 文件以包含安全定义，并将其应用于不同的端点，如这里的[所述](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-enable-cognito-user-pool.html)。(注意，我使用了 OpenAPI 2.0 的变化，因为 go-swagger 工具链关注于此)。
*   基于这个 Swagger 文件创建一个新的 REST API 它使用与不安全 API 定义相同的 AWS 命令，但是 Swagger 文件的内容指示 API 网关在创建 API 时添加授权码。

下面链接的 repo 中为此提供了一组 shell 脚本和一个 Swagger 文件模板。

然后可以很容易地测试 API:在没有访问令牌的情况下在 API 上使用`curl`会导致带有未授权响应的 HTTP 403。在形式为`"Authorization: $ACCESSTOKEN"`的头中添加一个由前端生成的访问令牌应该会导致对 API 的成功调用。

**从前端安全调用 API**

从前端调用安全 API 非常简单。如前一篇文章所述，从 Swagger 定义中生成客户端库并在前端使用它们没有出现任何问题。在这种情况下，客户端库与以前略有不同，唯一的区别是包含身份验证令牌的方式。使用客户端库只需要在调用后端时添加访问令牌——下面的代码展示了这是如何完成的。

```
// BearerToken creates the authentication header using a given
// token
func BearerToken(token string) runtime.ClientAuthInfoWriter {
 return runtime.ClientAuthInfoWriterFunc(func(r   runtime.ClientRequest, _ strfmt.Registry) error {
  return r.SetHeaderParam("Authorization", token)
 })
}// createClient creates a client to commmunicate with the REST
// API as necessary
func createClient() *client.SimpleTodoAPISecure {
  url, _ := url.Parse(AuthenticationData.RestEndpoint)
  authenticator := BearerToken(AuthenticationData.LoginData.ResponseParams.AccessToken) conf := client.Config{
    URL:      url,
    AuthInfo: authenticator,
  }
  return client.New(conf)
}// postItemToBackend posts a new Todo to the backend - note
// that the token is only used when creating the client above
func (c *ToDoList) postItemToBackend(t models.Todo) {
  backend := createClient() params := developers.NewAddTodoParams()
    params.Todo = &t
    ctx := context.TODO() if _, err := backend.Developers.AddTodo(ctx, params); err != nil {
    log.Printf("Error pusting new item on backend - error %v\n", err)
    return
  }
}
```

**结束语**

这项工作涉及到相当多的移动部件和从一个 Go 前端技术到另一个的移动；因此，我似乎遇到了现有前端 Go 框架的一些限制。也就是说，迁移到`vugu`极大地简化了前端实现。一般来说，PKCE 流的设置有点棘手，如果 Cognito 客户端包含客户端机密(这是默认设置),它就不能与 PKCE 一起使用，这一点并不明显，需要一些时间来解决。保护后端是有据可查的，而且相当简单，因为利用自动生成的 Swagger 代码来触发对后端的安全调用。

结果是一个用 Go 编写的简单应用程序，前端和后端都有身份验证和安全 API。当然，这只是一个玩具应用程序，但它确实展示了使某些东西更具实质性所必需的基本构件。

Github repo 包含的内容:

*   [https://github.com/seanrmurphy/vugu-tdl-swagger](https://github.com/seanrmurphy/vugu-tdl-swagger)