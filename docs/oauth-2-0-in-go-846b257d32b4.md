# Go 中的 OAuth 2.0

> 原文：<https://levelup.gitconnected.com/oauth-2-0-in-go-846b257d32b4>

![](img/35ed5b97e46fc06a1d2d0920460cdd52.png)

吉祥物归功于 golang.org

在 Go 中访问安全的 HTTP 服务

OAuth 2.0 是第三方应用程序在云中访问用户数据的方式。规范是这样描述的:“OAuth 2.0 授权框架使第三方应用程序能够获得对 HTTP 服务的有限访问。”因此，你需要理解它，以编程方式访问现在存储在各种在线服务中的几乎所有用户数据:亚马逊、谷歌、GitHub、脸书、LinkedIn、Strava 和无数其他服务。在本文中，我们将在 Go 中构建一个 OAuth 2.0 客户端应用程序，并使用它在多个服务中访问我们的用户数据。

核心规范， [RFC 6749](https://tools.ietf.org/html/rfc6749) ，实际上可读性非常好，所以不要被大量的等宽文本吓倒。尽管要记住核心规范只是[旅程的开始](https://oauth.net/2/)，一些部分现在已经过时了，这些年来已经有了一些[的补充和澄清](https://www.oauth.com/oauth2-servers/map-oauth-2-0-specs/)。特别是现在，你会想要阅读本地应用的 RFC 8252 规范。

所有基于委员会的规范都有自己的行话，因此我们将首先深入其中，以设置上下文。

## 授权类型

核心规范概述了使用 OAuth 2.0 的五种不同方式，称为“授权类型”

*   [4.1](https://tools.ietf.org/html/rfc6749#section-4.1) 。授权码授予
*   [4.2](https://tools.ietf.org/html/rfc6749#section-4.2) 。隐性资助(现为遗产，不鼓励)
*   [4.3](https://tools.ietf.org/html/rfc6749#section-4.3) 。授予资源所有者口令身份证明(现在保留，不鼓励)
*   [4.4](https://tools.ietf.org/html/rfc6749#section-4.4) 。客户端凭据授予
*   [4.5](https://tools.ietf.org/html/rfc6749#section-4.5) 。推广赠款

此后的几个扩展现在是最佳实践:

*   [PKCE](https://tools.ietf.org/html/rfc7636) 授权码授予的扩展，已经成为所有公共应用的首选流程
*   [设备代码](https://tools.ietf.org/html/rfc8628#section-3.4)是没有浏览器的受限设备的扩展

## 客户端类型和配置文件

核心规范定义了两种不同类型的应用程序，机密应用程序和公共应用程序，主要区别在于公共应用程序无法保持其凭证的机密性(客户端机密)。在此基础上，它继续列出一些应用程序角色:

*   **Web 应用**:运行在 Web 服务器上的机密客户端。这是传统的基于服务器的 web 客户端访问用户数据，授权码授权是推荐的流程。授权服务器可以允许或不允许扩展授权在访问令牌到期后刷新它。客户端凭证授权对于客户端获取有关其自身的数据非常有用，与特定用户无关。在这种情况下，客户端必须能够仅使用它已经安全存储的凭证或者通过与授权服务器的某种预先安排来认证自己。
*   **基于用户代理的应用**:从 web 服务器下载代码和凭证的公共客户端。单页应用程序就属于这种角色。核心规范推荐这个角色的隐式流程，但是这已经被否决了，现在推荐使用 PKCE 的授权代码授权。
*   **原生应用**:在设备上安装并执行代码的公共客户端，应用可能安全也可能不安全，但被认为是不安全的。移动和本地应用程序都属于这一角色，再次建议使用 PKCE 授权码授权。对于无浏览器或输入受限的设备，建议使用设备代码流，其工作原理在概念上类似于授权代码授权，但要求用户在验证 URI(如令牌端点)手动输入用户代码(如客户端密码)以检索访问令牌。

## 角色

该规范确定了 OAuth 舞蹈中的几个角色，称为“[角色](https://tools.ietf.org/html/rfc6749#section-1.1)，它们对于保持流程顺畅非常重要。

*   **资源所有者**:拥有被访问数据的用户
*   **资源服务器**:提供用户数据的 api 服务器
*   **客户端**:使用 api 获取用户数据的第三方 app
*   **授权服务器**:代表资源所有者(用户)发放令牌以授权访问数据的服务。这可能与资源服务器共存，也可能不共存，这并不重要。
*   出于本文的目的，我将再添加一个人，创建第三方客户端的**开发人员**

![](img/acd78922dc703e8ea8636fc545165974.png)

## 端点

有几个“端点”需要建立。对你我来说，这些只是完成事情的 URI。客户端需要知道在哪里请求授权、令牌和刷新令牌。授权服务器需要知道将信息发送回客户端的位置。

*   **授权端点**:授权服务器上的 URI，提供授权和授权码
*   **令牌端点**:授权服务器上的 URI，它将短期授权码交换为长期访问令牌或刷新令牌
*   **重定向端点**:客户端上的 URI，从授权服务器接收令牌

## 授权码授予

在本文中，我们将重点关注授权代码授权。这一切都可以归结为一条简化的快乐之路来解锁我们的 api:

*   资源所有者(用户)在客户机上发起一个动作，向授权服务器请求授权
*   授权服务器验证资源所有者和访问范围
*   授权服务器向客户端发回一次性授权码
*   客户端将授权码换成一个具有过期时间的长期访问令牌
*   客户端检查到期时间，如果有效，则使用访问令牌通过 api 请求用户数据；如果到期，则客户端请求刷新令牌或重新进行身份验证。

具体来说，这具有以下模式:

![](img/f86cb720ec5c1fc7d16405576b070b85.png)

授权码授权和扩展授权流程

## 开发者

作为一名客户端开发人员，一开始您必须做几件事情:

*   在授权服务器上设置用户和应用程序帐户
*   输入申请信息
*   输入重定向 URI 和/或回拨域
*   获取客户 Id
*   获取客户端机密(PKCE 不需要)

这些信息建立了客户端和授权服务器之间的握手。

# 例子

那么这实际上是如何工作的呢？好吧，让我们假设您，开发人员，正在构建一个简单的 Web 客户端，并且您想通过 OAuth 2.0 访问用户数据。我们将使用 Strava OAuth 2.0 服务 api 作为一个例子来构建上面在 Go 中展示的整个流程。

## 第一步:帐户设置

首先，我们将设置我们的应用程序。在 Strava，这很简单:

1.  前往[Strava.com](https://www.strava.com/)，如果你还没有账户的话，建立一个账户。
2.  转到[https://www.strava.com/settings/profile](https://www.strava.com/settings/profile)
3.  单击我的 API 应用程序来设置您的应用程序:

*   遗憾的是，你需要上传一个[图标](https://www.cleanpng.com/png-go-programming-language-computer-programming-progr-1659140/download-png.html)来编辑设置
*   输入您的应用程序名称
*   选择一个类别
*   暂时忽略俱乐部
*   进入你的网站，为这个演示输入[**https://www.strava.testapp.com**](https://www.strava.testapp.com)
*   输入您的授权回调域，对于这个演示，输入 **localhost** (顺便说一下，localhost 总是有效的)。

请注意以下项目，您将在下面需要这些项目(您可以随时回来查看):

*   客户端 ID
*   客户机密

Strava 概要文件还显示了一个初始访问令牌和刷新令牌，您可以立即使用，但是它们只在几个小时内有效(注意过期时间)，所以我们将通过代码以正确的方式获取它们。

您可以扩展这个示例，在 [LinkedIn](https://www.linkedin.com/developers/) 、 [Spotify](https://developer.spotify.com/) 和 [GitHub](https://developer.github.com/program/) 中设置应用程序帐户。

## 步骤 2:请求授权

现在我们开始写代码。我们将组装一个简单的 web 客户端来演示从授权服务器的往返，然后扩展它。首先，我们需要在授权服务器上打开授权页面。我们将使用一个按钮来启动它，该按钮使用一个查询字符串在访问端点上执行 GET。

oclient/main.go:

```
package mainimport (
    "fmt"
    "html/template"
    "log"
    "net/http"
    "os"
    "path"
)func main() {
    port := os.Getenv("PORT")
    if port == "" {
        port = "8000"
    }http.HandleFunc("/", HomePageHandler)fmt.Println(">>>>>>> OClient started at:", port)
    log.Fatal(http.ListenAndServe(":"+port, nil))
    return
}func HomePageHandler(w http.ResponseWriter, r *http.Request) { fp := path.Join("templates", "home.html")
    tmpl, err := template.ParseFiles(fp)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    } if err := tmpl.Execute(w, nil); err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
    }
}
```

oclient/templates/home.html

```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>OClient</title>
    </head>
    <body>
        <h1>OClient</h1>
        <br> <button onclick="window.location.href='[https://www.strava.com/oauth/authorize?client_id=40022&response_type=code&redirect_uri='](https://www.strava.com/oauth/authorize?client_id=40022&response_type=code&redirect_uri=') + window.location.origin + '/redirect&approval_prompt=force&scope=activity:read_all&state=foo'">Authorize</button> </body>
</html>
```

编译并运行:

```
cd oclient
go install
oclient
```

在 localhost:8000 上打开浏览器

点击授权按钮

![](img/686b4fc1d7f46beb5b765596edfc25ca.png)

果然，显示了 Strava 授权页面。

![](img/96331e54c84d1b0117beb0055d2c7cf9.png)

让我们在 home.html 中分解查询字符串，注意其中一些是必需的，一些是可选的，一些是特定于服务的。

[](https://www.strava.com/oauth/authorize?client_id=40022&response_type=code&redirect_uri=')****这是 Strava 授权端点。[必需]****

****[**Client _ id = 40022**](https://www.strava.com/oauth/authorize?client_id=40022&response_type=code&redirect_uri=')这是 Strava 为应用程序提供的(我的)唯一客户端 Id，您的将会不同。我们真的不需要保护它，因为它只回复到我们注册的域和 redirect_uri(好吧，也许你可以在 localhost 上搞乱它——但我不在乎)。[必需]****

****[**response _ type = Code**](https://www.strava.com/oauth/authorize?client_id=40022&response_type=code&redirect_uri=')这告诉授权服务器我们正在请求授权代码授权。[必需]****

****[**redirect _ uri = '**](https://www.strava.com/oauth/authorize?client_id=40022&response_type=code&redirect_uri=')**+window . location . origin+'/redirect**这是我们的重定向端点。请注意，域必须与我们在上面的设置配置文件(localhost)中提供给 Strava 的相匹配—这就是为什么我们不关心这个查询字符串是公共的。所以这告诉授权服务器我们将在[http://localhost:8000/redirect 收到代码。](http://localhost:8000/redirect.)【可选，但大多数服务都需要】****

******approval_prompt=force** 这是谷歌特有的参数，其他人已经采用了。如果为“强制”，则用户每次访问应用程序时都会被提示进行授权，如果为“自动”，则只会在第一次时被提示。[可选，服务特定]****

******scope = activity:read _ all**这告诉授权服务器您请求的访问范围。有效负载是特定于供应商的，因为它表示特定的服务访问，但是查询是标准的。它是一系列以空格分隔、区分大小写的字符串。在我们的例子中，我们请求读取用户的 Strava 活动数据。[可选，但大多数服务都需要]****

******state=foo** 该值特定于客户端，对授权服务器不透明。它将被一字不差地发送回客户端的重定向端点。您可以使用它来维护初始客户端授权码请求和后续服务器回调之间的状态。还强烈建议使用 state 参数作为随机生成的“绑定值”，客户端可以用它来验证服务器的回调，以减少跨站点请求伪造(CSRF)。(我们稍后会这样做)[可选，但推荐]****

## ****步骤 3:接收授权码****

****现在让我们扩展我们的程序，从授权服务器接收授权代码(下面以粗体显示)。我们将在重定向 URI: /redirect 设置一个新的处理程序。****

****oclient/main.go****

```
**package mainimport (
    "fmt"
    "html/template"
    "log"
    "net/http"
  **  "net/url"**
    "os"
    "path"
)func main() {port := os.Getenv("PORT")
    if port == "" {
        port = "8000"
    }//pages
    http.HandleFunc("/", HomePageHandler)
    **http.HandleFunc("/redirect", RedirectHandler)**fmt.Println(">>>>>>> OClient started at:", port)
    log.Fatal(http.ListenAndServe(":"+port, nil))
    return
}func HomePageHandler(w http.ResponseWriter, r *http.Request) {
fp := path.Join("templates", "home.html")
    tmpl, err := template.ParseFiles(fp)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    } if err := tmpl.Execute(w, nil); err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
    }
}**func RedirectHandler(w http.ResponseWriter, r *http.Request) {
    m, err := url.ParseQuery(r.URL.RawQuery)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    code := m.Get("code")
    scope := m.Get("scope")
    fmt.Fprintln(w, "Code: ", code, " Scope: ", scope)
}****
```

****编译并再次运行它****

```
**go install
oclient**
```

****和以前一样，当您点击 OClient 中的 Authorize 按钮时，将会显示 Strava Authorize 页面。这一次单击 Strava 页面中的 Authorize 按钮。您应该看到授权代码和范围:****

```
**Code:  7160d4a21b495bf6e371db6d6aa85ca656c587be  Scope:  read,activity:read_all**
```

## ****步骤 4:用授权码交换访问令牌****

****我们现在有了授权码，但它只告诉我们授权发生了，我们不能用它来获取数据。我们必须用它来交换一个访问令牌，所以让我们通过向令牌端点发送一个 POST 来实现这一点。****

****oclient/main.go****

```
**package mainimport (
 **"bytes"
    "encoding/json"**
    "fmt"
    "html/template"
   ** "io/ioutil"**
    "log"
    "net/http"
    "net/url"
    "os"
    "path"
)func main() {
    port := os.Getenv("PORT")
    if port == "" {
        port = "8000"
    }
    http.HandleFunc("/", HomePageHandler)
    http.HandleFunc("/redirect", RedirectHandler)fmt.Println(">>>>>>> OClient started at:", port)
    log.Fatal(http.ListenAndServe(":"+port, nil))
    return
}func HomePageHandler(w http.ResponseWriter, r *http.Request) { fp := path.Join("templates", "home.html")
    tmpl, err := template.ParseFiles(fp)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    } if err := tmpl.Execute(w, nil); err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
    }
}func RedirectHandler(w http.ResponseWriter, r *http.Request) {
    m, err := url.ParseQuery(r.URL.RawQuery)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    code := m.Get("code")
    scope := m.Get("scope")
    fmt.Fprintln(w, "Code: ", code, " Scope: ", scope)
    **token := getAccessToken(w, r, code)
    fmt.Fprintln(w, "Token: ", token)**
}**const (
    ClientId      = "40022"
    ClientSecret  = "BlahBlahBlahBlahBlahBlahBlahBlahBlahBlah"
    TokenEndpoint = "**[**https://www.strava.com/oauth/token**](https://www.strava.com/oauth/token)**"
)****func getAccessToken(w http.ResponseWriter, r *http.Request, code string) (result string) {
    requestBody, err := json.Marshal(map[string]string{
        "client_id":     ClientId,
        "client_secret": ClientSecret,
        "code":          code,
        "grant_type":    "authorization_code",
    })
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
    }
    resp, err := http.Post(TokenEndpoint, "application/json",   bytes.NewBuffer(requestBody))
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
    }
    defer resp.Body.Close()** **body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
    }
    result = string(body)
    return
}****
```

****编译并再次运行它****

```
**go install
oclient**
```

****单击 OClient 和 Strava 授权按钮。现在，除了授权代码和范围之外，您还会看到访问令牌，类似于:****

```
**Token: {"token_type":"Bearer","expires_at":1594843861,"expires_in":19430,"refresh_token":"9ec0cedf070a8b3832f7be43b5acca8bc976deae","access_token":"a16bffde2871928071e98fbff5674b12d4fd0198","athlete":{"id":24336962,"username":null,"resource_state":2,"firstname":"Eric","lastname":"Lang","city":"Seattle","state":"Washington","country":"United States","sex":"M","premium":false,"summit":false,"created_at":"2017-08-19T18:38:37Z","updated_at":"2020-07-07T20:09:26Z","badge_type_id":0,"profile_medium":"https://dgalywyr863hv.cloudfront.net/pictures/athletes/23376952/7221532/1/medium.jpg","profile":"https://dgalywyr863hv.cloudfront.net/pictures/athletes/23376952/7221532/1/large.jpg","friend":null,"follower":null}}**
```

****这就是我们的情况。我们已经从授权服务器请求了一个授权码，在我们的重定向端点收到了授权码，并将其交换为一个访问令牌，我们现在可以用它来访问安全 API。****

****请注意，我们正在使用包含 client_id、client_secret、code 和 grant_type 的 JSON 有效负载向 Strava 令牌端点进行 POST。这安全吗？可以，只要客户端和认证服务器使用 TLS (HTTPS)。OAuth 的安全性已经过彻底审查，并且[已记录](https://tools.ietf.org/html/rfc6819)。现在，您可以看到我们将 client_id 和 client_secret 放在源代码中，这不是一个好主意，在实际系统中，您会将它们放在环境变量中，就像上面对“PORT”所做的那样。我们将在下一个版本中这样做。****

****授权码是一次性使用的代码，现在已被“使用”，如果您再次尝试使用，您会发现它已过期。要获得新的访问令牌，您要么必须从头开始重新授权，要么获得刷新令牌。****

****我们现在可以使用不记名令牌来直接访问 Strava api 并获取用户信息。例如用简单的卷曲命令。****

```
**curl -X GET https://www.strava.com/api/v3/athlete -H 'Authorization: Bearer a16bffde2871928071e98fbff5674b12d4fd0198'{"id":23318762,"username":null,"resource_state":2,"firstname":"Eric","lastname":"Lang","city":"Seattle","state":"Washington","country":"United States","sex":"M","premium":false,"summit":false,"created_at":"2017-08-19T18:38:37Z","updated_at":"2020-07-25T14:51:31Z","badge_type_id":0,"profile_medium":"https://dgalywyr863hv.cloudfront.net/pictures/athletes/23376952/7221532/1/medium.jpg","profile":"https://dgalywyr863hv.cloudfront.net/pictures/athletes/23376952/7221532/1/large.jpg","friend":null,"follower":null}**
```

****Done，那就是基本 OAuth 2.0 授权码授予的全端-端。尽管我们还没有解决令牌更新、PKCE 或服务之间的一些细微差别。我们现在就去做。****

# ****OAuth 2.0 客户端包****

****现在我们将把这个简单的例子扩展成一个通用工具，我们可以用它来访问任何 OAuth 2.0 服务。我们将首先构建一些我们需要的助手。****

****我们希望我们的库支持任何和多个 api 服务器，所以让我们建立一个配置文件来保存我们的服务数据。请注意，实际的 client_id 和 client_secret 将以文件中给出的名称从环境变量中加载，我们不会将它们存储在普通的源文件中。您可以扩展该文件以包含任何新的 OAuth 2.0 服务。我在 Strava、LinkedIn、Spotify 和 GitHub 上设置了 api 帐户来测试代码。****

****oclient/oclient/services.json****

```
**{
    "strava": {
        "client_id":          "STRAVA_CLIENT_ID",                                    
        "client_secret":      "STRAVA_CLIENT_SECRET",
        "authorize_endpoint": "[https://www.strava.com/oauth/authorize](https://www.strava.com/oauth/authorize)",
        "redirect_uri":       "[http://localhost:8000/redirect](http://localhost:8000/redirect)",
        "scope":              "activity:read_all",
        "prompt":             "&approval_prompt=force",
        "token_endpoint":     "[https://www.strava.com/oauth/token](https://www.strava.com/oauth/token)",
        "post_type":          "json",
        "refresh_allowed":    "true"
    },
    "linkedin": {
        "client_id":          "LINKEDIN_CLIENT_ID",  
        "client_secret":      "LINKEDIN_CLIENT_SECRET", 
        "authorize_endpoint": "[https://www.linkedin.com/oauth/v2/authorization](https://www.linkedin.com/oauth/v2/authorization)",
        "redirect_uri":       "[http://localhost:8000/redirect](http://localhost:8000/redirect)",
        "scope":              "r_liteprofile%20r_emailaddress",
        "prompt":             "",
        "token_endpoint":     "[https://www.linkedin.com/oauth/v2/accessToken](https://www.linkedin.com/oauth/v2/accessToken)",
        "post_type":          "form",
        "refresh_allowed":    "false"
    },
    "spotify": {
        "client_id":          "SPOTIFY_CLIENT_ID", 
        "client_secret":      "SPOTIFY_CLIENT_SECRET", 
        "authorize_endpoint": "[https://accounts.spotify.com/authorize](https://accounts.spotify.com/authorize)",
        "redirect_uri":       "[http://localhost:8000/redirect](http://localhost:8000/redirect)",
        "scope":              "user-top-read%20user-read-email%20playlist-modify-public",
        "prompt":             "",
        "token_endpoint":     "[https://accounts.spotify.com/api/token](https://accounts.spotify.com/api/token)",
        "post_type":          "form",
        "refresh_allowed":    "true"
    },
    "github": {
        "client_id":          "GITHUB_CLIENT_ID", 
        "client_secret":      "GITHUB_CLIENT_SECRET", 
        "authorize_endpoint": "[https://github.com/login/oauth/authorize](https://github.com/login/oauth/authorize)",
        "redirect_uri":       "[http://localhost:8000/redirect](http://localhost:8000/redirect)",
        "scope":              "repo",
        "prompt":             "",
        "token_endpoint":     "[https://github.com/login/oauth/access_token](https://github.com/login/oauth/access_token)",
        "post_type":          "json",
        "refresh_allowed":    "true"
    }
}**
```

****我们将这些读入服务映射[字符串]映射[字符串]字符串，以便我们可以按服务和数据类型进行查找。在我们解组文件之后，我们通过从环境变量中读取 client_id 和 client_secret 名称，将它们转换为真实值，这些变量将在本地或服务器环境中设置。****

****oclient/oclient/oclient.go****

```
**func InitOclient() error {
    return loadConfig("oclient/services.json", &services)
}//== Servicesvar services map[string]map[string]stringfunc loadConfig(fname string, config *map[string]map[string]string) (err error) {
    file, err := os.Open(fname)
    if err != nil {
        return
    }
    defer file.Close()
    byteValue, err := ioutil.ReadAll(file)
    if err != nil {
        return
    }
    json.Unmarshal([]byte(byteValue), config)
    for k, v := range *config {
        v["client_id"] = os.Getenv(v["client_id"])
        if v["client_id"] == "" {
            err = errors.New("Missing service client_id for " + k)
            return
        }
        v["client_secret"] = os.Getenv(v["client_secret"])
        if v["client_secret"] == "" {
            err = errors.New("Missing service client_secret for " + k)
            return
        }
    }
    return
}**
```

****在第一个例子中，回想一下我们在 home.html 对授权请求进行了硬编码。这不是很好，原因有很多。我们必须在 web 客户端发起调用，但是让它对 web 服务器完全不透明是不好的。此外，如果我们想要使用推荐的状态来验证回调，那么我们必须以某种方式让 web 服务器知道它。因此，我们将在 web 服务器上生成状态和请求链接，并将其作为 api 提供给 web 客户端。干净多了。****

****州验证码至少应该是 32 个随机字节，但是因为我们需要 PKCE 支持，所以我们可以使用 PKCE(128)验证器来验证我们的州代码。让我们建立我们的 PKCE 助手。PKCE 是代码交换的验证密钥，如上所述，它现在是任何不安全(公共)客户端的授权代码授权的推荐扩展。它的工作原理是生成一个随机秘密(验证者)，然后对它进行 SHA256 哈希运算(挑战)。质询首先在对授权码的初始请求中发送。在授权服务器使用授权码进行回调之后，您可以在下一次对授权服务器的调用中发送验证器而不是客户端密码，以将授权码交换为访问令牌。服务器检查挑战是否与您之前发送的 SHA256 哈希验证器匹配，如果一切正常，您将获得一个访问令牌。****

****![](img/043b28094d77efbc8f10955c916abf47.png)****

****根据 [RFC 7636](https://tools.ietf.org/html/rfc7636) ，PKCE 验证器可以是来自[a-z，a-z，0–9，...，的 43 到 128 字节的随机文本。，_，~]字符集。PKCE 挑战是验证器的 base64-URL 编码的 SHA256 散列。****

****oclient/oclient/oclient.go****

```
**package oclientimport (
    "bytes"
    "crypto/sha256"
    "encoding/base64"
    "encoding/json"
    "errors"
    // "fmt"
    "io"
    "io/ioutil"
    "math/rand"
    "net/http"
    "net/url"
    "os"
    "strings"
    "sync"
    "time"
)const (
    STRAVA    = "strava"
    LINKEDIN  = "linkedin"
    SPOTIFY   = "spotify"
    GITHUB    = "github"
    AUTHORIZE = "authorization_code"
    REFRESH   = "refresh_token"
    SECRET    = "secret"
    PKCE      = "pkce"
)func InitOclient() error {
    PkceInit()
    return loadConfig("oclient/services.json", &services)
}//== PKCEfunc PkceInit() {
    rand.Seed(time.Now().UnixNano())
}//string of pkce allowed chars
func PkceVerifier(length int) string {
    if length > 128 {
        length = 128
    }
    if length < 43 {
        length = 43
    }
    const charset = "abcdefghijklmnopqrstuvwxyz" +
        "ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-._~"
    b := make([]byte, length)
    for i := range b {
        b[i] = charset[rand.Intn(len(charset))]
    }
    return string(b)
}//base64-URL-encoded SHA256 hash of verifier, per rfc 7636
func PkceChallenge(verifier string) string {
    sum := sha256.Sum256([]byte(verifier))
    challenge := base64.URLEncoding.WithPadding(base64.NoPadding).EncodeToString(sum[:])
    return (challenge)
}**
```

****因为我们需要临时存储初始客户端请求和服务器回调之间的状态，所以我们将构建一个小型的内存状态管理系统。这可以通过持久存储或其他类似 redis 的存储系统来实现，但是所需的存储时间窗口足够短，我们可以构建自己的存储系统并保持其简单性。它确实需要互斥保护，因为多个客户端可以访问它。我们将让查找键成为州代码，有效负载将是一个包含过期时间戳、服务名、授权类型以及 PKCE 验证器和质询的结构，我们最终将需要它来支持 PKCE。****

****当客户端向授权服务器发出代码请求时，可能会出现一个问题，授权服务器会生成一个保存到我们的地图中的状态代码，但是由于某种原因，用户没有完成授权，所以回调永远不会发生，所以状态代码永远不会从我们的地图中删除。这就是为什么 State 结构包含 CreatedAt 字段，以及为什么我们需要经常启动垃圾收集来清理映射。我们不需要 cron 它，我们可以偷懒，只检查我们是否应该在 setState()函数中进行 GC。这种泄漏对普通用户来说应该很少，所以我们不想给系统增加过多的负担，但我们需要足够频繁地清理以防范恶意攻击，如果我们达到了存储限制，我们需要积极地进行清理。因此，我们检查对 setState 的每个调用，寻找对垃圾收集限制(GcPeriod)的理想超越，以及对状态映射所允许的最大大小(MaxState)的不太理想的超越。这两种情况都会触发垃圾收集。垃圾收集例程以 InitAuthTimeout 的理想到期开始，并在每次迭代中将它减半，直到它回到 MaxState 之下。在输入时使用验证码也有助于防范恶意。您可以根据自己的具体应用随意更改这些常数。****

```
**//== State Managementconst (
    GcPeriod        = 60  //minutes - minimum ideal time between GC runs (unless MaxState)
    InitAuthTimeout = 10  //minutes - amount of time user has to complete Authorization and get Access Code from Authorization Server
    MaxState        = 400 //max allowed length of state map, to prevent malicious memory overflow
)type State struct {
    CreatedAt     time.Time
    Service       string
    AuthType      string
    PkceVerifier  string
    PkceChallenge string
}var state = make(map[string]*State)
var lastGc = time.Now().UTC()
var mutex = &sync.Mutex{}//get the payload for a state, check expiration, and delete
func getState(key string) (value *State) {
    mutex.Lock()
    v, exists := state[key]
    if exists {
        n := time.Now().UTC()
        if n.After(v.CreatedAt.Add(InitAuthTimeout * time.Minute)) {
            value = nil //don't accept expired state
        } else {
            value = v
        }
        delete(state, key)
    } else {
        value = nil
    }
    defer mutex.Unlock()
    return
}//set the payload for a state, set expiration, do gc as needed
func setState(key string, value *State) {
    mutex.Lock()
    n := time.Now().UTC()
    value.CreatedAt = n
    state[key] = value
    //gc
    authTimeout := InitAuthTimeout * time.Minute //type Duration
    gcTime := lastGc.Add(GcPeriod * time.Minute)
    if n.After(gcTime) || len(state) >= MaxState {
        for ok := true; ok; ok = len(state) >= MaxState { //keep going till below MaxState, 1/2 each cycle
            for k, v := range state {
                expiresAt := v.CreatedAt.Add(authTimeout)
                if n.After(expiresAt) {
                    delete(state, k)
                }
            }
            authTimeout /= 2
        }
        lastGc = time.Now().UTC()
    }
    defer mutex.Unlock()
    return
}**
```

****当我们成功地从授权服务器接收到一个访问令牌时，我们希望将它存储在某个地方，以便我们可以在将来的 api 调用中使用它，直到它过期。我们将为每项服务提供一个令牌。同样，我们可以使用一些持久存储数据库，但我们也可以只是让它成为一个安全的 cookie。注意，对于本地主机测试，我们将 Secure 设置为 false，将 SameSite 设置为 http。但是当我们部署到服务器时，我们将把 Secure 设置为 true，把 SameSite 设置为 http.SameSiteStrictMode。****

```
**//== Cookie Helpersconst CookiePrefix = "_OClient"func cookieName(service string) string {
    return (CookiePrefix + service)
}//generic cookie setter
func setCookie(w http.ResponseWriter, token string, cookieName string) {
    tok64 := base64.StdEncoding.EncodeToString([]byte(token))
    cookie := http.Cookie{
        Name:     cookieName,
        Value:    tok64,
        HttpOnly: true,
        Secure:   false, //use true for production
        Path:     "/",
        SameSite: http.SameSiteLaxMode,
    }
    http.SetCookie(w, &cookie)
    return
}//generic cookie getter
func getCookie(r *http.Request, cookieName string) (token string, err error) {
    cookie, err := r.Cookie(cookieName)
    if err != nil {
        return
    }
    tokb, err := base64.StdEncoding.DecodeString(cookie.Value)
    if err != nil {
        return
    }
    token = string(tokb)
    return
}**
```

****我们需要一个 API 助手来创建基于特定服务的请求链接。我们的助手将利用 json 文件加载的服务地图来查找特定服务的正确信息。注意，我们在最后调用 setState()，将 State 作为一个键——所以当我们从授权服务器获得 state 时，我们可以很容易地检查有效性并查找有效负载。****

```
**//== API Helpers//build service Code Authorize Link and save state as pkceVerifier (128)
func AuthLink(r *http.Request, authtype string, service string) (result string) {
    stData := State{Service: service, AuthType: authtype}
    st := PkceVerifier(128)
    result = services[service]["authorize_endpoint"]
    result += "?client_id=" + services[service]["client_id"]
    result += "&response_type=code&redirect_uri="
    result += services[service]["redirect_uri"]
    result += "&scope=" + services[service]["scope"]
    result += services[service]["prompt"]
    if authtype == PKCE {
        stData.PkceVerifier = PkceVerifier(128)
        stData.PkceChallenge = PkceChallenge(stData.PkceVerifier)
        result += "&code_challenge=" + stData.PkceChallenge
        result += "&code_challenge_method=S256"
    }
    result += "&state=" + st
    setState(st, &stData)
    return
}**
```

****现在，在我们的 main 中，我们将构建一个带有 API endpoint/authlink/{ authtype }/{ service }的小服务器，它返回上面我们的服务器生成的链接。{authtype}可以是“secret ”,用于基于普通客户端秘密的授权码授予，或者可以是“pkce ”,用于 PKCE 扩展。{service}可以是我们支持的任何服务的名称，目前包括“strava”、“linkedin”、“spotify”、“github”..以及您添加到 services.json 文件中的任何其他内容。我为每一个都添加了常量以减少错误，但这不是必需的。****

****oclient/main.go****

```
**package mainimport (
    "github.com/exyzzy/oclient/oclient"
    "fmt"
    "github.com/gorilla/mux"
    "html/template"
    "io/ioutil"
    "log"
    "net/http"
    "net/url"
    "os"
    "path"
)func main() {
    err := oclient.InitOclient()
    if err != nil {
        log.Fatal(err)
    }
    port := os.Getenv("PORT")
    if port == "" {
        port = "8000"
    }
    r := mux.NewRouter()
    r.HandleFunc("/", PageHomeHandler)
    r.HandleFunc("/page/api", PageApiHandler)
    r.HandleFunc("/authlink/{authtype}/{service}", AuthlinkHandler)
    r.HandleFunc("/redirect", RedirectHandler)
    r.HandleFunc("/strava/get/athlete", StravaGetAthleteHandler)
    r.HandleFunc("/strava/get/activities", StravaGetActivitiesHandler)
    r.HandleFunc("/linkedin/get/me", LinkedinGetMeHandler)
    r.HandleFunc("/spotify/get/me", SpotifyGetMeHandler)
    r.HandleFunc("/spotify/get/newreleases", SpotifyGetNewReleasesHandler)
    r.HandleFunc("/spotify/put/rename", SpotifyPutRenameHandler)
    r.HandleFunc("/github/get/user", GithubGetUserHandler)
    http.Handle("/", r)
    fmt.Println(">>>>>>> OClient started at:", port)
    log.Fatal(http.ListenAndServe(":"+port, nil))
    return
}func PageHomeHandler(w http.ResponseWriter, r *http.Request) {
    pageHandler(w, r, nil, "templates", "home.html")
}func PageApiHandler(w http.ResponseWriter, r *http.Request) {
    pageHandler(w, r, nil, "templates", "api.html")
}func pageHandler(w http.ResponseWriter, r *http.Request, data interface{}, dir string, filenames ...string) {
    var files []string
    for _, file := range filenames {
        files = append(files, path.Join(dir, file))
    }
    tmpl, err := template.ParseFiles(files...)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    if err := tmpl.Execute(w, data); err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
}func AuthlinkHandler(w http.ResponseWriter, r *http.Request) {
    vars := mux.Vars(r)
    authtype := vars["authtype"]
    service := vars["service"]
    authlink := oclient.AuthLink(r, authtype, service)
    fmt.Fprintln(w, authlink)
}**
```

****再次注意，我们在前端不需要任何硬编码的请求链接，因为我们创建了一个 api 来根据需要动态生成链接。一个小的 JS 函数调用 Axios 来为我们的客户端检索链接。我们只生成一个新的链接，并在实际用户点击时设置状态。****

****oclient/templates/home.html****

```
**<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>OClient</title>
        <style>
            body {
              background-color: lightgray;
            }
        </style>

    </head>
    <body>
        <h1>OClient Authorize</h1>
        <br>
        <button onclick="getLink(`secret`, `strava`)">Code Authorize Strava</button>
        <button onclick="getLink(`secret`,`linkedin`)">Code Authorize LinkedIn</button>
        <button onclick="getLink(`pkce`,`linkedin`)">PKCE Authorize LinkedIn</button>
        <button onclick="getLink(`pkce`,`spotify`)">PKCE Authorize Spotify</button>
        <button onclick="getLink(`secret`,`github`)">Code Authorize Github</button><script src="[https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js](https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js)"></script>
        <script> function getLink(type, service) {
                console.log(type, service)
            // Make a request for a user with a given ID
                axios.get('/authlink/'+ type + '/' + service)
                .then((response) => {
                    console.log(response.data)
                    window.location.href=response.data
                })
                .catch(function (error) {
                    console.log(error);
                });
            }
        </script></body>
</html>**
```

****既然我们成功地请求了授权代码并显示了服务提示，我们需要构建重定向端点来接收回调并交换代码。我们从服务收到的访问令牌是 json，所以对我们来说它是 map[string]interface{}。****

****getToken()函数通过构建一个字符串化的 map[string][string]参数来完成繁重的工作，该 map[string][string]参数是服务期望在令牌端点接收的 post 参数。根据服务偏好，它可以作为 application/JSON(" post _ type ":" JSON ")或 application/x-www-form-urlencoded(" post _ type ":" form ")发布。OAuth 2.0 指定了表单编码，但是大多数服务将接受 json。最后，它执行 post，ExchangeCode()将返回的令牌保存为 cookie。****

****令人惊讶的是，有些服务在令牌上根本没有到期时间，有些使用 expires_in 而不是 expires_at。我们将所有这些都统一在 getToken()中，并将所有内容转换为“expires_at ”,以使刷新更容易处理。****

****oclient/oclient/oclient.go****

```
**//== Access Token//exchange the Authorization Code for Access Token
func ExchangeCode(w http.ResponseWriter, r *http.Request, code string, state string) (err error) {
    statePtr := getState(state)
    if statePtr == nil {
        err = errors.New("State Key not found")
        return
    }
    token, err := getToken(w, r, statePtr.Service, AUTHORIZE, code, statePtr.AuthType, statePtr.PkceVerifier)
    if err != nil {
        return
    }
    setCookie(w, token, cookieName(statePtr.Service)) //note: must set cookie before writing to responsewriter
    return
}//wrapper to set accept header
func jsonPost(url string, body io.Reader) (resp *http.Response, err error) {
    var client = &http.Client{
        Timeout: time.Second * 10,
    }
    req, err := http.NewRequest("POST", url, body)
    if err != nil {
        return nil, err
    }
    req.Header.Set("Content-Type", "application/json")
    req.Header.Set("Accept", "application/json")
    return client.Do(req)
}//subtract a small delta from exires_at to account for transport time
const DELTASECS = 5//get a token from authorization endpoint
func getToken(w http.ResponseWriter, r *http.Request, service string, tokType string, code string, authType string, verifier string) (result string, err error) {
    rParams := map[string]string{
        "client_id":    services[service]["client_id"],
        "redirect_uri": services[service]["redirect_uri"],
    }
    switch tokType {
    case AUTHORIZE:
        rParams["code"] = code
        rParams["grant_type"] = AUTHORIZE
    case REFRESH:
        rParams["refresh_token"] = code
        rParams["grant_type"] = REFRESH
    default:
        err = errors.New("Unknown tokType")
        return
    }
    switch authType {
    case SECRET:
        rParams["client_secret"] = services[service]["client_secret"]
    case PKCE:
        rParams["code_verifier"] = verifier
    default:
        err = errors.New("Unknown authType")
        return
    }
    var resp *http.Response
    switch services[service]["post_type"] {
    case "json":
        var requestBody []byte
        requestBody, err = json.Marshal(rParams)
        if err != nil {
            return
        }
        resp, err = jsonPost(services[service]["token_endpoint"], bytes.NewBuffer(requestBody))
        if err != nil {
            return
        } case "form":
        vals := url.Values{}
        for k, v := range rParams {
            vals.Set(k, v)
        }
        resp, err = http.PostForm(services[service]["token_endpoint"], vals)
        if err != nil {
            return
        }
    default:
        err = errors.New("Unknown post_type")
        return
    }
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        return
    }
    if resp.StatusCode != 200 {
        err = errors.New(string(body))
        return
    }
    //check for expires_at
    var tokMap map[string]interface{}
    decoder := json.NewDecoder(strings.NewReader(string(body)))
    decoder.UseNumber()
    err = decoder.Decode(&tokMap)
    if err != nil {
        err = errors.New("decoder.Decode: " + err.Error())
        return
    }
    expire, exists := tokMap["expires_at"] if exists {
        result = string(body)
        return
    }
    var expiresIn int64
    expire, exists = tokMap["expires_in"]
    if !exists { //no expiration, so make it a year
        expiresIn = 31536000
    } else {
        expiresIn, err = expire.(json.Number).Int64()
    }
    tokMap["expires_at"] = epochSeconds() + expiresIn - DELTASECS
    b, err := json.Marshal(tokMap)
    if err != nil {
        err = errors.New("json.Marshal: " + err.Error())
        return
    }
    result = string(b)
    return
}**
```

****我们已经在上面为它添加了路由，现在让我们填充处理程序。****

****oclient/main.go****

```
**func RedirectHandler(w http.ResponseWriter, r *http.Request) {
    m, err := url.ParseQuery(r.URL.RawQuery)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    code := m.Get("code")
    state := m.Get("state")
    err = oclient.ExchangeCode(w, r, code, state) //do not write to w before this call
    if err != nil {
        http.Error(w, "Exchange Failed: "+err.Error(), http.StatusInternalServerError)
        return
    }
    // fmt.Fprintln(w, "Code: ", code, " Scope: ", scope)
    http.Redirect(w, r, "/page/api", 302)
}**
```

****我们现在有了访问令牌，我们将构建一些助手来对我们的服务 api 进行 api 调用，为服务添加正确的承载令牌。我们的 setHeader()函数将检查过期的 AccessToken，并在允许的情况下自动刷新它。****

****oclient/oclient/oclient.go****

```
**//make call to a resource api, add oauth bearer token
func ApiRequest(w http.ResponseWriter, r *http.Request, service, method, url string, data map[string]interface{}) (response *http.Response, err error) {
    var client = &http.Client{
        Timeout: time.Second * 10,
    }
    var body io.Reader
    if data == nil {
        body = nil
    } else {
        var requestBody []byte
        requestBody, err = json.Marshal(data)
        if err != nil {
            return
        }
        body = bytes.NewBuffer(requestBody)
    }
    request, err := http.NewRequest(method, url, body)
    if err != nil {
        return
    }
    err = setHeader(w, r, service, request)
    if err != nil {
        err = errors.New("Unable to set Header: " + err.Error())
        return
    }
    response, err = client.Do(request)
    return
}func epochSeconds() int64 {
    now := time.Now()
    secs := now.Unix()
    return secs
}//get Access Token via cookie, refresh if expired, set header bearer token
func setHeader(w http.ResponseWriter, r *http.Request, service string, newReq *http.Request) (err error) {
    token, err := getCookie(r, cookieName(service))
    if err != nil {
        return
    }
    var tokMap map[string]interface{} // err = json.Unmarshal([]byte(token), &tokMap)
    // normally as above, but we want numbers as ints vs floats
    decoder := json.NewDecoder(strings.NewReader(token))
    decoder.UseNumber()
    err = decoder.Decode(&tokMap)expiresAt, err := tokMap["expires_at"].(json.Number).Int64()
    if err != nil {
        return
    }
    if epochSeconds() > expiresAt { //token has expired, refresh it
        if services[service]["refresh_allowed"] == "false" {
            err = errors.New("Non-refreshable Token Expired, Re-authorize")
            return
        }
        refresh, exists := tokMap["refresh_token"]
        if !exists {
            err = errors.New("Refresh Token Not Found")
            return
        }
        var newToken string
        newToken, err = getToken(w, r, service, REFRESH, refresh.(string), SECRET, "")
        if err != nil {
            return
        }
        setCookie(w, newToken, cookieName(service)) //note: must set cookie before writing to responsewriter
        decoder = json.NewDecoder(strings.NewReader(newToken))
        decoder.UseNumber()
        tokMap = make(map[string]interface{})
        err = decoder.Decode(&tokMap)
        if err != nil {
            return
        }
    }
    newReq.Header.Add("Authorization", "Bearer "+tokMap["access_token"].(string))
    newReq.Header.Set("Content-Type", "application/json")
    newReq.Header.Set("Accept", "application/json")
    return
}**
```

****我们完成了基本的管道工程。接下来，我们将在 services.json 配置中实际练习各种 OAuth 服务 api。我们将首先设置服务 api 处理程序。我将只设置几个来演示每个服务，但是在这里您可以使用 ApiRequest()和任何应用程序/业务逻辑层对服务进行任何调用。****

****oclient/main.go****

```
**//== APIfunc StravaGetAthleteHandler(w http.ResponseWriter, r *http.Request) { url := "[https://www.strava.com/api/v3/athlete](https://www.strava.com/api/v3/athlete)"
    resp, err := oclient.ApiRequest(w, r, oclient.STRAVA, "GET", url, nil)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    fmt.Fprintln(w, string(body))
}func StravaGetActivitiesHandler(w http.ResponseWriter, r *http.Request) { url := "[https://www.strava.com/api/v3/athlete/activities?page=1&per_page=30](https://www.strava.com/api/v3/athlete/activities?page=1&per_page=30)"
    resp, err := oclient.ApiRequest(w, r, oclient.STRAVA, "GET", url, nil)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    fmt.Fprintln(w, string(body))
}func LinkedinGetMeHandler(w http.ResponseWriter, r *http.Request) { url := "[https://api.linkedin.com/v2/me](https://api.linkedin.com/v2/me)"
    resp, err := oclient.ApiRequest(w, r, oclient.LINKEDIN, "GET", url, nil)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    fmt.Fprintln(w, string(body))
}func SpotifyGetMeHandler(w http.ResponseWriter, r *http.Request) { url := "[https://api.spotify.com/v1/me](https://api.spotify.com/v1/me)"
    resp, err := oclient.ApiRequest(w, r, oclient.SPOTIFY, "GET", url, nil)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    fmt.Fprintln(w, string(body))
}func SpotifyGetNewReleasesHandler(w http.ResponseWriter, r *http.Request) { url := "[https://api.spotify.com/v1/browse/new-releases](https://api.spotify.com/v1/browse/new-releases)"
    resp, err := oclient.ApiRequest(w, r, oclient.SPOTIFY, "GET", url, nil)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    fmt.Fprintln(w, string(body))
}func SpotifyPutRenameHandler(w http.ResponseWriter, r *http.Request) { data := map[string]interface{}{
        "name":        "Updated Playlist Name",
        "description": "Updated playlist description",
        "public":      false,
    } //specific playlist, yours will be different
    url := "[https://api.spotify.com/v1/playlists/2RmnrZSPoYtVyjou7DU8We](https://api.spotify.com/v1/playlists/2RmnrZSPoYtVyjou7DU8We)"
    resp, err := oclient.ApiRequest(w, r, oclient.SPOTIFY, "PUT", url, data)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    fmt.Fprintln(w, string(body))
}func GithubGetUserHandler(w http.ResponseWriter, r *http.Request) { url := "[https://api.github.com/user](https://api.github.com/user)"
    resp, err := oclient.ApiRequest(w, r, oclient.GITHUB, "GET", url, nil)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    fmt.Fprintln(w, string(body))
}**
```

****最后，一个小的 api 页面，带有调用我们已经公开的这些服务 api 的按钮。服务授权成功后，主页将重定向到此处。****

****oclient/templates/api.html****

```
**<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>OClient API</title>
        <style>
            body {
              background-color: lightgray;
            }
        </style>
    </head>
    <body>
        <h1>OClient API</h1>
        <br>
        <a href="/">Home Page</a>
        <br><br>
        <button onclick="window.location.href=window.location.origin + '/strava/get/athlete'">Get Strava Athlete</button>
        <button onclick="window.location.href=window.location.origin + '/strava/get/activities'">Get Strava Activities</button>
        <button onclick="window.location.href=window.location.origin + '/linkedin/get/me'">Get Linkedin Profile</button>
        <button onclick="window.location.href=window.location.origin + '/spotify/get/me'">Get Spotify Profile</button>
        <button onclick="window.location.href=window.location.origin + '/spotify/get/newreleases'">Get Spotify New Releases</button>
        <button onclick="window.location.href=window.location.origin + '/spotify/put/rename'">Spotify Put Rename</button>
        <button onclick="window.location.href=window.location.origin + '/github/get/user'">Github Get User</button>
    </body>
</html>**
```

****好了，让我们克隆、编译并运行它。请注意，当您这样做时，您必须为您的服务设置环境变量，以匹配您在 oclient/services.json 中为 **client_id 和 client_secret** 设置的名称——在您这样做之前，它不会运行。如果您愿意，还可以添加不同的服务 api，并更改 auth/api 调用。此外，我的演示在 oclient/main.go 中有一个硬编码的 spotify 播放列表 id(特定于我)，只是为了测试和演示一个 PUT 调用。****

```
**go get github.com/exyzzy/oclient
go install $GOPATH/src/github.com/exyzzy/oclient
oclient**
```

****当您在 localhost:8000 上打开浏览器时，您应该会看到:****

****![](img/d78c4d9e0eb0d7cfc35822ae1d3c06a2.png)****

****当你点击一个按钮时，应该会弹出该服务的授权页面。****

****![](img/7c8d0cbe08a9778cda6058013615bdef.png)****

****当您授权时，您应该被重定向回服务 api 页面。****

****![](img/0ae50e086750fbef0b21212e540ddcbe.png)****

****您可以单击任何按钮来启动特定的 api 并查看返回值。但是如果您还没有这样做，您必须返回到主页并首先对它们进行授权。****

****![](img/3eae4af3ccfaf7068423a2a9485d0e77.png)****

****如果您检查应用程序 cookie，您会看到它将我们的状态存储在每个服务的 _OClient* cookie 下。****

# ****后续步骤****

****我们已经建立了一个很好的小通用和可扩展的 OAuth 2.0 客户端库，可以用于任何第三方服务。虽然它还没有在生产环境中测试过，但是它应该会给你一个很好的开端。****

****你可以在这里找到所有的代码。玩得开心。****

****[](https://github.com/exyzzy/oclient) [## GitHub - exyzzy/oclient:在 Go 中访问安全的 HTTP 服务

### 在 Go 中访问安全的 HTTP 服务。在 GitHub 上创建一个帐户，为 exyzzy/oclient 开发做出贡献。

github.com](https://github.com/exyzzy/oclient) 

# 附录

自从写这篇文章以来，我遇到了几个需要支持基本认证的新网站，它们已经被添加到 github repo 中。

oclient.go

```
func basicAuth(username, password string) string {
   auth := username + ":" + password
   return base64.StdEncoding.EncodeToString([]byte(auth))
}func basicPost(url string, body io.Reader, ba string) (resp *http.Response, err error) {
   var client = &http.Client{
      Timeout: time.Second * 10,
   }
   req, err := http.NewRequest("POST", url, body)
   if err != nil {
      return nil, err
   }
   req.Header.Add("Authorization", "Basic "+ba)
   req.Header.Set("Content-Type", "application/x-www-form-urlencoded")
   req.Header.Set("Accept", "application/json")
   return client.Do(req)
}func getToken(w http.ResponseWriter, r *http.Request, service string, tokType string, code string, authType string, verifier string) (result string, err error) {...case "basic":
   form := url.Values{}
   for k, v := range rParams {
      form.Set(k, v)
   }
   basic := basicAuth(rParams["client_id"], rParams["client_secret"])
   resp, err = basicPost(services[service]["token_endpoint"], strings.NewReader(form.Encode()), basic)
   if err != nil {
      return
   } ...
```

与本文最相似的代码版本是:【https://github.com/exyzzy/oclient

然而，代码的最新(最好)版本是一个新的包版本:

[](https://github.com/exyzzy/oauth2) [## GitHub - exyzzy/oauth2: oauth2 用于 go 的包

### 用于 Go 的 OAuth 2.0 客户端包见 Medium 文章:然而，这只是核心 OAuth 2 包的例子…

github.com](https://github.com/exyzzy/oauth2) 

这里是它的示例代码:

[](https://github.com/exyzzy/oclient2) [## GitHub-exy zzy/o client 2:oauth 2 包的客户端示例

### oauth2 包的客户端示例。通过在 GitHub 上创建帐户，为 exyzzy/oclient2 开发做出贡献。

github.com](https://github.com/exyzzy/oclient2)****