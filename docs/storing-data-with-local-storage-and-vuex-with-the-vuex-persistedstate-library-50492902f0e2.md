# 使用本地存储器存储数据，使用 vuex 持久状态库存储 Vuex

> 原文：<https://levelup.gitconnected.com/storing-data-with-local-storage-and-vuex-with-the-vuex-persistedstate-library-50492902f0e2>

![](img/043872e5e1a95f9b4cb7913a9bfe99f7.png)

照片由 [BBH 新加坡](https://unsplash.com/@bbh_singapore?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

vuex-persistedstate 库允许我们将持久性添加到 vuex 存储中的本地数据存储中。

# 装置

我们可以通过运行以下命令来安装它:

```
npm install --save vuex-persistedstate
```

此外，我们可以用脚本标签安装它:

```
<script src="https://unpkg.com/vuex-persistedstate/dist/vuex-persistedstate.umd.js"></script>
```

然后`window.createPersistedState`就可供我们使用了。

# 在本地存储中保存 Vuex 状态

我们可以用`createPersistedState`函数将我们的 Vuex 状态保存到本地存储器。

为了使用它，我们写:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import Vuex from "vuex";
import createPersistedState from "vuex-persistedstate";Vue.use(Vuex);
Vue.config.productionTip = false;const store = new Vuex.Store({
  state: {
    count: 0
  },
  plugins: [createPersistedState()],
  mutations: {
    increment: (state) => state.count++,
    decrement: (state) => state.count--
  }
});
new Vue({
  store,
  render: (h) => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div id="app">
    <p>{{ count }}</p>
    <p>
      <button [@click](http://twitter.com/click)="increment">increment</button>
      <button [@click](http://twitter.com/click)="decrement">decrement</button>
    </p>
  </div>
</template><script>
export default {
  name: "App",
  computed: {
    count() {
      return this.$store.state.count;
    }
  },
  methods: {
    increment() {
      this.$store.commit("increment");
    },
    decrement() {
      this.$store.commit("decrement");
    }
  }
};
</script>
```

在`main.js`，我们注册了 Vuex 插件并创建了商店。

商店有`count`状态，并通过突变来改变它。

此外，我们添加了由`vuex-persistedstate`库中的`createPersistedState`函数创建的插件。

这样，我们可以将商店的状态存储在本地存储中。

在`App.vue`中，我们在`count`计算属性中获取`count`状态。

我们调用`commit`来提交改变`count`状态的突变。

# 将 Vuex 状态存储为 Cookies

或者，我们可以将 Vuex 状态存储为 cookies。为此，我们可以对 js-cookie 库使用 vuex-persistedstate。

我们通过运行来安装`js-cookie`:

```
npm i js-cookie
```

在`main.js`中，我们将其改为:

```
import Vue from "vue";
import App from "./App.vue";
import Vuex from "vuex";
import createPersistedState from "vuex-persistedstate";
import Cookies from "js-cookie";Vue.use(Vuex);
Vue.config.productionTip = false;const store = new Vuex.Store({
  state: {
    count: 0
  },
  plugins: [
    createPersistedState({
      storage: {
        getItem: (key) => Cookies.get(key),
        setItem: (key, value) =>
          Cookies.set(key, value, { expires: 3, secure: true }),
        removeItem: (key) => Cookies.remove(key)
      }
    })
  ],
  mutations: {
    increment: (state) => state.count++,
    decrement: (state) => state.count--
  }
});
new Vue({
  store,
  render: (h) => h(App)
}).$mount("#app");
```

`App.vue`保持不变。

我们传入了一个对象，这样我们就可以用`getItem`通过它的键获取数据。

`setItem`用给定键设置数据。`expires`是以天为单位的到期时间。`secure`确保 cookie 仅设置在 HTTPS 上。

`removeItem`按键删除项目。

# 安全本地存储

我们可以用`secure-ls`图书馆打乱本地存储的数据。

要使用它，我们可以将`main.js`改为:

```
import Vue from "vue";
import App from "./App.vue";
import Vuex from "vuex";
import createPersistedState from "vuex-persistedstate";
import SecureLS from "secure-ls";
const ls = new SecureLS({ isCompression: false });
Vue.use(Vuex);
Vue.config.productionTip = false;const store = new Vuex.Store({
  state: {
    count: 0
  },
  plugins: [
    createPersistedState({
      storage: {
        getItem: (key) => ls.get(key),
        setItem: (key, value) => ls.set(key, value),
        removeItem: (key) => ls.remove(key)
      }
    })
  ],
  mutations: {
    increment: (state) => state.count++,
    decrement: (state) => state.count--
  }
});
new Vue({
  store,
  render: (h) => h(App)
}).$mount("#app");
```

我们导入`secure-ls`库并创建`ls`对象，让我们从本地存储中获取、设置和删除项目。

无论保存什么都将被打乱，所以没有人能看到它的浏览器开发控制台。

# 结论

vuex-persistedstate 库是一个用于在本地存储中存储 vuex 状态的有用库。

这样，我们可以在刷新页面后保留。