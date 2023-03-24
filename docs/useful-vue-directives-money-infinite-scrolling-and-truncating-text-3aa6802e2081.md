# 有用的 Vue 指令——金钱、无限滚动和截断文本

> 原文：<https://levelup.gitconnected.com/useful-vue-directives-money-infinite-scrolling-and-truncating-text-3aa6802e2081>

![](img/f6333ad5e4693b3376d470ac2701f8a9.png)

由 [Alexander Mils](https://unsplash.com/@alexandermils?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在本文中，我们将了解如何在 Vue 应用程序中添加金钱输入和无限滚动效果。

# 虚拟货币

我们可以用`v-money`包添加一个带有美元符号或其他货币符号的输入控件。

要安装它，我们可以运行:

```
npm install --save v-money
```

然后我们可以如下使用它:

`main.js`:

```
import Vue from "vue";
import App from "./App.vue";
import money from "v-money";Vue.use(money, { precision: 4 });
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`:

```
<template>
  <div>
    <money v-model="price" v-bind="money"></money>
    {{price}}
  </div>
</template><script>
import { Money } from "v-money";export default {
  components: { Money },
  data() {
    return {
      price: 123.45,
      money: {
        decimal: ",",
        thousands: ".",
        prefix: "C$ ",
        suffix: " #",
        precision: 2,
        masked: false
      }
    };
  }
};
</script>
```

在上面的代码中，我们使用了`precision`属性来指定我们可以输入的最大小数位数。

此外，我们还添加了属性，其含义如下:

*   `decimal` —带小数点分隔符的字符串
*   `thousands` —千位分隔符的字符串
*   `prefix`—货币符号的字符串
*   `suffix` —出现在输入末尾的字符串
*   `masked` —一个布尔值，指示组件是否应包含掩码

仅需要`precision`。

# 无限卷轴

应用程序经常有无限滚动的效果，每次加载一点数据。

为了方便添加无限滚动效果，我们可以使用`vue-infinite-scroll`包。

我们可以通过运行以下命令来安装它:

```
npm install --save vue-infinite-scroll
```

那么我们可以如下使用它:

`main.js`:

```
import Vue from "vue";
import App from "./App.vue";
const infiniteScroll = require("vue-infinite-scroll");Vue.use(infiniteScroll);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`:

```
<template>
  <div>
    <div
      v-infinite-scroll="onLoadMore"
      infinite-scroll-disabled="busy"
      infinite-scroll-distance="10"
    >
      <p v-for="n in nums">{{n}}</p>
    </div>
  </div>
</template><script>
export default {
  data() {
    return {
      busy: false,
      nums: new Array(50).fill(0).map((_, i) => i),
      max: 50
    };
  },
  methods: {
    onLoadMore() {
      this.nums = [
        ...this.nums,
        ...new Array(50).fill(0).map((_, i) => i + this.max)
      ];
      this.max += 50;
    }
  }
};
</script>
```

在上面的代码中，我们使用了向页面添加更多数字的`onLoadMore`方法。然后我们引用它作为`v-infinite-scroll`指令的值。

我们还定义了`infinite-scroll-disabled`和`infinite-scroll-distance`道具。

如果该属性的值为`true`，则`infinite-scroll-disabled`属性禁用无限滚动。

`infinite-scroll-distance`是一个数字，它指定了在我们传递给`v-infinite-scroll`指令的方法运行之前，元素底部和视窗底部之间的最小距离。

还有 3 个其他选项，分别是:

*   `infinite-scroll-immediate-check` —一个布尔值，表示指令应在绑定后立即检查。如果内容不够高，无法填满可滚动容器，这将非常有用。
*   `infinite-scroll-listen-for-event` —当事件发出时，无限滚动将再次检查
*   `infinite-scroll-throttle-delay` —下一次检查和当前时间之间的间隔，以毫秒为单位。

![](img/7f5790153cc19e3f3b3779dbac0cce42.png)

照片由[费利克斯·普拉多](https://unsplash.com/@fprado?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# Vue-Clampy

`vue-clampy`让我们截断元素内部的内容，并在末尾添加省略号。

我们可以通过运行以下命令来安装它:

```
npm install --save @clampy-js/vue-clampy
```

然后我们可以通过写来使用它:

`main.js`:

```
import Vue from "vue";
import App from "./App.vue";
import clampy from "[@clampy](http://twitter.com/clampy)-js/vue-clampy";Vue.use(clampy);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`:

```
<template>
  <div>
    <div v-clampy="3">{{text}}</div>
  </div>
</template><script>
import clampy from "@clampy-js/vue-clampy";
export default {
  data() {
    return {
      text: "foo ".repeat(200).trim()
    };
  },
  directives: {
    clampy
  }
};
</script>
```

在上面的代码中，我们添加了`v-clamp`指令，并将值设置为 3，以截断 3 行后的文本。

我们也可以按如下方式更改截断字符:

```
import Vue from "vue";
import App from "./App.vue";
import clampy from "@clampy-js/vue-clampy";Vue.use(clampy, {
  truncationChar: "✂️"
});
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

# 结论

通过让我们修改各种选项来创建我们的货币输入，这个`v-money`包让我们可以轻松地添加货币输入。

我们可以使用`vue-infinite-scroll`指令添加无限滚动效果，以便在滚动到底部时加载更多数据

`vue-clampy`包让我们将文本截短到给定的行数。