# 使用 Vue 会话插件存储会话数据

> 原文：<https://levelup.gitconnected.com/storing-session-data-with-the-vue-session-plugin-bed36c1e4e09>

![](img/10133637d7ef27de631c43c71f82ca3f.png)

Marc Sendra Martorell[在](https://unsplash.com/@marcsm?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

使用 Vue 会话插件可以更容易地存储会话数据。

# 入门指南

我们通过运行以下命令来安装该软件包:

```
npm i vue-session
```

然后我们可以在`main.js`中通过写:

```
import Vue from "vue";
import App from "./App.vue";
import VueSession from "vue-session";
Vue.use(VueSession);
Vue.config.productionTip = false;new Vue({
  render: (h) => h(App)
}).$mount("#app");
```

然后我们可以通过写来使用它:

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
import axios from "axios";
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
      const {
        data: { token }
      } = await axios.post(
        "https://ClosedThirdPixels--five-nine.repl.co/auth/login",
        {
          password: password,
          email: email
        }
      );
      this.$session.start();
      this.$session.set("jwt", token);
    }
  }
};
</script>
```

我们创建了一个允许我们登录的表单。

在`login`方法中，我们用 Axios 发出 POST 请求。

一旦成功，我们调用`$session.start`方法来创建会话。

我们的后端可能是这样的:

```
const express = require('express');
const bodyParser = require('body-parser');
const cors = require('cors')const app = express();
app.use(cors())
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.post('/auth/login', (req, res) => {
  res.json({
    token: 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
  })
});app.post('/auth/register', (req, res) => {
  res.json({})
});app.listen(3000, () => console.log('server started'));
```

归还代币。

只有当用户名和密码有效时，我们才会返回令牌。然后我们可以用我们自己的键值对填充它。

为了从会话中获取数据，我们可以使用`this.$session.get`方法。例如，我们可以写:

```
$session.get('jwt')
```

用`jwt`键获取会话数据。

为了检查会话是否存在，我们调用`this.$session.exists()`。

用给定的键移除某物，我们调用`this.$session.remove()`。

为了清除所有数据，我们调用`this.$session.clear()`。

`this.$session.id()`返回会话 ID。

`this.$session.renew()`更新会话。

`this.$session.destroy()`销毁会话。

所以我们可以写:

```
<template>
  <div>
    <form [@submit](http://twitter.com/submit).prevent="login">
      <input v-model="email" type="text" placeholder="email">
      <input v-model="password" type="password" placeholder="password">
      <br>
      <input type="submit" value="log in">
      <button type="button" [@click](http://twitter.com/click)="logout">log out</button>
    </form>
  </div>
</template><script>
import axios from "axios";
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
      const {
        data: { token }
      } = await axios.post(
        "https://ClosedThirdPixels--five-nine.repl.co/auth/login",
        {
          password: password,
          email: email
        }
      );
      this.$session.start();
      this.$session.set("jwt", token);
    },
    logout() {
      this.$session.destroy();
    }
  }
};
</script>
```

添加一个`logout`方法来销毁与`this.session.$destroy()`的会话。

# 闪光

我们可以使用`flash`属性保存数据，直到我们读取它们，而不必启动一个常规会话。

`this.$session.flash.set(key, value)`设置闪光值。

`this.$session.flash.get(key)`读取并删除闪存值。

`this.$session.flash.remove(key)`删除一个闪光值。

# 结论

Vue Session 是一个易于使用的库，用于在 Vue 应用程序中存储会话。