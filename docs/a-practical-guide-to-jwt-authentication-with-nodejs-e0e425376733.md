# NodeJS JWT 认证实用指南

> 原文：<https://levelup.gitconnected.com/a-practical-guide-to-jwt-authentication-with-nodejs-e0e425376733>

## 更好的编程

## 为下一个 NodeJS 应用程序构建一个身份验证模块

![](img/dc9fedc61f6addb8a38d5ad4d8659da0.png)

由[韦斯利·廷吉](https://unsplash.com/@wesleyphotography?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/private-property?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

您是否尝试过将 JWT 身份验证集成到 Node.js 应用程序中，但始终没有找到正确的解决方案？那你来对地方了。在这篇文章中，我们将带您了解 Node.js 中使用 npm 包`jsonwebtoken`的 JWT 认证的更详细的细节。

[](/a-brief-introduction-to-securing-applications-with-jwt-2004e9f6c829) [## 用 JWT 保护应用程序的简介

### 了解什么是 JWT，它是如何工作的，以及它如何保护我们的应用程序的安全。

levelup.gitconnected.com](/a-brief-introduction-to-securing-applications-with-jwt-2004e9f6c829) 

如果您仍然不确定 JWT 到底是什么以及它是如何工作的，您可以在继续实现之前关注我们之前的帖子。正如我们在上一篇文章中所讨论的，在这个实现中，我们将遵循 JWT 身份验证的最佳实践。如果您想先回忆一下 JWTs，让我们来看看在本教程中我们将遵循哪些最佳实践。

*   发送 cookie 中的 JWT 令牌，而不是 HTTP 头
*   为令牌设置较短的过期时间
*   使用刷新令牌来重新颁发即将到期的访问令牌

在进入细节之前，我想强调两点:

*   为身份验证编写自己的实现并不总是最好的解决方案。有几个第三方产品可以以非常安全的方式为您处理所有这一切。
*   本教程中介绍的代码是一个 monolith 应用程序的实现。如果您想将此代码用于微服务，您必须使用公钥/私钥组合来签名和验证令牌。

既然我们已经设定了目标，让我们开始实施吧。

# 初始准备工作…

如果您还没有为这个项目设置节点环境。然后，安装我们将在本教程中使用的以下软件包。

*   Express:我们将使用的 Node.js 框架
*   Cookie-Parser:因为我们将在 Cookie 中发送 JWT 令牌，所以使用这个包来解析随请求一起发送的 cookie
*   Body-Parser:这个包解析传入请求的主体，以提取 POST 参数
*   Dotenv:这个包从。应用程序环境的 env 文件
*   Json-Web-Token:这是帮助我们实现 JWT 的包

您可以使用以下命令安装这些软件包。

```
npm install express cookie-parser bory-parser dotenv json-web-token --save
```

现在我们将在项目的主文件`app.js`中设置应用程序的后端。

```
require('dotenv').config()
const express = require('express')
const bodyParser = require('body-parser')
const cookieParser = require('cookie-parser')
const app = express()const {login, refresh} = require('./authentication')
app.use(bodyParser.json())
app.use(cookieParser())app.post('/login', login)
app.post('/refrsh', refresh)
```

在接下来的步骤中，我们将在 controller.js 中实现我们在上面的代码中使用过的函数。我们使用 login 函数来处理发送到/login 路由和登录用户的 post 请求。我们使用刷新函数来处理发送到/refresh 路由的 post 请求，并使用刷新令牌发布新的访问令牌。

# 设置环境变量

在实现让用户登录的逻辑之前，我们需要设置配置 jwt 所需的环境变量。创建一个. env 文件，并添加我们将在应用程序中使用的这两个变量。

```
ACCESS_TOKEN_SECRET=swsh23hjddnns
ACCESS_TOKEN_LIFE=120
REFRESH_TOKEN_SECRET=dhw782wujnd99ahmmakhanjkajikhiwn2n
REFRESH_TOKEN_LIFE=86400
```

您可以添加任何字符串作为密码。建议使用带有随机字符的较长密码作为安全措施。我们创建的访问令牌的到期时间将是 120 秒。我们还设置了签名刷新令牌的秘密及其到期时间。请注意，与访问令牌相比，刷新令牌的生存期更长。

# 处理用户登录和 JWT 令牌创建

现在我们可以进入实现登录函数的步骤，我们将该函数导入到`app.js`文件来处理`/login`路由。

为了实现这个目的，我们将在应用程序中存储一个用户对象数组。在真实的场景中，您将从数据库或任何其他位置检索这些用户信息。另外，这只是为了演示，**从来没有**存储实际的密码。

![](img/344ae7b1b4d963ca601696ab06f91349.png)

不要以纯文本格式保存密码

```
let users = {
    john: {password: "passwordjohn"},
    mary: {password:"passwordmary"}
}
```

当实现登录功能时，首先我们需要检索与登录 POST 请求一起发送的用户名和密码。

```
const jwt = require('json-web-token')// Never do this!
let users = {
    john: {password: "passwordjohn"},
    mary: {password:"passwordmary"}
}exports.login = function(req, res){ let username = req.body.username
    let password = req.body.password

    // Neither do this!
    if (!username || !password || users[username] !== password){
        return res.status(401).send()
    }    
}
```

如果请求不包含用户名或密码，服务器将以 401 未授权状态响应。如果与请求一起发送的密码与存储在数据库中的该特定用户名的密码不匹配，则应用相同的响应。

如果客户端已经向服务器发送了正确的凭据，那么我们将通过颁发新的 JWT 令牌让用户登录系统。在这种情况下，新登录用户会收到两个令牌:访问令牌和刷新令牌。然后，访问令牌与 cookie 中的响应一起发送回客户端。刷新令牌存储在数据库中，用于将来发布访问令牌。在我们的例子中，我们将把刷新令牌存储在之前创建的用户数组中。

```
const jwt = require('json-web-token')// Never do this!
let users = {
    john: {password: "passwordjohn"},
    mary: {password:"passwordmary"}
}exports.login = function(req, res){ let username = req.body.username
    let password = req.body.password

    // Neither do this!
    if (!username || !password || users[username].password !== password){
        return res.status(401).send()
    } //use the payload to store information about the user such as username, user role, etc.
    let payload = {username: username} //create the access token with the shorter lifespan
    let accessToken = jwt.sign(payload, process.env.ACCESS_TOKEN_SECRET, {
        algorithm: "HS256",
        expiresIn: process.env.ACCESS_TOKEN_LIFE
    }) //create the refresh token with the longer lifespan
    let refreshToken = jwt.sign(payload, process.env.REFRESH_TOKEN_LIFE, {
        algorithm: "HS256",
        expiresIn: process.env.REFRESH_TOKEN_LIFE
    }) //store the refresh token in the user array
    users[username].refreshToken = refreshToken //send the access token to the client inside a cookie
    res.cookie("jwt", accessToken, {secure: true, httpOnly: true})
    res.send()
}
```

当在 cookie 中发送访问令牌时，记得设置 httpOnly 标志以防止攻击者从客户端访问 cookie。我们还在上面的例子中设置了安全标志。但是，如果您只是通过 HTTP 连接而不是 HTTPS 连接来测试这段代码，那么请移除安全标志，将其与响应一起发送。

# 添加中间件来验证用户请求

服务器需要检查用户是否已经登录，然后才允许访问某些路由。我们可以使用每个请求在 cookie 中发送的访问令牌来验证用户实际上已经过身份验证。这个过程是在中间件中执行的。

让我们创建一个名为`middleware.js`的新文件，并实现 verify 方法来检查用户是否经过身份验证。

首先，我们应该从随请求发送的 cookie 中检索访问令牌。如果请求不包含访问令牌，它将不会前进到预定的路由，而是返回 403 禁止错误。

```
const jwt = require('json-web-token')exports.verify = function(req, res, next){
    let accessToken = req.cookies.jwt //if there is no token stored in cookies, the request is unauthorized
    if (!accessToken){
        return res.status(403).send()
    }
}
```

如果请求包含访问令牌，那么服务器将使用存储的秘密来验证它是否是由服务器本身发出的。如果令牌过期或被识别为未经服务器签名的令牌，jsonwebtoken 的 verify 方法将抛出一个错误。我们可以处理这个错误，向客户端返回一个 401 错误。

```
const jwt = require('json-web-token')exports.verify = function(req, res, next){
    let accessToken = req.cookies.jwt //if there is no token stored in cookies, the request is unauthorized
    if (!accessToken){
        return res.status(403).send()
    } let payload
    try{
        //use the jwt.verify method to verify the access token
        //throws an error if the token has expired or has a invalid signature
        payload = jwt.verify(accessToken, process.env.ACCESS_TOKEN_SECRET)
        next()
    }
    catch(e){
        //if an error occured return request unauthorized error
        return res.status(401).send()
    }
}
```

现在，我们可以使用这个中间件来保护任何需要用户在访问之前登录的路由。将中间件导入到您正在处理路由的地方，在我们的例子中是`app.js`。如果我们试图保护一个名为/comments 的路由，可以通过在路由处理器之前添加中间件来轻松实现。

```
const {verify} = require('./middleware')app.get('/comments', verify, routeHandler)
```

只有当用户通过身份验证时，请求才会被传递到路由处理程序。

# 使用刷新令牌发布新的访问令牌

还记得我们在`app.js`文件的初始代码中使用的/refresh route 和 refresh 函数吗？现在，我们可以实现这个刷新功能，使用存储的刷新令牌发布新的访问令牌。

这里使用的函数 refresh 也在我们之前用于登录函数实现的`controller.js`文件中。

这个函数的第一部分非常类似于我们验证访问令牌的工作:检查访问令牌是否被发送并验证令牌。

```
exports.refresh = function (req, res){ let accessToken = req.cookies.jwt if (!accessToken){
        return res.status(403).send()
    } let payload
    try{
        payload = jwt.verify(accessToken, process.env.ACCESS_TOKEN_SECRET)
    }
    catch(e){
        return res.status(401).send()
    }
}
```

然后，我们将使用存储在访问令牌有效负载中的用户名来检索这个特定用户的刷新令牌。一旦刷新令牌被验证，服务器将发布新的访问令牌，如登录功能中所实现的。

如果刷新令牌过期或验证失败，服务器将返回未经授权的错误。否则，将在 cookie 中发送新的访问令牌。

```
exports.refresh = function (req, res){ let accessToken = req.cookies.jwt if (!accessToken){
        return res.status(403).send()
    } let payload
    try{
        payload = jwt.verify(accessToken, process.env.ACCESS_TOKEN_SECRET)
     }
    catch(e){
        return res.status(401).send()
    } //retrieve the refresh token from the users array
    let refreshToken = users[payload.username].refreshToken //verify the refresh token
    try{
        jwt.verify(refreshToken, process.env.REFRESH_TOKEN_SECRET)
    }
    catch(e){
        return res.status(401).send()
    } let newToken = jwt.sign(payload, process.env.ACCESS_TOKEN_SECRET, 
    {
        algorithm: "HS256",
        expiresIn: process.env.ACCESS_TOKEN_LIFE
    }) res.cookie("jwt", newToken, {secure: true, httpOnly: true})
    res.send()
}
```

这一步完成了我们使用 Node.js 的 JWT 认证的实现

# 摘要

在本教程中，我们介绍了在 Node.js 中使用 JWT 实现身份验证的步骤。作为上一篇文章的延续，我们讨论了 JWT 身份验证背后的理论，我们的实现侧重于遵循我们之前讨论的最佳实践。因此，我们的 JWT 实现利用 cookies 来发送 jwt 并刷新令牌以生成新的访问令牌。如果您愿意在这一实现之前先行一步，您可以提出一个解决方案，在短时间内重新发布刷新令牌，以避免安全漏洞。

感谢阅读！