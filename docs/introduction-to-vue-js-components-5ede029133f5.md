# Vue.js 组件简介

> 原文：<https://levelup.gitconnected.com/introduction-to-vue-js-components-5ede029133f5>

![](img/277e91e3476bb4e1f55ecfb2cb90bb6a.png)

保罗·吉尔摩在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以利用它来开发交互式前端应用。

在本文中，我们将看看如何创建基本的 Vue.js 组件。

# 组件基础

我们可以使用`Vue.component`方法创建一个 Vue 组件。它接受一个字符串作为组件名，并接受一个 options 对象作为第二个参数，该对象类似于我们用于`Vue`构造函数的对象。

如果名字有多个单词，那么它应该是 kebab-case。

例如，一个简单的例子如下:

`src/index.js`:

```
Vue.component("count-down", {
  data() {
    return {
      count: 1000
    };
  },
  template: '<button v-on:click="count--">{{count}}</button> '
});new Vue({
  el: "#app",
  data: {}
});
```

`index.js`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>App</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head> <body>
    <div id="app">
      <count-down></count-down>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

然后我们得到一个按钮，上面有一个数字，当我们点击它时，它就会下降。

大多数属性与我们传递给`new Vue`的对象相同。`data`、`computed`、`watch`、`methods`，生命周期挂钩可用。一些特定于根的选项如`el`不可用。

# 重用组件

使用组件的好处是我们可以在多个地方重用它。

例如，我们可以写:

```
<div id="app">
  <count-down></count-down>
  <count-down></count-down>
</div>
```

在`index.html`中，我们得到的不是一个按钮，而是一个按钮。

# 数据必须是一个函数

我们传递给`new Vue`的对象和组件的一个区别是`data`字段是一个函数而不是一个对象。

`data`应该返回一个对象。这是为了将数据隔离到组件的一个实例中。

# 组织组件

我们可以在全球或本地注册组件。全局注册的实例可以在任何 Vue 实例中使用。

# 用 Props 将数据传递给子组件

为了从子组件传递父组件的数据，我们可以传入 props。

为此，我们向组件添加了一个`props`属性。

例如，我们可以创建一个采用道具的简单组件，并按如下方式使用该组件:

`src/index.js`:

```
Vue.component("post", {
  props: ["content"],
  template: "<div>{{content}}</div>"
});new Vue({
  el: "#app",
  data: {}
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
      <post content="foo"></post>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

我们可以看到显示的单词`foo`。

一旦一个属性如上所述被注册，我们就可以将数据作为一个自定义属性传递给它。

一个组件可以有任意多的道具，默认情况下任何值都可以传递给它。

为了传递动态数据，我们可以使用带有属性名的`v-bind`作为参数。

例如，我们可以这样做:

`src/index.js`:

```
Vue.component("post", {
  props: ["content"],
  template: "<div>{{content}}</div>"
});new Vue({
  el: "#app",
  data: {
    posts: [{ content: "foo" }, { content: "bar" }]
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
      <post
        content="foo"
        v-for="post in posts"
        v-bind:content="post.content"
      ></post>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

最重要的部分是`v-bind:content=”post.content”`，它让我们传入一个动态值作为道具，而不是静态值。

它可以是任何表达式。

![](img/f841af3caca7b58232163a01530ebdfd.png)

照片由 [Beata Ratuszniak](https://unsplash.com/@beataratuszniak?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 每个组件必须有一个根元素

每个组件必须有一个根元素。

我们必须将组件包装在一个 HTML 根元素中，如下所示:

`src/index.js`:

```
Vue.component("post", {
  props: ["content", "title"],
  template: `
    <div>
      <h2>{{title}}</h2>
      <div>{{content}}</div>
    </div>
  `
});new Vue({
  el: "#app",
  data: {
    posts: [{ title: "Foo", content: "foo" }, { title: "Bar", content: "bar" }]
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
      <post
        content="foo"
        v-for="post in posts"
        v-bind:content="post.content"
        v-bind:title="post.title"
      ></post>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

我们也可以传入一个对象作为道具。这意味着我们不必传入太多的道具。

例如，我们可以这样做:

`src/index.js`:

```
Vue.component("post", {
  props: ["post"],
  template: `
    <div>
      <h2>{{post.title}}</h2>
      <div>{{post.content}}</div>
    </div>
  `
});new Vue({
  el: "#app",
  data: {
    posts: [{ title: "Foo", content: "foo" }, { title: "Bar", content: "bar" }]
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
      <post v-for="post in posts" v-bind:post="post"></post>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

在上面的代码中，我们有一个单独的`post`道具，而不是之前的`title`和`content`道具。随着我们的对象变得越来越大，这就越来越方便了。

此外，每当我们向一个`post`对象添加属性时，我们不需要添加另一个属性。

# 结论

我们可以定义新的组件并用`Vue.component`方法注册它。

它接受一个组件名称字符串和一个类似于我们传递到`new Vue`的 options 对象的对象。

如果一个组件有多个单词，它应该是烤肉串大小写。

要将数据传递到组件中，我们可以将道具传递到其中。