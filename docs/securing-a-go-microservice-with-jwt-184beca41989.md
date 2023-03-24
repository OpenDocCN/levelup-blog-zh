# 保护 JWT 的 Go 微服务

> 原文：<https://levelup.gitconnected.com/securing-a-go-microservice-with-jwt-184beca41989>

![](img/1d93e228862dbe59d376b9712fed36df.png)

# 保护 JWT 的 Go 微服务

JSON Web token(jwt)提供了一种机制，将一组声明或属性从客户端共享到服务器，服务器以加密的安全方式提供微服务。JWT 保护服务到服务的通信，还可以跨微服务传递最终用户上下文。

JWT 令牌可以用来携带调用微服务的身份，或者发起请求的客户端或系统的身份。它可用于在多个客户端和服务器之间传递授权和验证属性。使用这些属性可以保护微服务，并确保只有经过授权的访问才会发生。

在本帖中，我们将探讨 JWT 在 golang 微服务部署示例中保护服务对服务通信方面所扮演的角色。在 golang 编程生态系统中，有一个开源的`jwt-go`包使我们能够生成 JWT 令牌。

首先，我们将使用这个包来生成 JWT 令牌，并创建一个服务于该令牌的端点 API。您还可以使用诸如 FusionAuth 之类的认证系统来生成 JWT，但是看看 jwt 是如何在较低的级别上创建的会有所启发。

然后，我们将创建微服务服务器。将有一个授权中间件，它将在允许访问 golang 微服务之前执行。该中间件将接受 JWT 令牌并对其进行验证，以确保对微服务的授权访问。

所以，让我们开始吧！

# 先决条件

*   安装 [Golang 1.15.8](https://golang.org/)
*   基于 JWT 的认证的基础知识
*   golang 编程的基础知识

# 用 go 模块实现 JWT 端点

在本节中，我们将创建一个端点，该端点生成一个 JWT，然后将其返回给客户端。如果你想克隆它并跟随它，所有的代码都可以在 Github 上找到。

我们将使用 HMAC 算法来加密令牌。

首先，我们将从创建一个端点来发布 JWT 开始。为此，我们将使用 GitHub 的 [jwt-go 包。](https://github.com/dgrijalva/jwt-go)

在开始实现这个端点之前，我们需要首先创建一个 go 项目模块。让我们保持整洁，创建一个名为`jwt_client`的文件夹:

```
mkdir jwt_client && cd jwt_client
```

然后，创建 go 模块:

```
go mod init jwt_client
```

然后，我们需要创建一个名为`main.go`的文件。在其中，我们可以开始导入必要的包，如下面的代码片段所示:

```
package mainimport (
  "fmt"
  "time"
  jwt "github.com/dgrijalva/jwt-go"
)
```

# 设置共享密钥

接下来，我们定义一个签名密钥，如下所示。我们可以硬编码这个秘密:

```
var mySigningKey = []byte("unicorns")
```

但是为了增加安全性和灵活性，我们可以使用环境变量来存储密钥，而不是将它硬编码在应用程序中:

```
var mySigningKey = []byte(os.Getenv("SECRET_KEY"))
```

我们可以通过在运行 go 程序的终端中执行以下命令来设置这个环境变量:

```
set SECRET_KEY=unicorns // for windows
export SECRET_KEY=unicorns // for linux or mac
```

每当您启动一个新的终端并希望运行这段代码时，您都需要设置这个值。您还可以将这个环境变量添加到 shell 启动脚本中，以避免这种麻烦。

# 生成 JWT

为了生成令牌，我们需要创建一个名为`GetJWT`的函数。在函数中，我们首先使用 jwt 库提供的`New`方法初始化一个新的`JWT`实例。

我们需要在初始化令牌时将签名方法算法配置为`HS256`。支持多种不同的签名方法，但我们使用 HMAC，一种对称签名算法，因为它最容易实现。

然后，我们在`claims`地图中创建令牌有效负载；您可以按照自己的意愿进行定制，但是建议包括如图所示的标准声明`aud`、`iss`和`exp`。我们将通过适当地设置`exp`声明，将这个 JWT 设置为一分钟有效。

最后，我们用前面定义的`mySigningKey`变量的值对令牌进行签名。之后，我们从函数中返回`tokenString`。

下面是`GetJWT`函数的所有优点:

```
// ...
func GetJWT() (string, error) {
  token := jwt.New(jwt.SigningMethodHS256) claims := token.Claims.(jwt.MapClaims) claims["authorized"] = true
  claims["client"] = "Krissanawat"
  claims["aud"] = "billing.jwtgo.io"
  claims["iss"] = "jwtgo.io"
  claims["exp"] = time.Now().Add(time.Minute * 1).Unix() tokenString, err := token.SignedString(mySigningKey) if err != nil {
    fmt.Errorf("Something Went Wrong: %s", err.Error())
    return "", err
  } return tokenString, nil
}
// ...
```

# 建立 golang 流程为 JWT 服务

现在，让我们实际上提供这个令牌。我们首先导入额外的包，如`net/http`和`log`来创建服务器:

```
import (
  "fmt"
  "log"
  "net/http"
  "time" jwt "github.com/dgrijalva/jwt-go"
)
// ...
```

我们需要创建函数来处理对端口 8080 的 HTTP 请求，如下所示:

```
// ...
func Index(w http.ResponseWriter, r *http.Request) {
  validToken, err := GetJWT()
  fmt.Println(validToken)
  if err != nil {
    fmt.Println("Failed to generate token")
  } fmt.Fprintf(w, string(validToken))
}func handleRequests() {
  http.HandleFunc("/", Index) log.Fatal(http.ListenAndServe(":8080", nil))
}func main() {
    handleRequests()
}
```

整个 JWT 服务器如下所示:

```
package mainimport (
  "fmt"
  "log"
  "net/http"
  "os"
  "time" jwt "github.com/dgrijalva/jwt-go"
)
var mySigningKey = []byte(os.Getenv("SECRET_KEY"))func GetJWT() (string, error) {
  token := jwt.New(jwt.SigningMethodHS256) claims := token.Claims.(jwt.MapClaims) claims["authorized"] = true
  claims["client"] = "Krissanawat"
  claims["aud"] = "billing.jwtgo.io"
  claims["iss"] = "jwtgo.io"
  claims["exp"] = time.Now().Add(time.Minute * 1).Unix() tokenString, err := token.SignedString(mySigningKey) if err != nil {
    fmt.Errorf("Something Went Wrong: %s", err.Error())
    return "", err
  } return tokenString, nil
}func Index(w http.ResponseWriter, r *http.Request) {
  validToken, err := GetJWT()
  fmt.Println(validToken)
  if err != nil {
    fmt.Println("Failed to generate token")
  } fmt.Fprintf(w, string(validToken))
}func handleRequests() {
  http.HandleFunc("/", Index) log.Fatal(http.ListenAndServe(":8080", nil))
}func main() {
  handleRequests()
}
```

# 启动 go 服务器

现在，我们安装导入的软件包，并通过执行以下命令启动服务器:

```
go get
go run main.go
```

我们可以通过执行 curl 命令快速测试服务器是否返回令牌:

```
curl [http://localhost:8080](http://localhost:8080)
```

如果成功，我们将看到响应中的令牌字符串:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJiaWxsaW5nLmp3dGdvLmlvIiwiYXV0aG9yaXplZCI6dHJ1ZSwiY2xpZW50IjoiS3Jpc3NhbmF3YXQiLCJleHAiOjE2MTM1MDk1MDcsImlzcyI6Imp3dGdvLmlvIn0.t7qdqrpLk3nBOZFLBL_UOdciZ_rWei0rJg3tgyJ7cTw
```

现在，在下一部分中，我们将这个令牌发送给微服务。

# 实现一个简单的 API 网关来验证 JWT

在本节中，我们将验证 JWT。发现令牌有效后，客户端可以与受保护的内部服务进行交互。该服务器将充当客户端的 API 网关。

这里，我们首先在根项目中创建一个名为`api_gateway`的新文件夹。该文件夹将是`jwt_client`的同级文件夹。如果您仍然在`jwt_client`文件夹中，运行以下命令:

```
mkdir ../api_gateway
cd ../api_gateway
```

现在我们在`api_gateway`文件夹中。我们需要像前面一样，通过执行以下命令来创建一个 go 项目模块:

```
go mod init api_gateway
```

然后，我们需要在文件夹中创建另一个`main.go`文件。

在这个新的`main.go`文件中，我们需要像之前一样导入助手包:

```
package mainimport (
  "fmt"
  "log"
  "net/http" "github.com/dgrijalva/jwt-go"
)
```

# 设置签名密钥

我们还需要在这里访问我们的签名密钥:

```
var MySigningKey = (byte[])os.Getenv("SECRET_KEY")
```

该密钥将用于验证呈现给我们的微服务的 JWT。

# 创建中间件来拦截传入的请求并验证 JWT

分配密钥后，我们需要做的第一件事是创建一个中间件来拦截所有传入的请求。在允许请求进一步处理之前，它将检查是否提供了令牌。

下面是这个函数的框架:

```
// ...
func isAuthorized(endpoint func(http.ResponseWriter, *http.Request)) http.Handler {
  return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
    if r.Header["Token"] != nil {
      // TODO
    } else {
      fmt.Fprintf(w, "No Authorization Token provided")
    }
  })
}
// ...
```

接下来我们将为`//TODO`部分构建代码。

# 构建中间件 JWT 解析逻辑

我们需要解析令牌并检查凭证，以确定请求是否被授权。我们将验证令牌是否经过签名，签名方法是否正确，以及受众和发行者是否符合我们的预期。如果发生任何错误，我们将错误消息作为响应返回，而不是传递请求。

然而，在快乐路径中，我们让客户机请求传递到下一个端点。

下面是`//TODO`部分的代码:

```
token, err := jwt.Parse(r.Header["Token"][0], func(token *jwt.Token) (interface{}, error) {
  if _, ok := token.Method.(*jwt.SigningMethodHMAC); !ok {
    return nil, fmt.Errorf(("Invalid Signing Method"))
  }
  if _, ok := token.Claims.(jwt.Claims); !ok && !token.Valid {
    return nil, fmt.Errorf(("Expired token"))
  }
  aud := "billing.jwtgo.io"
  checkAudience := token.Claims.(jwt.MapClaims).VerifyAudience(aud, false)
  if !checkAudience {
    return nil, fmt.Errorf(("invalid aud"))
  }
  iss := "jwtgo.io"
  checkIss := token.Claims.(jwt.MapClaims).VerifyIssuer(iss, false)
  if !checkIss {
    return nil, fmt.Errorf(("invalid iss"))
  } return MySigningKey, nil
})
if err != nil {
  fmt.Fprintf(w, err.Error())
}if token.Valid {
  endpoint(w, r)
}
```

# 添加微服务端点

好的，那么`isAuthorized`通过验证令牌来防止对端点的未授权访问。但是它在保护什么呢？一旦客户的请求被验证，我们需要创建一个端点来返回*一些东西*。就这么办吧。

```
func index(w http.ResponseWriter, r *http.Request) {
  fmt.Fprintf(w, "Super Secret Information")
}
```

(您可以随意构建比上面更有用的东西，但是出于说明的目的，返回`Super Secret Information`足以显示端点通过检查 JWT 而受到保护。)

我们还需要在`main.go`的顶部导入服务器包，如下面的代码片段所示:

```
import (
  "fmt"
  "log"
  "net/http" "github.com/dgrijalva/jwt-go"
)
// ...
```

然后，我们需要用 jwt 中间件包装我们的索引端点。我们也将在端口 9001 上监听。

```
// ...
func handleRequests() {
  http.Handle("/", isAuthorized(index))
  log.Fatal(http.ListenAndServe(":9001", nil))
}
func main() {
  fmt.Println("server")
  handleRequests()
}
```

就这样，我们的受保护微服务已经准备就绪。`main.go`全部如下:

```
package mainimport (
  "fmt"
  "log"
  "net/http"
  "os" "github.com/dgrijalva/jwt-go"
)var MySigningKey = []byte(os.Getenv("SECRET_KEY"))func homePage(w http.ResponseWriter, r *http.Request) {
  fmt.Fprintf(w, "Super Secret Information")
}
func isAuthorized(endpoint func(http.ResponseWriter, *http.Request)) http.Handler {
  return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
    if r.Header["Token"] != nil { token, err := jwt.Parse(r.Header["Token"][0], func(token *jwt.Token) (interface{}, error) {
        if _, ok := token.Method.(*jwt.SigningMethodHMAC); !ok {
          return nil, fmt.Errorf(("Invalid Signing Method"))
        }
        aud := "billing.jwtgo.io"
        checkAudience := token.Claims.(jwt.MapClaims).VerifyAudience(aud, false)
        if !checkAudience {
          return nil, fmt.Errorf(("invalid aud"))
        }
        // verify iss claim
        iss := "jwtgo.io"
        checkIss := token.Claims.(jwt.MapClaims).VerifyIssuer(iss, false)
        if !checkIss {
          return nil, fmt.Errorf(("invalid iss"))
        } return MySigningKey, nil
      })
      if err != nil {
        fmt.Fprintf(w, err.Error())
      } if token.Valid {
        endpoint(w, r)
      } } else {
      fmt.Fprintf(w, "No Authorization Token provided")
    }
  })
}
func handleRequests() {
  http.Handle("/", isAuthorized(homePage))
  log.Fatal(http.ListenAndServe(":9001", nil))
}
func main() {
  fmt.Println("server")
  handleRequests()
}
```

# 启动微服务

我们需要通过执行以下命令来启动中间件和微服务服务器:

```
go run main.go
```

现在我们可以用 curl 测试端点。首先，尝试没有任何 JWT:

```
curl [http://localhost:9001](http://localhost:9001)
```

您将收到一条错误消息:

```
No Authorization Token provided
```

接下来，生成一个令牌:

```
curl [http://localhost:8080](http://localhost:8080)
```

您会看到类似这样的内容:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJiaWxsaW5nLmp3dGdvLmlvIiwiYXV0aG9yaXplZCI6dHJ1ZSwiY2xpZW50IjoiS3Jpc3NhbmF3YXQiLCJleHAiOjE2MTM1MDk1MDcsImlzcyI6Imp3dGdvLmlvIn0.t7qdqrpLk3nBOZFLBL_UOdciZ_rWei0rJg3tgyJ7cTw
```

现在我们可以构造 curl 命令，如下所示:

```
curl http://localhost:9001 --header 'Token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJiaWxsaW5nLmp3dGdvLmlvIiwiYXV0aG9yaXplZCI6dHJ1ZSwiY2xpZW50IjoiS3Jpc3NhbmF3YXQiLCJleHAiOjE2MTM1MDk1MDcsImlzcyI6Imp3dGdvLmlvIn0.t7qdqrpLk3nBOZFLBL_UOdciZ_rWei0rJg3tgyJ7cTw'
```

如果您在创建令牌后的一分钟内执行此请求，您将获得以下奖励:

```
Super Secret Information
```

如果时间太长，您会看到令牌过期警告。也没有超级机密信息。

# 结论

这篇文章展示了戈兰语中 JWT 的产生。它使用相同的令牌访问另一台服务器上运行的 golang 微服务。令牌生成使用了带有 HS256 签名方法的`go-jwt`模块。请求终结点后，生成的令牌被返回给客户端。

然后，对于微服务服务器，中间件逻辑验证令牌。成功验证后，请求被发送到微服务端点。

以这种方式使用令牌来保护微服务被广泛使用，因为它是一种安全且简单的机制。

# 更进一步

所有代码都可以在 [Github](https://github.com/FusionAuth/fusionauth-example-go-jwt-microservices) 上获得。如果您想尝试 jwt 和您在这里构建的 golang 微服务，您可以:

*   [在 5 分钟内设置 fusion auth](https://fusionauth.io/docs/v1/tech/5-minute-setup-guide/)并让它在用户登录时生成 JWTs。
*   了解如何通过 OAuth 保护 golang 应用程序。
*   修改中间件和 curl 脚本，使用更标准的`Authorization`头和`Bearer`令牌前缀。
*   使用非对称签名算法(如 RSA)来避免在两个程序之间共享秘密。
*   构建多个 golang 微服务，并通过 JWT`roles`声明的价值来控制服务访问。

编码快乐！

*最初发布于*[*https://fusion auth . io*](https://fusionauth.io/blog/2021/02/18/securing-golang-microservice/)*。*