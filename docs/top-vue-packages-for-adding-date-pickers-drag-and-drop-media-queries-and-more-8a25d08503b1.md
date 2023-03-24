# 用于添加日期选择器、拖放、媒体查询等的顶级 Vue 包

> 原文：<https://levelup.gitconnected.com/top-vue-packages-for-adding-date-pickers-drag-and-drop-media-queries-and-more-8a25d08503b1>

![](img/0789780d1e6643bcd3af8d8760e5ccce.png)

照片由[安布尔·基普](https://unsplash.com/@sadmax?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在本文中，我们将研究添加日期选择器、在不同数组之间拖放、媒体查询、内容加载占位符和显示祝酒词的最佳包。

# 日期选择

我们使用 Vue 日期选择器来添加日期选择器。

要安装它，我们运行:

```
npm i vue-date-pick
```

然后我们可以通过写来使用它:

```
<template>
  <date-pick v-model="date"></date-pick>
</template><script>
import DatePick from "vue-date-pick";
import "vue-date-pick/dist/vueDatePick.css";export default {
  components: { DatePick },
  data: () => ({
    date: "2020-01-01"
  })
};
</script>
```

`date-pick`有日期选择器。

我们导入 CSS 来显示正确的样式。

`v-model`将选择日期绑定到`date`状态。

# 虚拟媒体查询

v-media-query 是一个有用的 Vue 应用程序媒体查询库。

要安装它，我们运行:

```
npm i v-media-query
```

来安装它。

然后我们可以通过写来使用它:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import vMediaQuery from "v-media-query";Vue.use(vMediaQuery);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div v-if="$mq.resize && $mq.above('600px')">big div</div>
</template><script>
export default {};
</script>
```

我们注册了插件，这样我们就可以使用`$mq`属性来获取屏幕大小以及元素是否在调整大小。

`above`表示宽度大于给定宽度。

还有一个`between`方法，检查屏幕宽度是否在一定范围内。

`below`让我们检查屏幕宽度是否低于特定宽度。

# Vue 内容加载

Vue 内容加载让我们可以显示一个占位符图形，以显示正在加载的内容。

为了使用它，我们运行:

```
npm i vue-content-loading
```

然后我们可以写:

```
<template>
  <vue-content-loading :width="300" :height="100">
    <circle cx="50" cy="50" r="30"/>
  </vue-content-loading>
</template><script>
import VueContentLoading from "vue-content-loading";export default {
  components: {
    VueContentLoading
  }
};
</script>
```

去使用它。

`vue-content-loading`是让我们显示占位符元素的组件。

我们在里面放了一个 SVG 来以动画的方式显示它。

还有许多其他定制选项。

# Vue-Easy-Toast

Vue-Easy-Toast 是一个简单易用的 Vue 应用通知组件。

要使用它，首先我们通过运行以下命令来安装它:

```
npm i vue-easy-toast
```

然后我们可以写:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import Toast from "vue-easy-toast";Vue.use(Toast);Vue.config.productionTip = false;new Vue({
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
    this.$toast("hello");
  }
};
</script>
```

我们使用`$toast`方法来显示通知。

它有许多选择。我们可以改变样式、位置和过渡。

# 武-德拉古拉

vue-dragula 允许我们在应用程序中添加拖放功能。

为了使用它，我们运行:

```
npm i vue-dragula
```

然后我们可以通过写来使用它:

```
<template>
  <div>
    <div class="wrapper">
      <div class="container" v-dragula="colOne" bag="first-bag">
        <div v-for="n in colOne" :key="n">{{n}}</div>
      </div>
    </div>
  </div>
</template><script>
export default {
  data() {
    return {
      colOne: Array(10)
        .fill()
        .map((_, i) => i)
    };
  }
};
</script>
```

然后我们可以拖放数字。

`bag`让我们在列之间拖放。

我们还可以在容器之间拖放:

```
<template>
  <div>
    <div class="wrapper">
      <div class="container" v-dragula="colOne" bag="first-bag">
        <div v-for="n in colOne" :key="n">{{n}}</div>
      </div>
      <div class="container" v-dragula="colTwo" bag="first-bag">
        <div v-for="n in colTwo" :key="n">{{n}}</div>
      </div>
    </div>
  </div>
</template><script>
export default {
  data() {
    return {
      colOne: Array(10)
        .fill()
        .map((_, i) => i),
      colTwo: []
    };
  }
};
</script>
```

我们可以添加拖放功能来在数组之间移动项目。

![](img/4f29260d41d8af4f273e51f6f769255b.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Behzad Ghaffarian](https://unsplash.com/@behz?utm_source=medium&utm_medium=referral) 拍摄的照片

# 结论

Vue 日期选择器是一个有用的日期选择器。

v-media-query 允许我们在没有 CSS 的情况下检查 Vue 应用程序中的媒体查询。

vue-dragula 是一个有用的 vue 应用程序拖放库。

Vue 内容加载允许我们添加一个带有 SVGs 的内容加载占位符。

Vue-Easy-Toast 是一个展示祝酒词的库。