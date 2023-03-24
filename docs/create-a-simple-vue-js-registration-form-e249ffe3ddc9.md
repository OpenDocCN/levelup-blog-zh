# 创建一个简单的 Vue.js 注册表

> 原文：<https://levelup.gitconnected.com/create-a-simple-vue-js-registration-form-e249ffe3ddc9>

![](img/001910a5db38b8ddb6e4c11877bbc731.png)

照片由 [Christiann Koepke](https://unsplash.com/@christiannkoepke?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本文中，我们将了解如何使用 Vue 和 Express 创建注册表单。

# 后端

我们可以创建一个简单的登录表单，在后端获取一些数据。为此，我们通过安装一些软件包来创建一个快速应用程序:

```
npm i express cors body-parser
```

然后我们可以通过书写来使用它们:

```
const express = require('express');
const bodyParser = require('body-parser');
const cors = require('cors')
const app = express();
app.use(cors())
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.post('/register', (req, res) => {  
  res.json(req.body);
});app.listen(3000, () => console.log('server started'));
```

`cors`包让我们进行跨域通信。我们用的是`app.use(cors())`。

`bodyParser`解析我们将从前端提交的 JSON 请求体。`bodyParser.json()`让我们解析 JSON。

我们也有一条`register`路线来获取请求数据。

`req.body`有解析过的 JSON 数据。

# 注册表单前端

在我们添加了接受注册数据的后端之后，我们可以用 Vue.js 添加注册表单。

为此，我们可以写:

```
<template>
  <div id="app">
    <form [@submit](http://twitter.com/submit).prevent="login">
      <div>
        <label for="username">username</label>
        <input name="username" v-model="username" placeholder="username">
      </div>
      <div>
        <label for="password">password</label>
        <input name="password" v-model="password" placeholder="password" type="password">
      </div>
      <div>
        <label for="firstName">first name</label>
        <input name="firstName" v-model="firstName" placeholder="first name">
      </div>
      <div>
        <label for="lastName">last name</label>
        <input name="lastName" v-model="lastName" placeholder="last name">
      </div><div>
        <label for="age">age</label>
        <input name="age" v-model="age" placeholder="age" type="number">
      </div>
      <div>
        <label for="address">address</label>
        <input name="address" v-model="address" placeholder="address">
      </div>
      <input type="submit" value="register">
    </form>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      username: "",
      password: "",
      firstName: "",
      lastName: "",
      age: "",
      address: ""
    };
  },
  methods: {
    async login() {
      const { username, password, firstName, lastName, age, address } = this;
      const res = await fetch(
        "https://SomberHandsomePhysics--five-nine.repl.co/register",
        {
          method: "POST",
          headers: {
            "Content-Type": "application/json"
          },
          body: JSON.stringify({
            username,
            password,
            firstName,
            lastName,
            age,
            address
          })
        }
      );
      const data = await res.json();
      console.log(data);
    }
  }
};
</script>
```

我们添加了一个带有`form`元素的注册表单。

`submit.prevent` listen 在提交时运行`login`方法，同时运行`preventDefault`。

表单域由`label`和`input`标签创建。

`for`具有输入字段的`name`属性值。

`v-model`绑定到我们提交的值。

我们还设置了一些输入的`type`属性。

`password`用于密码输入。

`number`用于数字输入。

`login`方法调用`fetch`向后端发出 POST 请求。

我们在第一行中获得了所有想要提交的反应属性。然后我们把它们都放在 JSON 字符串中。

`headers`必须将`Content-Type`设置为`application/json`才能提交 JSON。

一旦发出请求，我们就从`register`路由得到响应。

# 结论

我们可以用 Vue.js 创建一个带有`v-model`指令和`fetch`函数的注册表单来发出请求。