# 用于添加工具提示、下拉菜单和管理 Cookies 的顶级 Vue 包。

> 原文：<https://levelup.gitconnected.com/top-vue-packages-for-adding-tooltips-dropdowns-and-manage-cookies-c7cc627e6103>

![](img/3b1a9af632163d1cc48785a831b0177d.png)

照片由 [niko photos](https://unsplash.com/@niko_photos?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在这篇文章中，我们将看看最好的软件包是如何添加工具提示、管理 cookies 和添加下拉菜单的。

# VueTippy

VueTippy 让我们可以轻松地在 Vue 应用程序中添加工具提示。

要使用它，我们通过编写以下内容来安装它:

```
npm i vue-tippy
```

然后我们可以通过写来使用它:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import VueTippy, { TippyComponent } from "vue-tippy";Vue.use(VueTippy);
Vue.component("tippy", TippyComponent);Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

我们注册了`VueTippy`插件和`TippyComponent`。

然后，我们可以通过编写以下代码来使用`v-tippy`指令:

```
<template>
  <div>
    <button content="hello" v-tippy>click me</button>
  </div>
</template>

<script>
export default {};
</script>
```

我们只是把`content`设置成我们想要的。

现在当我们悬停在它上面时会得到一个工具提示。

为了定制工具提示显示，我们可以使用`tippy`组件并填充提供的槽来添加我们想要的内容。

例如，我们可以写:

```
<template>
  <div>
    <tippy arrow>
      <template v-slot:trigger>
        <button>click me</button>
      </template> <div>
        <h3>Header</h3>
        <p style="color: black">{{ message }} - data binding</p>
      </div>
    </tippy>
  </div>
</template>

<script>
export default {};
</script>
```

我们添加了`tippy`组件，并在`trigger`槽中添加了触发工具提示的元素。

默认槽有工具提示的内容。

# vue-cookie

vue-cookie 让我们可以按照自己的方式管理 cookie。

为了安装它，我们编写:

```
npm i vue-cookie
```

现在我们可以通过书写来使用它:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
const VueCookie = require("vue-cookie");Vue.use(VueCookie);Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

注册`VueCookie`插件。

然后，我们可以通过编写以下内容来保存 cookie:

```
<template>
  <div></div>
</template>

<script>
export default {
  mounted() {
    this.$cookie.set("test", "hello", 1);
  }
};
</script>
```

我们用这个很容易保存饼干。

第一个参数是 cookie 名称，第二个参数是值，第三个参数是以天为单位的到期时间。

这比没有库保存 cookies 容易多了。

要获得 cookie，我们可以写:

```
this.$cookie.get('test');
```

我们可以通过写入以下内容来删除 cookie:

```
this.$cookie.delete('test');
```

我们还可以通过编写以下内容来更改 cookie 的域和过期时间:

```
<template>
  <div></div>
</template>

<script>
export default {
  mounted() {
    this.$cookie.set("test", "hello", { expires: 1, domain: "localhost" });
  }
};
</script>
```

我们可以设置 cookie 到期时间和域。

还有其他方法可以做到这一点。

# vue-多选

vue-multiselect 是 vue 应用程序的多功能下拉组件。

要安装它，我们运行:

```
npm i vue-multiselect
```

然后我们可以通过写来使用它:

```
<template>
  <div>
    <multiselect
      v-model="selected"
      :options="options">
    </multiselect>
  </div>
</template>

<script>
  import Multiselect from 'vue-multiselect'
  export default {
    components: { Multiselect },
    data () {
      return {
        selected: null,
        options: ['foo', 'bar', 'baz']
      }
    }
  }
</script>

<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>
```

我们使用`multiselect`组件来创建一个下拉列表。

`v-model`将选择的值绑定到一个状态。

`options`让我们设置下拉菜单的选项。

`style`标签有样式。

它支持许多其他选项，如 Vuex 集成、多选、标签输入等。

# vue-cookie

vue-cookies 是另一个 vue 库，可以让我们在 Vue 应用中管理 cookies。

要安装它，我们运行:

```
npm i vue-cookies
```

现在我们可以通过书写来使用它:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import VueCookies from "vue-cookies";
Vue.use(VueCookies);Vue.$cookies.config("30d");Vue.config.productionTip = false;new Vue({
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
  mounted() {
    this.$cookies.set("foo", "bar");
  }
};
</script>
```

在`main.js`中，我们注册了插件并设置了全局配置。

我们将 vue-cookies 创建的所有 cookie 设置为 30 天后过期。

在我们的组件中，我们用`this.$cookies.set`设置了一个 cookie，

第一个参数是键，第二个是相应的值。

第三个参数是以秒为单位的到期时间。

例如，我们可以写:

```
this.$cookies.set("foo", "bar", 1);
```

我们可以使用`remove`方法删除一个 cookie:

```
this.$cookies.remove("token");
```

![](img/e2daaea7925ca782e75c558e8a22c589.png)

图为[托德·夸肯布什](https://unsplash.com/@toddquackenbush?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 结论

vue-cookie 和 vue-cookies 都是有用的插件，可以让我们将 cookie 管理功能添加到我们的 vue 应用程序中。

VueTippy 是一个方便的工具提示库。

vue-multiselect 是一个多功能的下拉库。