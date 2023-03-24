# 常见的 Vue 问题—去抖动计算属性、后退导航等

> 原文：<https://levelup.gitconnected.com/common-vue-problems-debouncing-computed-properties-back-navigation-and-more-5753fccb46d0>

![](img/6cb6035aaa7f68e94e45069c33f4374d.png)

安库什·明达在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Vue.js 让开发前端应用变得简单。然而，我们仍有可能遇到问题。

在这篇文章中，我们将看看一些常见的问题，并看看如何解决它们。

# 去抖动计算的属性

我们可以使用 Lodash `debounce`方法来谴责 setter。

例如，我们可以写:

```
const vm = new Vue({
  el: '#app',
  data: {
    text: ''
  },
  computed: {
    textComputed: {
      get() {
        return this.text;
      },
      set: _.debounce((newValue) => {
        this.text = newValue;
      }, 500)
    }
  }
})
```

我们用`debounce`方法包装我们的 setter 方法。

第二个参数是以毫秒为单位的延迟。

所以在 500 毫秒后`this.text`将被设置为`newValue`值。

# 搜索框和复选框过滤器

我们可以用计算出的属性来过滤数组。

例如，我们可以写:

```
filtered() {
  const result =  this.estates.filter((estate) =>
    estate.name == that.search;
  );
  if(this.checkedRegions.length || this.checkedRooms.length) {
    return result.filter(estate => this.checkedRegions.includes(estate.region) || this.checkedRooms.includes(estate.rooms))
  }
  return result;
  }
}
```

我们有`filtered`方法，它以两种方式过滤我们的代码。

`this.checkedRegions`有检查过的值，所以我们可以使用`filter`和其中的`includes`方法来检查`estate.region`是否有在`this.checkedRegions`数组中的值。

我们可以用`this.checkedRooms`做同样的事情，它也是一个校验值数组。

另外，如果我们没有任何检查过的值，我们返回从第一个过滤器得到的值。

# Vue 和 jQuery

Vue 提供了许多使 jQuery 变得多余的特性。

因此，在大多数情况下，我们不应该将 jQeurt 放在我们的代码中。

# 在 BootstrapVue 中添加到卡组的链接

要向卡组添加链接，我们可以在`b-card-body`中添加一个`b-link`。

例如，我们可以写:

```
<b-card>
  <b-card-body>
    <b-link to="/">
      Title
    </b-link>
    <p class="card-text">
       text
    </p>
  </b-card-body>
</b-card>
```

我们只是在添加卡片文本之前添加它。

# 绑定到 img 元素的 src 属性

为了绑定到图像 URL，我们可以使用`v-bind`或`:`语法，

例如，如果我们有:

```
<img :src="require('./assets/pic.png')" :alt="pic">
```

我们也可以写:

```
import img from './assets/pic.png';export default {
  ...
  data(){
    return {
      img
    }
  } 
  ...
}
```

Webpack 将图像视为模块。

这就是为什么我们可以使用`require`或`import`将它们包含在我们的应用程序中。

# 要求和默认导出

如果我们使用`require`将组件包含在我们的应用程序中，我们需要访问带有`default`属性的组件。

例如，我们必须写:

```
Vue.component('Header', require("./components/Header.vue").default);
```

这是因为 Vue 组件代码被导出为默认导出。

在 Vue 组件中，我们有:

```
export default {
  ...
}
```

在`script`标签之间。

# 检测 Vue 路由器中的后退按钮导航

当我们有 Vue 路由器时，我们可以检查后退按钮导航。

为此，我们可以写:

```
window.popStateDetected = false
window.addEventListener('popstate', () => {
  window.popStateDetected = true
})router.beforeEach((to, from, next) => {
  const isBackButton = window.popStateDetected;
  window.popStateDetected = false;
  if (isBackButton && from.meta.something) {
    next(false) 
    return ''
  }
  next()
})
```

我们可以听听`popstate`事件。

然后，当检测到状态时，我们将一个全局`window.popStateDetected`变量设置为`true`。

然后，当我们在应用程序中导航时运行导航卫士，我们可以检查`window.popStateDetected`变量，它应该被设置为`true`。

在它里面，我们可以检查如果`window.popStateDetected`是`true`是否按下后退按钮来导航。

然后我们可以在里面做我们想做的事。

`from.meta.something`是我们在路线的`meta`属性中定义的自定义属性。

`next(false)`如果`if`中的条件为`true`，则停止继续导航。

上面的代码应该在`main.js`里。

# 在页面加载时调用 Vue 函数

要在页面加载中调用 Vue 函数，我们可以在`beforeMount`钩子中调用它。

例如，我们可以写:

```
...
methods:{
  getData() {...}
},
beforeMount(){
  this.getData();
},
...
```

我们定义了`getData`方法。

然后我们在`beforeMount`钩子中调用它，这个钩子在`created`钩子被调用之后但是在`mounted`钩子之前被调用。

当组件被完全安装时调用它。

![](img/f75bfa9c26cd5c43de4eb2365f2f1010.png)

[奥克塔维奥·福萨蒂](https://unsplash.com/@enriq_b312?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

我们可以用 Lodash `debounce`方法公开计算的属性。

此外，如果在`methods`属性中定义了组件代码，我们可以在组件代码中调用方法。

我们可以检查 checked 和 search 值，并在 computed property 方法中相应地返回一些内容。