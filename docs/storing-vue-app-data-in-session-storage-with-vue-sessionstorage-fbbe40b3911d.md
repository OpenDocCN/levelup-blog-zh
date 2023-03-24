# 使用 vue-sessionstorage 将 Vue 应用程序数据存储在会话存储器中

> 原文：<https://levelup.gitconnected.com/storing-vue-app-data-in-session-storage-with-vue-sessionstorage-fbbe40b3911d>

![](img/099beb9c758c34d3830a081829c42884.png)

马克·森德拉·马托雷尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们可以使用 vue-sessionstorage 库在浏览器的会话存储中存储 vue 应用程序数据。

# 装置

我们可以通过运行以下命令来安装该库:

```
npm i vue-sessionstorage
```

# 将数据保存在本地存储中

安装库后，我们可以通过写入以下内容将数据保存在本地存储中:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import VueSessionStorage from "vue-sessionstorage";
Vue.use(VueSessionStorage);
Vue.config.productionTip = false;new Vue({
  render: (h) => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div id="app"></div>
</template><script>
export default {
  name: "App",
  mounted() {
    this.$session.set("username", "user123");
  }
};
</script>
```

我们只需在`main.js`注册插件。

然后我们有了可以在任何地方使用的`this.$session`对象。

我们用想要保存的数据的键和值调用`set`来保存它。

为了得到数据，我们可以使用`this.$session.get`方法:

```
<template>
  <div id="app"></div>
</template><script>
export default {
  name: "App",
  mounted() {
    this.$session.set("username", "user123");
    console.log(this.$session.get("username"));
  }
};
</script>
```

`this.$session.exists`方法检查一个键是否存在:

```
<template>
  <div id="app"></div>
</template><script>
export default {
  name: "App",
  mounted() {
    this.$session.set("username", "user123");
    console.log(this.$session.exists("username"));
  }
};
</script>
```

`this.$session.remove`方法删除带有给定键的项目:

```
<template>
  <div id="app"></div>
</template><script>
export default {
  name: "App",
  mounted() {
    this.$session.set("username", "user123");
    console.log(this.$session.remove("username"));
  }
};
</script>
```

`this.$session.clear`方法用给定的键清除会话存储数据:

```
<template>
  <div id="app"></div>
</template><script>
export default {
  name: "App",
  mounted() {
    this.$session.set("username", "user123");
    console.log(this.$session.clear("username"));
  }
};
</script>
```

使用会话存储，同一应用程序的不同实例可以在各自的会话中存储不同的数据。

数据既可以按原点设置，也可以按实例设置，即按窗口或标签设置。

因此,`clear`方法将用给定的键清除所有数据。

# 结论

我们可以使用 vue-sessionstorage 库将数据保存到会话存储中。