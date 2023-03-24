# 用于添加旋转木马、无限滚动和动态模板的顶级 Vue 包

> 原文：<https://levelup.gitconnected.com/top-vue-packages-for-adding-carousels-infinite-scroll-and-dynamic-templates-8b410c5d38cc>

![](img/c3fc3d29c3f4140ed9a0e61b6a272cad.png)

杰西卡·富特尼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在这篇文章中，我们将看看添加传送带、无限滚动和动态模板的最佳包。

# vue-敏捷

vue-agile 是一个简单易用、可定制的 vue 应用转盘

要安装它，我们运行:

```
npm i vue-agile
```

然后我们可以通过写来使用它:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import VueAgile from "vue-agile";Vue.use(VueAgile);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div>
    <agile>
      <div class="slide">
        <h3>slide 1</h3>
      </div> <div class="slide">
        <h3>slide 2</h3>
      </div>
    </agile>
  </div>
</template>

<script>
export default {};
</script>
```

我们在`main.js`注册插件。

然后我们使用`agile`组件来添加转盘。

我们用`slide`类添加 div 来添加幻灯片。

我们会看到在幻灯片之间导航的按钮。

它有许多其他选项，如计时、速度、节流、初始滑动等。

# 无限卷轴

vue-infinite-scroll 是一个 vue 插件，可以让我们在应用程序中添加无限滚动。

要安装它，我们可以写:

```
npm i vue-infinite-scroll
```

然后我们可以通过写来使用它:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import infiniteScroll from "vue-infinite-scroll";
Vue.use(infiniteScroll);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div>
    <div v-infinite-scroll="loadMore" infinite-scroll-disabled="busy" infinite-scroll-distance="10">
      <div v-for="n in num" :key="n">{{n}}</div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      num: 50,
      busy: false
    };
  },
  methods: {
    loadMore() {
      this.num += 50;
    }
  }
};
</script>
```

我们注册了`infiniteScroll`插件。

然后我们添加了`v-infinite-scroll`指令来支持组件的无限滚动。

然后里面的 div 可以无限滚动加载。

`infinite-scroll-disabled`让我们在加载数据时禁用无限滚动。

`infinite-scroll-distance`是在我们加载更多数据之前，屏幕剩余的百分比。

10 意味着当我们还有 10%的屏幕空间可以滚动时，我们可以加载更多的数据。

# 运行时模板

v-runtime-template 允许我们在组件中加载 Vue 模板。

没有这个包，我们可以用`v-html`加载 HTML，但是不能加载任何带有组件标签、指令或者其他 Vue 实体的东西。

要安装它，我们运行:

```
npm i v-runtime-template
```

然后我们可以写:

`components/HelloWorld.vue`

```
<template>
  <div class="hello">hello</div>
</template><script>
export default {
  name: "HelloWorld"
};
</script>
```

`App.vue`

```
<template>
  <div>
    <v-runtime-template :template="template"></v-runtime-template>
  </div>
</template>

<script>
import VRuntimeTemplate from "v-runtime-template";
import HelloWorld from "@/components/HelloWorld";export default {
  data: () => ({
    template: `
      <hello-world></hello-world>
    `
  }),
  components: {
    VRuntimeTemplate,
    HelloWorld
  }
};
</script>
```

我们使用带有`template`道具的`v-runtime-template`组件来设置模板。

此外，我们导入了`HelloWorld`组件，这样我们就可以将它包含在模板中。

我们也可以像往常一样传入道具:

`component/HelloWorld.vue`:

```
<template>
  <div class="hello">hello {{name}}</div>
</template><script>
export default {
  name: "HelloWorld",
  props: ["name"]
};
</script>
```

`App.vue`

```
<template>
  <div>
    <v-runtime-template :template="template"></v-runtime-template>
  </div>
</template>

<script>
import VRuntimeTemplate from "v-runtime-template";
import HelloWorld from "@/components/HelloWorld";export default {
  data: () => ({
    template: `
      <hello-world name='world'></hello-world>
    `
  }),
  components: {
    VRuntimeTemplate,
    HelloWorld
  }
};
</script>
```

# Vue 材料设计图标组件

我们可以使用 Vue 材质设计图标组件包将材质设计图标添加到我们的 Vue 应用程序中。

首先，我们通过运行以下命令安装它:

```
npm i vue-material-design-icons
```

然后，我们可以将以下内容添加到组件中:

```
<template>
  <div>
    <menu-icon/>
  </div>
</template>

<script>
import MenuIcon from "vue-material-design-icons/Menu.vue";export default {
  components: {
    MenuIcon
  }
};
</script>
```

我们只需导入图标，然后注册组件并使用它。

![](img/4b44bc20fed8e85f6d52933a3d19a5ed.png)

维克多·马柳舍夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

vue-agile 允许我们添加一个旋转木马。

Vue 材质设计图标组件是一组带有材质设计图标的组件。

v-runtime-template 允许我们添加包含 Vue 代码的动态模板。

让我们添加无限滚动。