# 用于添加计时器、社交共享链接、工具提示和回调引用的顶级 Vue 包

> 原文：<https://levelup.gitconnected.com/top-vue-packages-for-adding-timers-social-sharing-links-tooltips-and-callback-refs-10a2e8847456>

![](img/58711dd2ed2a9ff413217d311240a582.png)

马丁·莫雷诺在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在这篇文章中，我们将看看添加计时器、社交共享链接、工具提示和回调引用的最佳包。

# vue 计时器

vue-timers 是一个简单的 mixin，允许我们在 vue 应用程序中添加类似于`setTimeout`和`setInterval`的计时器。

要使用它，我们首先通过编写以下内容来安装它:

```
npm i vue-timers
```

然后我们可以通过写来使用它:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import VueTimers from "vue-timers";Vue.use(VueTimers);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div></div>
</template>

<script>
export default {
  timers: {
    log: { time: 1000, autostart: true }
  },
  methods: {
    log() {
      console.log("Hello world");
    }
  }
};
</script>
```

我们导入包并注册它。

然后我们使用`this.$options.interval`来存储`setInterval`返回的对象。

这个包的好处是我们可以添加可重用的定时器，而不用添加我们自己的代码。

清理也是自动的。

我们只是添加了`timers`对象来调用`setTimeout`。

`autostart`表示定时器自动运行。

`time`是以毫秒为单位的延迟。

我们也可以通过编写以下代码以编程方式调用它:

```
<template>
  <div></div>
</template>

<script>
export default {
  timers: {
    log: { time: 1000, autostart: true },
    foo: { time: 1000, autostart: false }
  },
  methods: {
    log() {
      console.log("Hello world");
    },
    foo() {
      console.log("foo");
    }
  },
  mounted() {
    this.$timer.start("foo");
  }
};
</script>
```

我们添加了`foo`计时器，我们可以用`this.$timer.start`自己运行它。

# vue-社交分享

vue-social-sharing 允许我们在代码中添加社交共享小部件。

要安装它，我们运行:

```
npm i vue-social-sharing
```

然后我们可以通过写来使用它:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import VueSocialSharing from "vue-social-sharing";Vue.use(VueSocialSharing);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div>
    <ShareNetwork
      network="facebook"
      url="https://example.com"
      title="hello"
      description="description"
      quote="quote"
      hashtags="vuejs,vite"
    >Share on Facebook</ShareNetwork>
  </div>
</template>

<script>
export default {};
</script>
```

我们用给定的标题和描述分享到脸书的`https://example.com`链接。

我们还可以添加一个引用。

我们将`network`设置为`facebook`以共享到脸书的链接。

它支持许多其他社交网络，如 Twitter、Instagram 等。

# vue-popperjs

vue-popperjs 让我们可以轻松地在应用程序中添加工具提示。

要安装它，我们运行:

```
npm i vue-popperjs
```

然后我们可以通过写来使用它:

```
<template>
  <div>
    <popper
      trigger="clickToOpen"
      :options="{
      placement: 'top',
      modifiers: { offset: { offset: '0,10px' } }
    }"
    >
      <div class="popper">content</div> <button slot="reference">click me</button>
    </popper>
  </div>
</template>

<script>
import Popper from "vue-popperjs";
import "vue-popperjs/dist/vue-popper.css";export default {
  components: {
    popper: Popper
  }
};
</script>
```

我们使用捆绑的`popper`组件。

`trigger`道具让我们设置如何打开工具提示。

`clickToOpen`表示点击即可打开。

`reference`槽有用来打开工具提示的元素。

默认槽有内容。

我们还导入 CSS。

我们也可以将`offset`设置为我们想要的。

让我们设置工具提示的位置。

# vue-ref

vue-ref 允许我们使用一个 direct 来设置一个带有回调的 ref。

要安装它，我们运行:

```
npm i vue-ref
```

然后我们通过书写来使用它:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import ref from "vue-ref";
Vue.use(ref);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div>
    <input v-ref="c => this.dom = c">
  </div>
</template>

<script>
export default {
  data() {
    return {
      dom: undefined
    };
  },
  mounted() {
    this.dom.focus();
  }
};
</script>
```

我们使用`v-ref`指令获取元素，并将其设置为`this.dom`的值。

然后我们可以在输入上调用`focus`来聚焦它。

![](img/e34972b82a3901f6bc467d84d887f25c.png)

由[rafarudol](https://unsplash.com/@rudol?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

vue-timers 包让我们可以比使用`setTimeout`和`setInterval`更容易地将计时器添加到组件中。

vue-social-sharing 让我们可以添加社交分享链接。

vue-popperjs 允许我们添加灵活样式的工具提示。

vue-ref 允许我们添加回调引用。