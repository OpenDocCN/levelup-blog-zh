# 使用 vue-agile 库向 Vue 应用添加滑块

> 原文：<https://levelup.gitconnected.com/add-a-slider-to-a-vue-app-with-the-vue-agile-library-aef57347af5a>

![](img/5890254ae2a1e2031da855790996091e.png)

照片由[大卫·特拉维斯](https://unsplash.com/@dtravisphd?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

有了 vue-agile 库，我们可以轻松地向我们的 vue 应用添加一个旋转木马。

# 装置

我们可以通过运行以下命令来安装它:

```
yarn add vue-agile
```

或者

```
npm install vue-agile
```

该库没有提供任何样式，所以我们必须自己添加。

我们可以有选择地将 CSS 添加到`public/index.html`中的`head`标签中，方法是:

```
<link
  rel="stylesheet"
  href="https://unpkg.com/vue-agile/dist/VueAgile.css"
/>
```

# 添加旋转木马

一旦我们安装了这个包，我们就可以通过编写以下代码来使用它:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import VueAgile from "vue-agile";Vue.use(VueAgile);
Vue.config.productionTip = false;new Vue({
  render: (h) => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div id="app">
    <agile>
      <div class="slide" v-for="n of 10" :key="n">
        <h3>slide {{n}}</h3>
      </div>
    </agile>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

我们在`main.js`注册插件。

那么我们可以在任何组件中使用它。

`agile`组件拥有转盘容器。

div 是幻灯片。

现在，我们应该可以看到底部带有导航按钮的幻灯片。

# 响应滑块

我们可以在幻灯片中添加许多选项，包括添加断点以使其具有响应性。

例如，我们可以写:

```
<template>
  <div id="app">
    <agile :options="options">
      <div class="slide" v-for="n of 10" :key="n">
        <h3>slide {{n}}</h3>
      </div>
    </agile>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      options: {
        navButtons: false,
        responsive: [
          {
            breakpoint: 600,
            settings: {
              dots: false
            }
          },
          {
            breakpoint: 900,
            settings: {
              navButtons: true,
              dots: true,
              infinite: false
            }
          }
        ]
      }
    };
  }
};
</script>
```

我们有`options`和`responsive`属性的反应属性。

`breakpoint`有断点，让我们决定给定断点显示什么。

`navButtons`表示是否显示导航按钮。

`dots`让我们显示点或不显示点。

`infinite`表示我们是否要永远循环浏览这些项目。

# 自定义箭头和导航按钮

我们可以填充`prevButton`和`nexButton`插槽，以添加用于转到上一张和下一张幻灯片的按钮。

例如，我们可以写:

```
<template>
  <div id="app">
    <agile>
      <div class="slide" v-for="n of 10" :key="n">
        <h3>slide {{n}}</h3>
      </div>
      <template slot="prevButton">prev</template>
      <template slot="nextButton">next</template>
    </agile>
  </div>
</template><script>
export default {
  name: "App"
};
</script>
```

内容将作为他们的内容位于按钮内部。

# 标题

此外，我们可以填充`caption`槽来添加我们自己的标题。

例如，我们可以写:

```
<template>
  <div id="app">
    <agile @after-change="e => currentSlide = e.currentSlide">
      <div class="slide" v-for="n of 10" :key="n">
        <h3>slide {{n}}</h3>
      </div>
      <template slot="caption">caption {{ currentSlide }}</template>
    </agile>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return {
      currentSlide: 0
    };
  }
};
</script>
```

我们监听由`agile`发出的`after-change`事件，以获取带有`currentSlide`属性的当前幻灯片。

然后我们可以用它填充`caption`,在标题中显示当前的幻灯片索引。

# 结论

vue-agile 库是一个灵活易用的 vue 应用库。