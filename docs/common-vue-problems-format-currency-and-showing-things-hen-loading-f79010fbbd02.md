# 常见的 Vue 问题—货币格式和加载时显示内容

> 原文：<https://levelup.gitconnected.com/common-vue-problems-format-currency-and-showing-things-hen-loading-f79010fbbd02>

![](img/8f6c738a5b0bcc65767b92114f6b0203.png)

[董成](https://unsplash.com/@dongcheng?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Vue.js 让开发前端应用变得简单。然而，我们仍有可能遇到问题。

在这篇文章中，我们将看看一些常见的问题，并看看如何解决它们。

# 在 Vue 组件中格式化货币

有几种方法可以在我们的组件中转换货币。

一种方法是添加一个方法。例如，我们可以写:

```
methods: {
  formatPrice(value) {
    if (typeof value !== "number") {
        return value;
    }
    const formatter = new Intl.NumberFormat('en-US', {
      style: 'currency',
      currency: 'USD',
      minimumFractionDigits: 0
    });
    return formatter.format(value);
  }
}
```

我们使用`Intl.NumberFormat`构造函数来创建一个货币格式化程序，我们可以用它来格式化货币。

之后，我们可以用`format`方法将`value`数字格式化成一个字符串。

此外，我们可以创建一个过滤器，如下所示:

```
Vue.filter('currency', (value) => {
  if (typeof value !== "number") {
    return value;
  }
  const formatter = new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency: 'USD',
    minimumFractionDigits: 0
  });
  return formatter.format(value);
});
```

我们使用相同的`Intl.NumberFormat`构造函数。

然后我们可以用它来格式化货币，就像我们用方法一样，并返回格式化后的字符串。

然后在我们的模板中，我们可以写:

```
{{ fees | currency }}
```

将货币值显示为格式化字符串。

# 加载延迟加载的路径组件时显示加载动画

我们可以使用类似于`nprogress`库的东西，当路线开始加载时开始和结束动画，当它停止时停止动画。

例如，我们可以写:

```
const router = new VueRouter({
  routes
})router.beforeEach((to, from, next) => {
  NProgress.start()
  next()
})
router.afterEach(() => {
  NProgress.done()
})
```

我们显示由`NProgress`提供的动画。

或者，我们可以在根 Vue 实例上设置`loading`状态，方法是:

```
Vue.component('loading',{ template: '<div>Loading!</div>'})const router = new VueRouter({
  routes
})const app = new Vue({
  data: { loading: false },
  router
}).$mount('#app')router.beforeEach((to, from, next) => {
  app.loading = true;
  next();
})router.afterEach((to, from, next) => {
  app.loading = false;
  next();
})
```

然后在我们的根组件的模板中，我们可以写:

```
<loading v-if="$root.loading"></loading>
<router-view v-else></router-view>
```

我们使用`$root`获得根 Vue 实例。

所以`$root.loading`有我们之前设置的`loading`状态的当前值。

这样，在我们的模板中，我们可以写出我们所拥有的。

当`$root.loading`为`false`时，我们加载`router-view`。

# 在 5 个项目后添加新行

我们可以创建一个嵌套数组，每个数组包含 5 个元素。

为了使这变得容易，我们可以使用 Lodash 的`chunk`方法。

例如，我们可以写:

```
computed: {
  itemChunks(){
    return _.chunk(this.items, 5);
  }
},
```

`chunk`方法将`this.items`数组中的项目分成块。

然后我们可以使用`v-for`遍历嵌套数组的所有元素:

```
<div class="row" v-for="chunk of itemChunks">
  <span v-for="item in chunk">
     {{item}}
  </span>
</div>
```

我们用外部的`v-for`循环遍历数组的顶层。

然后在内部`v-for`中，我们遍历嵌套数组中的项目。

# 在渲染之前隐藏 Vue 模板

如果我们只想在呈现之前隐藏标记，那么我们可以使用`v-cloak`指令。

然后我们可以在`v-cloak`指令中添加`display: none`来隐藏它，因为`v-cloak`属性会在元素加载前添加到元素中。

例如，我们可以写:

```
<div v-cloak>
  {{message}}
</div>
```

然后在我们的 CSS 中，我们可以写:

```
[v-cloak] {
  display: none;
}
```

隐藏未呈现的模板。

如果我们想添加一个装载信息，我们可以写:

```
const vm = new Vue({
  el: "#app",
  created(){
    this.$http.get('url')
      .then(response => {     
        this.loading = false;
        this.message = response.message;
      });
  },
  data: {
    message: '',
    loading: true
  }
});
```

然后在我们的模板中，我们可以写:

```
<div v-if="loading">
   Loading...
</div>
<div v-else>
  {{message}}
</div>
```

我们最初将`loading`设置为`true`,以显示“正在加载…”信息。

然后当`loading`为`false`时，我们看到消息从服务器加载。

我们还可以添加一个类，在加载东西的时候显示不同的样式。

例如，我们可以写:

```
<div :class="{'loading': loading}"></div>
```

![](img/59b876e0111ff1ce178b31ff47b1e328.png)

照片由 [Shane Guymon](https://unsplash.com/@shaneguymon?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们可以用一种方法或过滤器来格式化货币。

当我们加载一个东西时，有不同的方法来显示不同的东西。

此外，我们可以使用 Lodash 的`chunk`将数组分块，以将一个数组划分为具有相同项数的嵌套数组。