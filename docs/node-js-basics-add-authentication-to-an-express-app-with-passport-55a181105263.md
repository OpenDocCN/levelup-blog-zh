# Node.js 基础知识-使用 Passport 向 Express 应用程序添加身份验证

> 原文：<https://levelup.gitconnected.com/node-js-basics-add-authentication-to-an-express-app-with-passport-55a181105263>

![](img/559ebc8ec6844a552fe760b43597a105.png)

照片由[妮可·格里](https://unsplash.com/@nicolegeri?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本文中，我们将了解如何开始使用 Node.js 创建程序。

# 证明

在现实世界中，许多 HTTP 请求只能在用户通过身份验证后才能发出。我们可以在 Node.js 应用程序中轻松做到这一点。

为了让我们的生活更容易，我们使用 Express web 框架来创建我们的 HTTP 服务器，而不是使用内置的`http`模块。

我们还需要`passport`和`passport-local`来添加认证和`body-parser`包来解析请求体。

为了安装所有的包，我们运行:

```
npm install express body-parser passport passport-local
```

然后我们可以编写下面的代码来添加与`passport`的认证:

```
const express = require('express');
const bodyParser = require('body-parser');
const Passport = require('passport');
const LocalStrategy = require('passport-local').Strategy;
const users = {
  foo: {
    username: 'foo',
    password: 'bar',
    id: 1
  },
  bar: {
    username: 'bar',
    password: 'foo',
    id: 2
  }
}const localStrategy = new LocalStrategy({
    usernameField: 'username',
    passwordField: 'password'
  },
  function(username, password, done) {
    user = users[username];
    if (user === undefined) {
      return done(null, false, {
        message: 'Invalid user'
      });
    }
    if (user.password !== password) {
      return done(null, false, {
        message: 'Invalid password'
      });
    }
    done(null, user);
  })const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({
  extended: true
}));app.use(express.static('public'));
app.use(Passport.initialize());
Passport.use('local', localStrategy);app.get('/', (req, res) => {
  res.sendFile('public/index.html');
});app.post(
  '/login',
  Passport.authenticate('local', {
    session: false
  }),
  function(request, response) {
    response.send('User Id ' + request.user.id);
  }
);app.listen(3000, () => console.log('server started'));
```

我们需要`passport`和`passport-local`封装。

然后我们使用`LocalStrategy`构造函数来创建我们的认证机制。

`userField`和`passwordField`被设置为`users`对象中对象的属性，这样我们就可以获得用户名和密码的属性。

然后在我们传入第二个参数`LocalStrategy`的函数中，我们检查`user`并检查密码。

调用`done`函数，让我们根据用户名或密码的有效性返回我们想要的响应消息。

当用户名和密码都有效时，运行最后一个`done`调用。

然后我们调用`Passport.use`来创建`'local'`认证策略，这样我们就可以使用上面的代码和`Passport.authenticate`函数。

然后，我们可以使用`request.user`属性获取经过身份验证的用户的数据。

数据是通过调用以下命令获得的:

```
done(null, user);
```

现在，当我们用请求体向`/login`路由发出 POST 请求时:

```
{
    "username": "foo",
    "password": "bar"
}
```

然后我们得到:

```
User Id 1
```

从服务器返回。

如果我们有一个不在`users`对象中的用户名和密码组合，那么我们得到一个`Unauthorized`响应体和一个 401 状态码。

# 结论

为了更容易地向代码中添加身份验证，我们可以使用 Passport 来添加中间件，从而更容易地添加基本身份验证。