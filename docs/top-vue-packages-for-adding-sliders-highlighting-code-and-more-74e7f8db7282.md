# 用于添加滑块、突出显示代码等的顶级 Vue 包

> 原文：<https://levelup.gitconnected.com/top-vue-packages-for-adding-sliders-highlighting-code-and-more-74e7f8db7282>

![](img/ab29549c5568eeabeea4b90bfc9a0d1b.png)

照片由[迈克·本纳](https://unsplash.com/@mbenna?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

在本文中，我们将看看添加自动滚动、数字动画、高亮代码、滑块和显示通知的最佳包。

# 无缝滚动

vue-seamless-scroll 允许我们添加一个组件来自动滚动它的内容。

为了使用它，我们运行:

```
npm i vue-seamless-scroll
```

来安装它。

然后我们可以写:

```
<template>
  <vue-seamless-scroll :data="listData" class="seamless-warp">
    <ul class="item">
      <li v-for="item in listData" :key="item">
        <span v-text="item"></span>
      </li>
    </ul>
  </vue-seamless-scroll>
</template><script>
import vueSeamlessScroll from "vue-seamless-scroll";export default {
  components: {
    vueSeamlessScroll
  },
  data() {
    return {
      listData: Array(20)
        .fill()
        .map((_, i) => i)
    };
  }
};
</script>
```

我们注册组件。

然后我们使用`vue-seamless-scroll`组件。

我们设置`data`属性来设置滚动数据。

然后我们循环遍历想要呈现的条目。

里面的任何内容都将被连续滚动。

它会循环，所以它会一直从头到尾滚动，直到我们离开页面。

# 动画数字视频

动画数字 vue 让我们在 Vue 应用程序中制作数字动画。

为了使用它，我们运行:

```
npm i animated-number-vue
```

来安装它。

然后我们通过书写来使用它:

```
<template>
  <animated-number :value="value" :formatValue="formatToPrice" :duration="300"/>
</template>
<script>
import AnimatedNumber from "animated-number-vue";export default {
  components: {
    AnimatedNumber
  },
  data() {
    return {
      value: 1000
    };
  },
  methods: {
    formatToPrice(value) {
      return `$ ${value.toFixed(2)}`;
    }
  }
};
</script>
```

我们使用`animated-number`组件来显示动画数字。

`duration`是动画的长度。

`value`是动画的结束编号。

# Vue 旋转木马 3d

Vue Carousel 3d 是一个用于 Vue 应用程序的 3d 转盘。

为了使用它，我们运行:

```
npm i vue-carousel-3d
```

来安装它。

然后我们写道:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import Carousel3d from "vue-carousel-3d";Vue.use(Carousel3d);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div>
    <carousel-3d>
      <slide :index="0">Slide 1</slide>
      <slide :index="1">Slide 2</slide>
      <slide :index="2">Slide 3</slide>
    </carousel-3d>
  </div>
</template>

<script>
export default {};
```

去使用它。

我们使用至少有 3 张幻灯片的`carousel-3d`。

`slide`组件容纳滑块。

然后我们可以浏览幻灯片。

# Vue Toast 通知

Vue Toast Notification 是一个 Vue 插件，可以让我们在 Vue 应用程序中显示 Toast。

要使用它，我们通过运行以下命令来安装它:

```
npm i vue-toast-notification
```

然后我们可以通过写来使用它:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import VueToast from "vue-toast-notification";
import "vue-toast-notification/dist/theme-default.css";Vue.use(VueToast);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div id="app"></div>
</template><script>
export default {
  mounted() {
    this.$toast.open("hello!");
  }
};
</script>
```

我们导入 CSS 并注册库。

然后我们调用`this.$toast.open`打开一个基本通知。

我们还显示错误、警告和信息消息的通知。

此外，我们可以写:

```
<template>
  <div id="app"></div>
</template><script>
export default {
  mounted() {
    this.$toast.open({
      message: "Hello Vue",
      type: "success",
      duration: 5000,
      dismissible: true
    });
  }
};
</script>
```

来设定我们自己的选择。

使它变得不可理喻。

`message`是消息。

`type`是吐司的种类。

`duration`是显示 toast 的时间长度。

# vue-hljs

vue-hljs 允许我们在我们的 vue 应用程序中用语法高亮显示代码。

为了使用它，我们运行:

```
npm i vue-hljs
```

来安装它。

然后我们写道:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import vueHljs from "vue-hljs";
import "vue-hljs/dist/vue-hljs.min.css";Vue.use(vueHljs);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div id="app">
    <div v-highlight>
      <pre>
        <code class="javascript">const foo = 'bar'</code>
      </pre>
    </div>
  </div>
</template><script>
export default {};
</script>
```

来显示代码。

我们只是使用`v-highlight`指令来添加语法高亮显示。

![](img/867c1b4675c7a83384797857f2139ae5.png)

由[马丁·莫雷诺](https://unsplash.com/@memoreno?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 结论

vue-hljs 允许我们在代码显示中添加语法高亮显示。

vue-无缝滚动使滚动自动化。

Vue Toast 通知以各种格式显示通知。

Vue Carousel 3d 允许我们添加一个 3d 滑块。

数字动画。