# React + Rails 应用程序中的 JWT 验证

> 原文：<https://levelup.gitconnected.com/jwt-auth-in-a-react-rails-app-8a7e6ba1ac0>

![](img/1b3df87f207ac583ac6dbb8ad9f3b2e8.png)

反应+轨道+ JWT

这是为 React(客户端)+ Rails(服务器端)应用程序实现 JSON Web 令牌(JWT)的指南。为了完全实现 JWT，React 和 Rails 都将被设置来处理生成的令牌。请记住，本指南将涵盖实施 JWT 的一种基本方法。

让我们从服务器端开始。

# Rails API

对于指南的这一部分，我将参考我的 jwt 后端存储库:[https://github.com/reireynoso/jwt-backend](https://github.com/reireynoso/jwt-backend)

通过将以下内容添加到 gemfile，JWT 将被集成到 API 中:

```
gem 'jwt'
```

从终端运行:

```
bundle install
```

这种回购有以下几种途径:

```
Rails.application.routes.draw do resource :users, only: [:create] post "/login", to: "auth#login" get "/auto_login", to: "auth#auto_login" get "/user_is_authed", to: "auth#user_is_authed"end
```

`resource: users, only: [: create]`处理用户注册。`post “/login”, to: “auth#login”`处理数据库中现有用户的登录。一旦用户能够成功注册/登录,`get “/auto_login”, to: “auth#auto_login”`处理自动登录。最后，`get “/user_is_authed”, to: “auth#user_is_authed”` 是只有授权用户才能访问的路线。

## 用户注册

当用户注册时，会在后端创建一个新的用户实例。将向`/users`发出一个 POST 请求，该请求将被重定向到用户控制器`create`方法。

![](img/88fbcd3c6c18f61fd0079be2d43f3459.png)

用户控制器

让我们检查一下在`create`方法中发生了什么。创建一个用户实例。如果有效，将使用用户实例的 id 创建一个有效负载对象，并将其传递给在应用程序控制器中定义的`encode_token`方法。如果需要，有效负载对象可以包含多个键。

![](img/8f9cded6963fe6566c96592eb54f8a85.png)

应用控制器 encode_token 方法

将利用 JWT gem 提供的方法`encode`来生成令牌。`JWT.encode`有两个参数，`payload`对象和一个表示“秘密”的字符串。方法`encode_token`将返回由 JWT 生成的令牌。

```
eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjo2fQ.dnjqXrQ8HWQnIiSMoM-Yt66LbXidwwtdvo2hcXoQBMU
```

成功创建用户对象和令牌后，它们都作为 JSON 对象呈现为键值对。否则，将引发并呈现一个错误。

## 用户登录

当现有用户登录时，会实现类似的方法。在**身份验证控制器中，**让我们检查一下`login`方法。

![](img/4e7925b8adea112d23f1bf845a3dffdf.png)

授权控制器登录方法

主要的区别是该方法使用`find_by`方法从传递的参数中检查用户实例，特别是用户名。然后使用`bcrypt`提供的身份验证方法来比较通过 params 传递的密码和存储在数据库中的密码是否匹配。

如果用户实例和 authenticate 方法都通过了，JWT 将生成一个令牌，并使用用户和令牌的键值对呈现一个 JSON 对象。

## 自动登录

让我们检查位于**授权控制器**中的`auto_login`方法。

![](img/7336a9a6d84b786207163f82661e4a74.png)

授权控制器自动登录

自动登录似乎很简单。如果方法`session_user`返回任何东西，它将把它呈现为一个 JSON 对象。否则，它将错误地呈现 JSON 对象。现在让我们检查一下`session_user`方法，位置在**应用控制器**中。

![](img/ad7a634937b26b2607fa1792c19ee1ea.png)

应用程序控制器会话 _ 用户方法

将一个`decoded_hash`设置为`decoded_token`方法的返回值。如果`decoded_hash`为**非**空，我们继续从`decoded_hash`获取特定信息，这将是一个数组。否则，`session_user`将返回`nil`。

只有数组的第一个索引是必需的，取出从 JWT 编码分配的有效载荷对象键。在这种情况下，只有一个键被分配给有效载荷`user_id`。将使用`find_by`方法找到与`user_id`关联的用户。

现在，让我们检查一下**应用控制器**中的`decoded_token`方法。

![](img/7153dc093c007c8903619a8d17bb94d9.png)

应用程序控制器 decoded_token 和 auth_header 方法

`decoded_token`调用另一个方法`auth_header`，该方法检查请求头中的“授权”密钥。当请求从前端发送时，它通常采用以下格式:

```
"Authorization": `Bearer ${jwt-token}`
```

使用这种格式，`auth_header`将只返回字符串:

```
`Bearer ${jwt-token}`
```

但是只有`jwt-token`是必需的。因此，在 Bearer 和 jwt-token 之间的字符串中有空格的地方调用 split 方法。返回一个包含两个元素的数组，第一个元素是 Bearer 字符串，第二个元素是 jwt-token。将只使用第二个索引。

一旦 jwt-token 被获取并分配给 token 变量，将使用 jwt 提供的`decode`方法。`JWT.decode`接受四个参数，要解码的令牌、编码时分配的秘密字符串、真值和算法(这里是‘hs 256’)。否则，如果有错误，它将返回一个空错误。

## 授权用户

API 中的许多路由都需要用户授权才能访问。在任何人可以访问该路由之前，方法`user_is_authed`必须返回 true 以显示授权证明，也就是令牌。让我们导航回到**授权控制器**，

![](img/85a76ef9a29a946995fd06379097798b.png)

应用控制器

宏`before_action`通知我们的 API 总是先运行方法`require_login`，这意味着在`require_login`允许之前`user_is_authed`不能被访问。除非方法`logged_in?`返回 true，否则`require_login`方法会返回一个 JSON 对象，并显示一条登录消息。为了检查用户是否登录，使用之前的方法`session_user`。本质上，`logged_in`使用`!`操作符检查`session_user`返回真还是假。

已经讨论了 Rails(服务器端)的基本 JWT 实现。我们现在将研究如何为 React(客户端)实现 JWT。

关于如何从头开始设置 Rails API，请参考我的指南:

[](/building-a-small-rails-api-with-serializers-32e3e69a078) [## 用序列化器构建一个小型 Rails API

### 先决条件

levelup.gitconnected.com](/building-a-small-rails-api-with-serializers-32e3e69a078) 

# React 应用

对于指南的这一部分，我将引用我的 jwt-前端库:[https://github.com/reireynoso/jwt-frontend](https://github.com/reireynoso/jwt-frontend)。

请记住，前端是使用 React 钩子用功能组件构建的，但同样的方法也适用于类组件。此外，不需要安装与 JWT 相关的软件包或库。存储由 Rails API 生成的 JWT 的方法将是重点。

## 注册/登录

用户注册和登录具有相似的功能。我们将只关注`SignIn`组件，特别是当表单被提交并且 POST 请求被发送到 Rails API `/users`时。

![](img/81f88889bdd8123b61933e26008b5b13.png)

登录组件

## 将令牌存储在本地存储中

如前所述，成功创建用户实例后，将返回包含用户和令牌的 JSON 对象。

```
render json: {user: user, jwt: token}
```

一旦 JSON 对象被返回，我们希望存储令牌，这样我们就可以保持登录状态。为此，将利用浏览器**本地存储**。

```
localStorage.setItem("token", data.jwt)
```

`LocalStorage.setItem`接受两个论点。第一个是要设置的项的名称，第二个是要设置的实际值。

## 自动登录

设置本地存储只是自动登录的第一步。必须在某个时候向`auto_login`发出请求。在加载 react 页面时自动登录是有意义的。在应用程序组件中，将使用一个`useEffect`来处理页面渲染。

![](img/a69a4b88f38173d815852c1665783dd9.png)

应用程序组件

在页面加载时，从本地存储中获取令牌并存储在令牌变量中。`getItem`接受一个参数，即您想要的密钥，在本例中名称是“token”。

```
const token = localStorage.getItem("token")
```

如果令牌存在，就向 Rails API `/auto_login`发出 GET 请求，发送与令牌相关的用户的 JSON 对象。

## 授权路线

既然 localStorage 存储了授予用户授权所必需的令牌，那么让我们研究一下如何将该令牌发送给 Rails API 以获得对经过身份验证的路由的访问。此时，我们知道令牌被存储，因为用户对象被返回。

![](img/e9053184553599d01384fa17af49d69e.png)

对页面加载做出反应

目标是将令牌传递给 Rails API。有一个名为“Access Authorized Route”的按钮，单击它会向 Rails API 发出 GET 请求。让我们检查一下 **App 组件中的回调函数，** `handleAuthClick`。

![](img/2fd90b046af60ebbf39089f4081707d4.png)

应用程序组件，handleAuthClick

当回调函数被调用时，它从本地存储中获取令牌。令牌作为设置为值`Bearer ${token}`的`Authorization`键中的值被传递给头。从 Rails API 中，它将呈现一条消息，“您已被授权。”

![](img/a6c4ce9572f694bea2adb22f913fecd4.png)

特许用户

# 结束语

本指南不包括 React 和 Rails 的 JWT 设置。相反，它提供了其实现的结构和逻辑过程的解释。请记住，这是实现 JWT 的众多方法之一。感谢您的阅读！