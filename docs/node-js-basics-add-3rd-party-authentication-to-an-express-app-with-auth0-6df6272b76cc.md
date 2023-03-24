# Node.js 基础知识-使用 Auth0 向 Express 应用程序添加第三方身份验证

> 原文：<https://levelup.gitconnected.com/node-js-basics-add-3rd-party-authentication-to-an-express-app-with-auth0-6df6272b76cc>

![](img/4facfdee780ef7dceaafee4832081c00.png)

[Pim Myten](https://unsplash.com/@pimmyten?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我们将了解如何开始使用 Node.js 创建程序。

# 在 Auth0 中设置我们的应用

在我们编写应用程序之前，我们必须在 Auth0 中设置我们的节点应用程序。

为了在 Auth0 中创建我们的应用程序，我们登录 Auth0，然后转到[https://manage . auth 0 . com/dashboard/us/dev-v7h 077 Zn/Applications](https://manage.auth0.com/dashboard/us/dev-v7h077zn/applications)以转到应用程序页面。

然后，我们单击“创建应用程序”来创建新的应用程序。

完成后，我们单击常规 Web 应用程序，然后单击创建。

然后，我们单击 Node.js 来创建我们的应用程序。

# 创建快速应用程序

一旦我们创建了 Express 应用程序，我们就可以编写以下代码来创建我们的应用程序:

```
const express = require('express');
const bodyParser = require('body-parser');
const { auth } = require('express-openid-connect');
const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({
    extended: true
}));const config = {
    authRequired: false,
    auth0Logout: true,
    secret: 'a long, randomly-generated string stored in env',
    baseURL: 'http://localhost:3000',
    clientID: 'client id',
    issuerBaseURL: 'https://<domain>.us.auth0.com'
};// auth router attaches /login, /logout, and /callback routes to the baseURL
app.use(auth(config));// req.isAuthenticated is provided from the auth router
app.get('/', (req, res) => {
    res.send(req.oidc.isAuthenticated() ? 'Logged in' : 'Logged out');
});app.listen(3000, () => console.log('server started'));
```

我们安装了`express-openid-connect`包，让我们用 Open ID 添加认证。

要安装它，我们运行:

```
npm i express-openid-connect
```

安装软件包。

我们调用`app.use(auth(config));`来添加登录、注销的路由，以及认证成功后调用的回调路由。

然后我们可以去`/`路线，看看我们是否被认证。

我们通过调用`req.oidc.isAuthenticated()`方法来检查我们是否被认证。

`config`对象有`clientID`、`issuerBaseURL`、`baseURL`和`secret` 属性。

我们可以从我们的 Auth0 帐户中的应用程序页面获取所有这些信息。

`issuerBaseURL`是托管我们应用的域。

现在，当我们进入`http://localhost:3000/login`时，我们应该能够使用 Google 帐户登录。

# 获取登录用户的数据

要获取当前登录用户的数据，我们可以编写:

```
const express = require('express');
const bodyParser = require('body-parser');
const { auth, requiresAuth } = require('express-openid-connect');
const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({
    extended: true
}));const config = {
    authRequired: false,
    auth0Logout: true,
    secret: 'a long, randomly-generated string stored in env',
    baseURL: 'http://localhost:3000',
    clientID: 'client id',
    issuerBaseURL: 'https://<domain>.us.auth0.com'
};// auth router attaches /login, /logout, and /callback routes to the baseURL
app.use(auth(config));// req.isAuthenticated is provided from the auth router
app.get('/', (req, res) => {
    res.send(req.oidc.isAuthenticated() ? 'Logged in' : 'Logged out');
});app.get('/profile', requiresAuth(), (req, res) => {
    res.send(JSON.stringify(req.oidc.user));
});app.listen(3000, () => console.log('server started'));
```

我们添加了带有由`requiresAuth`函数返回的中间件的`/profile`路由，使其只对经过身份验证的用户可用。

那么`req.oidc.user`属性有用户数据。

一旦我们登录到 Google 帐户，我们应该会看到来自`/profile`路线的数据。

# 结论

我们可以使用 Auth0 轻松地将第三方认证添加到我们的 Express 应用程序中。