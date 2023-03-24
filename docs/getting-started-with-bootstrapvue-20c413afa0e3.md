# BootstrapVue 入门

> 原文：<https://levelup.gitconnected.com/getting-started-with-bootstrapvue-20c413afa0e3>

![](img/23d162bd3a1693f5bb7716466c723a7c.png)

照片由[赫克托·阿圭略·卡纳尔斯](https://unsplash.com/@harguello?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

为了制作好看的 Vue 应用，我们需要设计组件的样式。为了使我们的生活更容易，我们可以使用内置样式的组件。

在本文中，我们将了解如何开始使用 BootstrapVue。

# 入门指南

要开始，我们需要 Vue 2.6 或更高版本。需要引导程序 4.3.1。Popper.js 是下拉菜单、工具提示和弹出窗口所必需的。jQuery 不是必需的。

# 装置

要安装它，我们通过运行以下命令来安装所需的依赖项:

```
npm install vue bootstrap-vue bootstrap
```

与 NPM 或:

```
yarn add vue bootstrap-vue bootstrap
```

用纱线。

然后，我们可以通过编写以下代码来注册插件:

```
import Vue from "vue";
import App from "./App.vue";
import { BootstrapVue } from "bootstrap-vue";Vue.use(BootstrapVue);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

在`main.js.`

我们在`BootstrapVue`上调用`Vue.use`来全球注册这个插件。

接下来，我们添加 BootstrapVue 所需的 CSS:

```
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'
```

然后我们得到:

```
import Vue from "vue";
import App from "./App.vue";
import { BootstrapVue } from "bootstrap-vue";
import "bootstrap/dist/css/bootstrap.css";
import "bootstrap-vue/dist/bootstrap-vue.css";Vue.use(BootstrapVue);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

# 挑选单个组件

如果我们只想使用单个组件，那么我们可以导入它们并自己将它们添加到一个组件中。

例如，我们可以写:

```
<template>
  <div id="app"></div>
</template><script>
import { BModal } from 'bootstrap-vue'export default {
  name: "App",
  components: {
    'b-modal': BModal
  }
};
</script>
```

然后我们只是将`BModal`组件导入到我们的组件中。

我们还可以全局注册一个组件。

例如，我们可以写:

```
import Vue from "vue";
import App from "./App.vue";
import { BModal } from "bootstrap-vue";
import "bootstrap/dist/css/bootstrap.css";
import "bootstrap-vue/dist/bootstrap-vue.css";Vue.component("b-modal", BModal);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

然后我们只在全球范围内添加了`BModal`组件。

# 警报

现在，我们可以使用应用程序中的组件。

我们可以使用`b-alert`组件添加一个警告组件。

我们可以写:

```
<template>
  <div id="app">
    <b-alert show>Alert</b-alert>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

然后我们在组件中得到一个警告框。

它还带有各种变体。

我们可以写:

```
<b-alert variant="success" show>Success</b-alert>
```

创建一个绿色的成功警报。

要添加一个可忽略的警告，我们可以写:

```
<template>
  <div id="app">
    <b-alert v-model="showAlert" variant="danger" dismissible>Dismissible Alert!</b-alert>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      showAlert: true
    };
  }
};
</script>
```

我们添加了`v-model`指令来使用`showAlert`状态来关闭模态。

我们最初将`showAlert`设置为`true`，以便我们可以看到它。

`dismissible`指令使其不可接受。这意味着我们看到一个“x ”,我们可以单击它来消除它。

我们可以通过编写以下内容来创建一个在一段时间后可以解除的警报:

```
<template>
  <div id="app">
    <b-alert
      :show="dismissCountDown"
      dismissible
      variant="warning"
      @dismissed="dismissCountDown = 0"
      @dismiss-count-down="countDownChanged"
    >
      <p>alert will dismiss {{ dismissCountDown }} seconds...</p>
      <b-progress variant="warning" :max="dismissSecs" :value="dismissCountDown" height="4px"></b-progress>
    </b-alert>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      dismissCountDown: 15,
      dismissSecs: 15
    };
  },
  methods: {
    countDownChanged(e){
      this.dismissCountDown = e;
    }
  }
};
</script>
```

我们添加了`dismissCountDown`状态，用于跟踪警报解除前的时间。

变体`'warning'`向我们显示黄色警报。

`dismissed`处理器接受警报何时解除的条件。

在这种情况下，当`dismissCountDown`为 0 时。

我们有`countDownChanged`，它接受一个带有剩余秒数的事件参数，将其设置为`dismissCountDown`来更新进度条。

# 电影《阿凡达》

我们可以通过使用`b-avatar`组件来添加一个头像。

我们可以通过书写来使用它:

```
<template>
  <div id="app">
    <b-avatar></b-avatar>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

然后我们得到一个通用的化身。

要给头像加首字母，我们可以写:

```
<template>
  <div id="app">
    <b-avatar variant="primary" text="AB"></b-avatar>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

我们将变量设置为 primary 以显示蓝色。

`text`有我们想要显示的首字母。

此外，我们可以通过编写以下内容来设置图像:

```
<template>
  <div id="app">
    <b-avatar variant="info" src="https://placekitten.com/g/100/100"></b-avatar>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

用`src`道具给头像添加图像。

![](img/80ecc9c1915a5f0276c241cc4054038c.png)

照片由 [Kouji 鹤](https://unsplash.com/@pafuxu?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们可以使用 BootstrapVue 轻松添加提醒和头像。

也很容易安装。