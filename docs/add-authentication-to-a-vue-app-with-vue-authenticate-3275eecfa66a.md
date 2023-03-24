# 使用 vue-authenticate 向 Vue 应用程序添加鉴定

> 原文：<https://levelup.gitconnected.com/add-authentication-to-a-vue-app-with-vue-authenticate-3275eecfa66a>

![](img/5985f5aef8855623d13f4721e69d0c47.png)

斯科特·韦伯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

向应用程序添加身份验证总是一件苦差事。如果我们正在构建一个 Vue 应用程序，我们可以使用 vue-authenticate 插件来简化我们的工作。

在本文中，我们将了解如何使用 vue-authenticate 插件来添加身份验证。

# 入门指南

我们可以从安装几个包开始。

我们运行:

```
npm install vue-authenticate axios vue-axios
```

安装所需的软件包。

Vue-authenticate 使用 Axios 和 Vue-Axios 进行 HTTP 请求。

然后，我们可以通过编写以下代码将插件添加到我们的应用程序中:

```
import Vue from "vue";
import App from "./App.vue";
import VueAxios from "vue-axios";
import VueAuthenticate from "vue-authenticate";
import axios from "axios";Vue.use(VueAxios, axios);
Vue.use(VueAuthenticate, {
  baseUrl: "https://ClosedThirdPixels--five-nine.repl.co"
});
Vue.config.productionTip = false;new Vue({
  render: (h) => h(App)
}).$mount("#app");
```

在`main.js`里。

我们将`baseUrl`设置为 API 的基本 URL。然后我们可以用它创建一个登录表单:

```
<template>
  <div>
    <form @submit.prevent="login">
      <input v-model="email" type="text" placeholder="email">
      <input v-model="password" type="password" placeholder="password">
      <br>
      <input type="submit" value="log in">
    </form>
  </div>
</template><script>
export default {
  data() {
    return {
      email: "",
      password: ""
    };
  },
  methods: {
    async login() {
      const { email, password } = this;
      await this.$auth.login({ email, password });
    }
  }
};
</script>
```

在组件中。

我们使用`this.$auth.login`方法向[https://closedthirdpixels-five-nine.repl.co/auth/login](https://closedthirdpixels--five-nine.repl.co/auth/login)发出 POST 请求。有效载荷具有`email`和`password`字段。

在后端，我们有:

```
const express = require('express');
const bodyParser = require('body-parser');
const cors = require('cors')const app = express();
app.use(cors())
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.post('/auth/login', (req, res) => {
  res.json({})
});app.listen(3000, () => console.log('server started'));
```

来处理请求。

我们添加了`cors`中间件，这样我们就可以进行跨域请求。我们可以添加更多的代码来处理登录凭证验证。

要添加用户注册表单，我们可以编写:

```
<template>
  <div>
    <form [@submit](http://twitter.com/submit).prevent="register">
      <input v-model="name" type="text" placeholder="name">
      <input v-model="email" type="text" placeholder="email">
      <input v-model="password" type="password" placeholder="password">
      <br>
      <input type="submit" value="log in">
    </form>
  </div>
</template><script>
export default {
  data() {
    return {
      name: "",
      email: "",
      password: ""
    };
  },
  methods: {
    async register() {
      const { name, email, password } = this;
      await this.$auth.register({ name, email, password });
    }
  }
};
</script>
```

创建一个表单。

当我们提交表单时，我们调用`register`方法，该方法调用`this.$auth.register`方法向[https://closedthirdpixels-five-nine.repl.co/auth/register.](https://closedthirdpixels--five-nine.repl.co/auth/register.)发出 POST 请求

参数中的所有内容都将被发送。

然后，我们可以向后端添加一个路由来处理该请求:

```
const express = require('express');
const bodyParser = require('body-parser');
const cors = require('cors')const app = express();
app.use(cors())
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.post('/auth/login', (req, res) => {
  res.json({})
});app.post('/auth/register', (req, res) => {
  res.json({})
});app.listen(3000, () => console.log('server started'));
```

# 结论

我们可以使用 Vue-Authenticate 库向 Vue 应用添加基本身份验证。