# 如何使用 JWT 在应用程序中实现授权

> 原文：<https://levelup.gitconnected.com/how-to-implement-authorization-in-your-application-using-jwt-bf19bffff9fe>

![](img/e6f4fc3f43e626d5a1bde6cbeb5b1578.png)

克里斯汀·休姆在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如果您正在构建一个 web 应用程序，实现 API 是一个必要的需求。您向用户显示的数据需要从不同服务器上的 API 服务中获取。

有时，您的网站可能需要限制对其中一些 API 的访问，以防止它们被其他地方获取。这些限制可以是从用户的可用性到用户角色的任何内容。这被称为授权。

在这篇文章中，我将向你展示如何使用 JSON Web Token (JWT)实现前端(React)和后端(Node JS)的授权。我们将实现三个 API 调用来演示这个过程。

## 批准

在涉及 API 服务的应用程序中，应该考虑安全性。API 应该不能从应用程序之外的任何地方访问。此外，您还希望确保 API 只能被必要的实体访问。为此，您需要实现授权。考虑以下两种情况。

如果您的应用程序需要对用户进行身份验证，您需要确保某些服务只能由经过身份验证的用户访问。例如，在亚马逊上，任何人都可以查看可用产品的列表，但特定用户的订单、购买、支付信息等除外。应该只允许经过身份验证的用户访问。

另一个用例是只有特定用户可以访问服务。在学校网站上，学生可以查看自己的成绩，但只有教师有权修改成绩。

当用户通过身份验证后，身份验证服务会将访问令牌发送回应用程序。访问令牌包含关于用户、他/她的角色、登录时间、到期时间以及其他细节的信息。

发出 API 请求时，会添加包含访问令牌的授权头。这个令牌在后端被解码，并检索关于用户的信息。根据这些信息，API 决定用户是否有权获得响应。

我们将实现一个类似的场景。我将在以后的文章中介绍认证。这里，我们将直接跳到身份验证的最后，即获取访问令牌，然后使用同一个令牌来演示三个 API 调用。

在这篇文章中，我们将使用 JWT。

## 什么是 JWT？

JWT 是一种以编码形式发送信息的安全方式。该令牌包含三个部分:

*   **头:**这个部分包含令牌的类型和用于编码令牌的算法。
*   **有效负载:**这部分包含用户的信息和其他信息，如令牌有效期。
*   **签名:**该部分用于对令牌进行签名，并验证消息在传输时未被更改。

一个 JWT 看起来像这样，所有的部分都以编码的形式:
`[Header].[Payload].[Signature]`

JWT 的一个例子:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

访问[这里](https://jwt.io/)解码 JWT。观看[这个](https://www.youtube.com/watch?v=7Q17ubqLfaM&t=32s)视频，了解更多关于 Node.js 中 JWT 的实现

现在，您对授权和 JWT 有了一点了解，让我们开始实现吧。

# 安装

## React 应用

在您的项目目录中创建一个文件夹`authorization-frontend`。在其中，运行以下命令来创建 React 应用程序。

```
create-react-app authorization-frontend
```

我们将在这个项目中使用 Bootstrap，所以将这个 CDN 添加到*index.html*文件中。

```
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-1BmE4kWBq78iYhFldvKuhfTAU6auU8tT94WrHftjDbrCEXSU1oBoqyl2QvZ6jIW3" crossorigin="anonymous">
```

或者，您可以从[这里](https://getbootstrap.com/docs/5.1/getting-started/download/)下载引导源文件。

我们将使用 [*Axios*](https://axios-http.com/docs/intro) 库来进行 API 调用。使用命令`npm i axios`安装它。

## Node.js 服务器

首先，如果您的系统中没有 Node.js，请安装它。从[这里](https://nodejs.dev/download/)下载。

为服务器代码创建一个名为`api-server`的单独文件夹。在该文件夹中，运行命令`npm init`并根据给定的提示输入值。它应该会在您的文件夹中创建一个`package.json`文件。该文件显示了您已经安装在项目目录中的模块。

![](img/e9586270a77c8d7f0959aa80e1f4655b.png)

总体项目结构

我们正在使用以下模块:

*   express : Node.js 框架，使构建服务器变得容易
*   cors :允许跨来源的源发出 API 请求。[了解更多](https://www.section.io/engineering-education/how-to-use-cors-in-nodejs-with-express/)。
*   **jsonwebtoken** :生成访问令牌

用命令`npm i express cors jsonwebtoken`安装它们。

现在，创建一个文件`server.js`，其中包含设置节点服务器所需的代码。包括以下代码:

```
const express = require('express')
const cors = require('cors')
const jwt = require('jsonwebtoken')const app = express()// For sending JSON data through POST requests
app.use(express.json());app.use(cors({
    origin: '*'
})) // Define APIs hereconst PORT = 8000app.listen(PORT, () => {
    console.log('Listening on port', PORT);
})
```

对于这个项目，允许 CORS 下的所有源代码，即任何浏览器都可以访问这些 API。但是在开发实际应用程序时，这不是一个好的做法。

# 获取访问令牌

既然您已经设置了这两个应用程序，现在是时候在后端生成访问令牌并将其传递给前端，即实现一个虚拟身份验证。

我们将生成两种类型的访问令牌:一种用于普通用户，另一种用于管理员用户。

## 用于提取令牌的端点

创建一个 API 端点来获取令牌。使用 POST API，因为我们从前端获取一个请求体，它通常包含身份验证期间的用户凭证。在我们的例子中，前端只发送一个指示要获取的令牌类型的值。

```
app.post('/access_token', (req, res) => {
    const { value } = req.body; switch(value) {
        case 1:
            // generate normal token
        case 2:
            // generate admin token        
        default:
            // Send error message
    }
})
```

## 生成访问令牌

让我们有两个用户对象，一个普通用户和一个管理员用户。此外，定义一个将用于对令牌进行签名的密码。

```
const NORMAL_USER = {
    name: 'kunal',
    admin: false
}const ADMIN_USER = {
    name: 'alex',
    admin: true
}const SECRET = 'client-secret'
```

**注意:**在你的应用程序代码中定义一个秘密不是一个好习惯，你应该把它放在一个 *env* 文件中。

令牌可以通过使用`sign()`函数来生成。

```
jwt.sign(NORMAL_USER, SECRET, (err, token) => { res.json({ accessToken: token })})
```

对于管理令牌，用`ADMIN_USER`替换`NORMAL_USER`

现在，让我们通过解码令牌来检查。`iat`字段表示令牌被发布的时间。

![](img/854f30b6b997839126a2c62ca3e84a07.png)

## 去拿令牌

在前端，导入 Axios 库并初始化后端服务器的主机名。

```
import axios from 'axios'const HOST_NAME = 'http://localhost:8000'
```

然后，创建两个按钮，分别用于获取普通令牌和管理令牌。

![](img/08131b7dad9396c622b5a0666c6d597a.png)

单击按钮时，调用`handleGetTokenClick()`方法，用一个`option`参数获取访问令牌，以确定获取哪个令牌。

```
function handleGetTokenClick(option) {    }
```

在这个方法中，向端点`/access_token`的 API 发出 post 请求。

```
axios.post(HOST_NAME+'/access_token', { value: option }) .then(res => { --- HANDLE THE RESPONSE ---})
```

回应是这样的。

![](img/5866a6bdff3d45902a9ba05c8b924f2d.png)

## 将令牌存储为状态

从响应中获取访问令牌，并将其存储在会话存储(或本地存储；在此阅读差异[。](https://www.geeksforgeeks.org/difference-between-local-storage-session-storage-and-cookies)

创建一个状态变量来保存访问令牌。

```
const [accessToken, setAccessToken] =               
          useState(sessionStorage.getItem('accessToken'));
```

此外，当您从 API 接收访问令牌时，设置状态。下面是处理响应的代码。

```
const { accessToken } = res.data;
sessionStorage.setItem('accessToken', accessToken);
setAccessToken(accessToken);
```

# API 调用

现在，是实现 API 调用的时候了。如前所述，我们将进行三次 GET API 调用:

*   **公共 API:** 任何人都可以访问。
*   **私有 API:** 仅授权用户可访问(即使用访问令牌)
*   **受限 API:** 仅管理员用户可访问。

## 实现三个 API

**公共 API**

这个 API 在被调用时发送一个简单的响应。

```
app.get('/public_api', (req, res) => { res.send('Public API called')})
```

**私有 API**

这个 API 需要一个带有有效访问令牌的授权头。

```
app.get('/private_api', (req, res) => {  ...  })
```

首先，检查用户是否发送了授权头。如果没有，则发送一条带有`401`状态代码的错误消息。这表示缺少凭据或凭据无效。

```
const auth_header = req.headers.authorization;
if(!auth_header)  res.send(401, 'Unauthorized request')
```

如果请求有一个授权头，从它那里获取访问令牌。因为授权头的形式是`Bearer *access_token*`，所以使用`split()`函数来获取访问令牌。

```
const accessToken = auth_header.split(' ')[1]
```

现在，使用`verify()`函数来验证令牌。它将令牌、秘密和回调函数作为参数。如果令牌有效，则使用解码后的有效负载调用回调函数，否则调用时出错。

```
jwt.verify(accessToken, SECRET, (err, payload) => {
    if (err) res.send(401, 'Unauthorized request')
    res.send('Private API called')
})
```

使用 Postman 测试 API。

![](img/37cfaf24022196ee4bd07ae350ce880f.png)

**受限 API**

此 API 只能由管理员用户调用。逻辑几乎类似于私有 API，除了我们还检查用户是否是管理员。

```
app.get('/restricted_api', (req, res) => {
    const auth_header = req.headers.authorization;
    if(!auth_header)  res.send(401, 'Unauthorized request')

    const accessToken = auth_header.split(' ')[1]

    jwt.verify(accessToken, SECRET, (err, user) => {
        if (err) res.send(401, 'Unauthorized request')
        if (user.admin == true) res.send('Restricted API called')
        res.send(401, 'Unauthorized request')
    })
})
```

现在，如果一个普通用户向这个 API 发出请求，它会发送一个 401 错误。

## 创建状态以存储响应

在前端，创建三个状态变量来存储来自每个 API 的响应。

```
const [publicResponse, setPublicResponse] = useState('')
const [privateResponse, setPrivateResponse] = useState('')
const [restrictedResponse, setRestrictedResponse] = useState('')
```

## 创建按钮来调用 API

![](img/66bd369790b52b089baf091e6bdfa708.png)

在点击每个按钮时，调用`handleAPIButtonClick()`方法，该方法也需要一个`option`参数来决定调用哪个 API。

使用 switch-case 语句根据传递的选项调用每个 API。

## 调用 API

调用公共 API。

```
axios.get(HOST_NAME + '/public_api').then(res => { setPublicResponse(res.data)})
```

调用私有 API 时，以`Bearer *access_token*` *的形式传递授权头。*点击阅读更多关于无记名代币的信息[。](https://swagger.io/docs/specification/authentication/bearer-authentication/)

此外，实现`catch`块来处理错误响应。

```
axios.get(HOST_NAME + '/private_api', {
    headers: {
        Authorization: 'Bearer ' + accessToken
    }
}).then(res => {
    setPrivateResponse(res.data)
}).catch(err => {
    setPrivateResponse(err.response.data)
})
```

以类似的方式调用受限 API，但是替换端点和状态更新函数。

![](img/34e5f78b261eba8a1e78c09c781d1add.png)

函数来处理 API 调用

![](img/14e8e6cd4a9ca464ecf8f0f7bfcd8ef4.png)

普通用户令牌的响应

# 包扎

将上述 API 视为您自己实现的模板。如果您有一个显示用户信息的页面，请实现私有 API。只有特定用户才能看到的信息属于受限 API。

您可以向 API 添加任何类型的限制。您所要做的就是传递访问令牌，解码它并检查用户是否满足这些限制。

我没有在这篇文章中添加 HTML 和 CSS 代码，因为我只想关注逻辑部分。你可以在 [GitHub](https://github.com/KunalN25/authorization-with-jwt) 上找到实现。

如果您发现任何不正确的地方或知道更好的实现方式，请在下面评论。

# 结论

授权有助于使您的 API 安全并受到限制。使用 JWT 可以很容易地实现授权。在这篇文章中，我向您展示了如何生成一个访问令牌，并使用它来访问不同类型的 API。这篇文章简单演示了如何在应用程序中实现授权。

我用简单的语言解释了每一个步骤，以帮助您理解授权。我希望这对你未来的项目有所帮助。

如果您无法理解内容或对解释不满意，请在下面评论您的想法。新想法总是受欢迎的！如果你喜欢这篇文章，请鼓掌。**订阅**、**关注**我获取每周内容。如果你想讨论什么，可以在[推特](https://twitter.com/nkunal25)上联系我。到那时，再见！

# 分级编码

感谢您成为我们社区的一员！更多内容请参见[升级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在改造理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)