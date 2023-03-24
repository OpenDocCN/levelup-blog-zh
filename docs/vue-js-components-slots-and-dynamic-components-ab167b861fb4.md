# Vue.js 组件-插槽和动态组件

> 原文：<https://levelup.gitconnected.com/vue-js-components-slots-and-dynamic-components-ab167b861fb4>

![](img/f6fbe14b46d3a5f7585afa0474e7fbc3.png)

马特·希顿在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在本文中，我们将研究用插槽和动态组件来分发组件。

# 用插槽分发组件

我们可以通过在开始和结束标签之间放置文本来传递组件中的内容。

为此，我们可以用`slot`元素向组件添加一个插槽。

例如，我们可以如下使用它:

`src/index.js`:

```
Vue.component("error-box", {
  template: `
    <div v-bind:style="{color: 'red'}">
      <slot></slot>
    </div>
  `
});new Vue({
  el: "#app",
  data: {
    text: ""
  }
});
```

`index.html`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>App</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head> <body>
    <div id="app">
      <error-box>Error</error-box>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

我们放在`error-box`标签之间的文本`Error`替换了`slot`标签，所以我们得到了红色的“错误”一词。

# 动态组件

我们可以通过使用`v-bind:is`在组件之间动态切换。

`v-bind:is`的值可以包含注册组件的名称或组件的选项对象。

例如，我们可以如下使用它:

`src/index.js`:

```
Vue.component("tab-foo", {
  template: "<div>Foo</div>"
});Vue.component("tab-bar", {
  template: "<div>Bar</div>"
});Vue.component("tab-baz", {
  template: "<div>Baz</div>"
});new Vue({
  el: "#app",
  data: {
    currentTab: "foo"
  },
  computed: {
    currentTabComponent() {
      return `tab-${this.currentTab.toLowerCase()}`;
    }
  }
});
```

在上面的代码中，我们创建了 3 个组件，在根 Vue 实例中，我们有`currentTab`字段，这样我们可以添加按钮来更改 HTML 模板中的内容。

`currentTabComponent`将随着`this.currentTab`的变化而更新，我们将在模板中用按钮设置它的值。

`index.html`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>App</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head> <body>
    <div id="app">
      <button [@click](http://twitter.com/click)='currentTab = "foo"'>Foo</button>
      <button [@click](http://twitter.com/click)='currentTab = "bar"'>Bar</button>
      <button [@click](http://twitter.com/click)='currentTab = "baz"'>Baz</button>
      <component v-bind:is="currentTabComponent"></component>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

在上面的模板中，我们有 3 个带有 click 事件处理程序的按钮来设置`currentTab`值，这也将在`currentTab`改变时设置`currentTabComponent`属性。

因为我们将`currentTabComponent`传递给了`v-bind:is`，所以`component`元素将用`currentTabComponent`中设置的名称替换组件。

因此，当我们单击 Foo 按钮时，我们得到 Foo，当我们单击 Bar 按钮时，我们得到 Bar，当我们单击 Baz 按钮时，我们得到 Baz。

我们可以向`v-bind:is`传递一个对象，如下所示:

`src/index.js`:

```
new Vue({
  el: "#app",
  data: {
    tabBar: {
      template: "<div>Bar</div>"
    }
  }
});
```

`index.html`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>App</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head> <body>
    <div id="app">
      <component v-bind:is="tabBar"></component>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

我们将`tabBar`对象作为`v-bind:is`指令的值传入。

![](img/b3f496f30de2d519e90a993c31c341f1.png)

凯文在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# DOM 模板解析问题

一些 HTML 元素对元素内部的内容有限制。例如，`ul`内部必须有`li`元素。

我们可以用`is`属性嵌套任何我们想要的元素。

例如，我们可以如下使用它:

`src/index.js`:

```
Vue.component("list-item", {
  template: "<div>Foo</div>"
});new Vue({
  el: "#app"
});
```

`index.html`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>App</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head> <body>
    <div id="app">
      <ul>
        <li is="list-item"></li>
      </ul>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

上面的代码将`list-item`组件设置为`li`元素内部的组件。

如果我们使用来自以下来源之一的字符串模板，则此限制不适用:

*   字符串模板(例如`template: '...'`)
*   单列(`.vue`)组件
*   `<script type="text/x-template">`

# 结论

我们可以使用`slot`元素获取组件的开始和结束标记之间的项目内容。

`component`元素让我们通过为组件传递一个字符串名称来动态地改变组件。

我们可以将带有组件名称的对象或字符串传递给`v-bind:is`。