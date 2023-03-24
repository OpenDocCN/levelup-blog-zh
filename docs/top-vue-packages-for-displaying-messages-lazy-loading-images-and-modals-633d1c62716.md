# 用于显示消息、延迟加载图像和模态的顶级 Vue 包

> 原文：<https://levelup.gitconnected.com/top-vue-packages-for-displaying-messages-lazy-loading-images-and-modals-633d1c62716>

![](img/da66eff0e0a8999c8ba43e5ca42ce482.png)

照片由 [Pixzolo 摄影](https://unsplash.com/@pixzolo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本文中，我们将看看显示消息、延迟加载图像和添加模态的最佳包。

# vue-toastr

vue-toastr 是 vue 应用程序的 toast 组件。

为了使用它，我们运行:

```
npm i vue-toastr
```

来安装它。

然后我们可以通过写来使用它:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import VueToastr from "vue-toastr";
Vue.use(VueToastr, {});Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div></div>
</template><script>
export default {
  mounted() {
    this.$toastr.defaultPosition = "toast-top-left";
    this.$toastr.s("<b>hello</b>");
  }
};
</script>
```

我们通过注册一个插件来创建一个 toast。

然后，我们使用`$toastr`属性设置默认位置，并使用`s`方法显示 toast。

支持 HTML。

其他选项，如超时、样式等。可以设置。

# 薄莫代尔

如果我们想轻松地将模型添加到我们的应用程序中，我们可以使用 vue-thin-modal 包来完成。

要安装它，我们运行:

```
npm i vue-thin-modal
```

然后我们可以通过写来使用它:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import VueThinModal from "vue-thin-modal";
import "vue-thin-modal/dist/vue-thin-modal.css";Vue.use(VueThinModal);Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div>
    <button type="button" @click="open">Open Modal</button>
    <modal name="demo">
      <div class="basic-modal">
        <h1 class="title">Title</h1>
        <button class="button" type="button" @click="close">Close Modal</button>
      </div>
    </modal>
  </div>
</template>

<script>
export default {
  methods: {
    open() {
      this.$modal.push("demo");
    }, close() {
      this.$modal.pop();
    }
  }
};
</script>
```

我们注册组件并导入 CSS。

然后我们使用`modal`组件来添加模态。

`this.$modal.push`打开模态。

参数是`modal`的`name`道具的值。

要关闭它，我们调用`pop`方法。

# 懒惰图像

v-lazy-image 是一个图像组件，它只在图像显示在屏幕上时才加载图像。

为了使用它，我们运行:

```
npm i v-lazy-image
```

然后我们可以写:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import { VLazyImagePlugin } from "v-lazy-image";Vue.use(VLazyImagePlugin);Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div>
    <v-lazy-image
      src="http://placekitten.com/200/200"
      src-placeholder="http://placekitten.com/201/201"
    />
  </div>
</template>

<script>
export default {};
</script>
```

去使用它。

`src`是实际图像的 URL。

`src-placeholder`是占位符图像的 URL。

我们可以逐步加载图像。

为此，我们可以用 CSS 添加过渡效果。

例如，我们可以写:

```
<template>
  <div>
    <v-lazy-image
      src="http://placekitten.com/200/200"
      src-placeholder="http://placekitten.com/201/201"
    />
  </div>
</template>

<script>
export default {};
</script><style scoped>
.v-lazy-image {
  filter: blur(20px);
  transition: filter 0.7s;
}
.v-lazy-image-loaded {
  filter: blur(0);
}
</style>
```

我们只是在`v-lazy-image`类中添加了过渡效果，以便在图像加载时添加过渡效果。

也支持响应式图像。

# vue-flash-消息

vue-flash-message 是一个让我们在屏幕上显示消息的包。

要安装它，我们运行:

```
npm i vue-flash-message
```

然后我们可以通过写来使用它:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import VueFlashMessage from "vue-flash-message";
Vue.use(VueFlashMessage);Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div>
    <flash-message></flash-message>
  </div>
</template>

<script>
export default {
  mounted() {
    this.flash("loaded", "success");
  }
};
</script>
```

我们使用`flash-message`组件来显示消息，该消息将显示几秒钟。

我们调用`this.flash`来显示消息。

第一个论点是内容。

第二个参数是类型。

我们可以添加软件包附带的 CSS 来添加样式:

```
import Vue from "vue";
import App from "./App.vue";
import VueFlashMessage from "vue-flash-message";
import "vue-flash-message/dist/vue-flash-message.min.css";Vue.use(VueFlashMessage);Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

其他种类的消息包括错误、警告和信息。

![](img/e7ab3648e3f5a9f124561a3ca10dbf1d.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Calum Lewis](https://unsplash.com/@calumlewis?utm_source=medium&utm_medium=referral) 拍摄的照片

# 结论

我们可以使用 vue-toastr 或 vue-flash-messag 来显示消息。

vue-thin-modal 是 vue 应用程序的一个模态组件。

v-lazy-image 是一个延迟加载图像的组件。