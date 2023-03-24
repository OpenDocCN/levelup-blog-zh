# 用于添加通知、渐进式图像加载等的顶级 Vue 包

> 原文：<https://levelup.gitconnected.com/top-vue-packages-for-adding-notifications-progressive-image-loading-and-more-9a58385ed6eb>

![](img/fb02092c5fe2edde34c7c38896810c73.png)

托马斯·博诺梅蒂在 Unsplash 的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在本文中，我们将看看添加通知、渐进式图像加载、涟漪效应和密码输入的最佳包。

# 武埃斯-诺蒂

vuejs-noty 是 Vue 应用程序的通知库。

为了使用它，我们运行:

```
npm i vuejs-noty
```

来安装它。

然后我们可以写:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import VueNoty from "vuejs-noty";
import "vuejs-noty/dist/vuejs-noty.css";Vue.use(VueNoty);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

注册组件并导入 CSS。

`App.vue`

```
<template>
  <div></div>
</template>

<script>
export default {
  mounted() {
    this.$noty.show("Hello!");
  }
};
</script>
```

显示通知。

我们也可以改变配置。

然后，我们可以通过编写以下内容来设置配置:

```
import Vue from "vue";
import App from "./App.vue";
import VueNoty from "vuejs-noty";
import "vuejs-noty/dist/vuejs-noty.css";Vue.use(VueNoty, {
  timeout: 4000,
  progressBar: true,
  layout: "topCenter"
});
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`timeout`是要显示的持续时间。

`progressBar`在通知上显示进度条。

`layout`是通知的落点。

# 物质波纹效应

材质波纹效果是一个让我们在元素上添加波纹效果的指令。

为了使用它，我们运行:

```
npm i vue-ripple-directive
```

然后我们可以通过写来使用它:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import Ripple from "vue-ripple-directive";Vue.directive("ripple", Ripple);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div>
    <div v-ripple>button</div>
  </div>
</template>

<script>
export default {};
</script>
```

我们只是导入指令并在元素上使用它。

我们可以添加修饰符来改变它们的行为。

我们还可以改变波纹的颜色。

我们可以写:

```
<template>
  <div>
    <div v-ripple.mouseover.1500>button</div>
  </div>
</template>

<script>
export default {};
</script>
```

我们添加了`mouseover`修改器来观察鼠标悬停时的涟漪效果。

1500 是效果的持续时间。

我们也可以改变颜色:

```
<template>
  <div>
    <div v-ripple="'green'">button</div>
  </div>
</template>

<script>
export default {};
</script>
```

# vue-a 层

Vue-APlayer 是一款简单易用的 Vue 应用音频播放器。

要安装它，我们运行:

```
npm i vue-aplayer
```

然后，我们可以通过运行以下命令来使用它:

```
<template>
  <div>
    <aplayer
      autoplay
      :music="{
        title: 'sample',
        artist: 'musician',
        src: 'https://file-examples.com/wp-content/uploads/2017/11/file_example_MP3_700KB.mp3',
        pic: 'http://placekitten.com/200/200'
      }"
    />
  </div>
</template>

<script>
import Aplayer from "vue-aplayer";export default {
  components: {
    Aplayer
  }
};
</script>
```

`title`是剪辑标题。

`artist`是艺人的名字。

它们都会显示在屏幕上。

`src`是剪辑的网址。

`pic`是图片的网址。

然后我们可以看到一个音频播放器，播放剪辑。

图片显示在播放器上。

# vue-密码

我们可以用 vue-password 添加一个密码输入。

要安装它，我们运行:

```
npm i vue-password
```

然后我们可以通过写来使用它:

```
<template>
  <div>
    <form>
      <label for="password">Password</label>
      <VuePassword v-model="password" id="password1" :strength="strength" type="text"/>
    </form>
  </div>
</template>

<script>
import VuePassword from "vue-password";export default {
  components: {
    VuePassword
  },
  computed: {
    strength() {
      if (this.password.length > 4) {
        return 4;
      }
      return Math.floor(this.password.length / 4);
    }
  },
  data() {
    return {
      password: ""
    };
  }
};
</script>
```

我们使用`VuePassword`组件来添加输入。

`v-model`将输入值绑定到`password`状态。

`strength`设置力量计。

有插槽自定义密码输入，图标，力量米，等等。

`strength`必须返回一个介于 0 和 4 之间的整数。

# 渐进图像

我们可以使用 vue-progressive-image 来添加一个渐进加载的图像元素。

为了使用它，我们运行:

```
npm i vue-progressive-image
```

然后我们可以写:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import VueProgressiveImage from "vue-progressive-image";Vue.use(VueProgressiveImage);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div>
    <progressive-img src="http://placekitten.com/200/200"/>
  </div>
</template>

<script>
export default {};
</script>
```

然后我们使用`progressive-img`组件来显示图像。

同样，我们可以使用`progressive-background`来添加背景:

```
<template>
  <div>
    <progressive-background src="http://placekitten.com/200/200"/>
  </div>
</template>

<script>
export default {};
</script>
```

![](img/92919fbfe7b77db670dfb642de204aa6.png)

照片由 [vaun0815](https://unsplash.com/@vaun0815?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

vuejs-noty 是一个有用的通知库。

材料涟漪效应给我们元素上的涟漪效应。

vue-password 允许我们使用力量计添加密码输入。

vue-progressive-image 允许添加一个图像元素来渐进地加载图像。