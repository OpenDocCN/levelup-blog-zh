# 更有用的 Vue 指令来节省我们的时间

> 原文：<https://levelup.gitconnected.com/more-useful-vue-directives-to-save-us-time-f62330883c7e>

![](img/4b7cb00a649b6f2df9e0debaa33734d4.png)

杰弗里·F·林在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在本文中，我们将看看一些节省我们时间的 Vue 指令，包括滚动和延迟加载。

# Vue-ScrollTo

我们可以使用`vue-scrollto`包以编程方式滚动到一个元素。

要安装它，我们运行:

```
npm install --save vue-scrollto
```

那么我们可以如下使用它:

`main.js`:

```
import Vue from "vue";
import App from "./App.vue";
const VueScrollTo = require("vue-scrollto");Vue.use(VueScrollTo);Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`:

```
<template>
  <div id="app">
    <button v-scroll-to="'#p-99'">Scroll to bottom</button>
    <p v-for="n in nums" :id="`p-${n}`" :key="n">{{n}}</p>
  </div>
</template><script>
export default {
  data() {
    return {
      nums: new Array(100).fill(0).map((_, i) => i)
    };
  }
};
</script>
```

在上面的代码中，我们只是将元素的选择器设置为滚动，然后当我们单击 Scroll to bottom 按钮时，它将滚动到该元素。

我们还可以更改更多选项，如下所示:

```
<template>
  <div>
    <button v-scroll-to="config">Scroll to bottom</button>
    <p v-for="n in nums" :id="`p-${n}`" :key="n">{{n}}</p>
  </div>
</template><script>
export default {
  data() {
    return {
      nums: new Array(100).fill(0).map((_, i) => i),
      config: {
        el: "#p-99",
        duration: 500,
        easing: "linear"
      }
    };
  }
};
</script>
```

在上面的代码中，我们将想要滚动到的元素的选择器移动到了`config`对象中。我们还指定了一些动画选项。

我们还可以通过编写以下代码来使滚动可编程和可取消:

```
<template>
  <div>
    <button @click="scroll">Scroll to bottom</button>
    <button @click="cancel" class="fixed">Cancel</button>
    <p v-for="n in nums" :id="`p-${n}`" :key="n">{{n}}</p>
  </div>
</template><script>
export default {
  data() {
    return {
      cancelScroll: undefined,
      nums: new Array(100).fill(0).map((_, i) => i),
      config: {
        easing: "linear",
        cancelable: true
      }
    };
  },
  methods: {
    scroll() {
      this.cancelScroll = this.$scrollTo("#p-99", 2500, this.config);
    },
    cancel() {
      this.cancelScroll && this.cancelScroll();
    }
  }
};
</script><style>
.fixed {
  position: fixed;
}
</style>
```

在上面的代码中，我们有了`scroll`方法，它用滚动到的项目的选择器、持续时间和选项`config`对象来调用`$sccrollTo`方法。

在`config`对象中，我们将`cancelable`设置为`true`，这样我们就可以用`$scrollTo`返回的函数取消它。

然后当我们点击滚动到底部，我们得到滚动，当我们点击取消，它会停止滚动。

我们还可以设置缓动，通过编写以下内容来控制动画在不同时间帧的速度:

```
[0.25, 0.1, 0.25, 1.0]
```

通过将缓动值设置为`easing`属性的值来更改缓动值。

![](img/444db27b56223e190b310c6e08aa6adc.png)

塞巴斯蒂安·拉瓦雷在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# vue-lazy 负载

`vue-lazyload`包让我们可以延迟加载我们的图像来加速页面的加载。它的工作原理是推迟图像的加载，直到它出现在屏幕上。

要安装它，我们运行:

```
npm install --save vue-lazyload
```

那么当我们可以如下使用它时:

`main.js`:

```
import Vue from "vue";
import App from "./App.vue";
import VueLazyload from "vue-lazyload";Vue.use(VueLazyload);Vue.use(VueLazyload, {
  preLoad: 1.3,
  error:
    "https://lh3.googleusercontent.com/proxy/z6LJw3nSgwQVqzu4AazrN_cJrUdkD5qnulNv-WCG_CjA8hYweNfGbcCdQ-JrbCCfBHIGnJA5DfOnb2zNSVntaMn2DJFKTOIgx80Dyx4dZDwByoX1BafHRzQvq9QTe1o",
  loading:
    "https://i.pinimg.com/originals/10/b2/f6/10b2f6d95195994fca386842dae53bb2.png",
  attempt: 1
});
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`:

```
<template>
  <div>
    <img
      v-lazy="'https://images.unsplash.com/photo-1502673530728-f79b4cab31b1?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=750&q=80'"
    >
  </div>
</template><script>
export default {};
</script>
```

在上面的代码中，我们用`v-lazy`指令加载一个图像。我们可以像在`main.js`中所做的那样，设置图像在加载时和加载失败时加载。

我们还可以如下设置错误和加载图像:

```
<template>
  <div>
    <div v-lazy-container="{ selector: 'img' }">
      <img
        data-src="https://images.unsplash.com/photo-1502673530728-f79b4cab31b1?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=750&q=80"
        data-error="https://lh3.googleusercontent.com/proxy/z6LJw3nSgwQVqzu4AazrN_cJrUdkD5qnulNv-WCG_CjA8hYweNfGbcCdQ-JrbCCfBHIGnJA5DfOnb2zNSVntaMn2DJFKTOIgx80Dyx4dZDwByoX1BafHRzQvq9QTe1o"
        data-loading="https://i.pinimg.com/originals/10/b2/f6/10b2f6d95195994fca386842dae53bb2.png"
      >
    </div>
  </div>
</template><script>
export default {};
</script>
```

`data-error`和`data-loading`分别有错误和加载状态的图像 URL。

# 结论

除了延迟加载给定的图像，我们还可以定义错误、加载图像和`v-lazy`。

`v-lazy`用于保存加载页面，将图像的加载推迟到它出现在屏幕上。

这个`vue-scrollto`包让我们增加了滚动到一个给定元素的能力，带有各种不同的缓动效果。

它还允许我们在模板中调用滚动代码，或者在代码中包含它。我们还可以在完成之前取消滚动操作。