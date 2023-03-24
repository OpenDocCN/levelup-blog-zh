# Vue.js 组件中的事件处理

> 原文：<https://levelup.gitconnected.com/event-handling-in-vue-js-components-fa314e469d30>

![](img/831aa29332056e06c314377a5ec0561a.png)

[约书亚·希伯特](https://unsplash.com/@joshnh?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在本文中，我们将看看如何处理 Vue 组件中的事件，包括如何使用定制组件的`v-model`以及在父组件和子组件之间传递数据。

# 监听子组件事件

我们可以从子组件向父组件发送事件，以便在父组件中运行代码。

例如，如果我们想给每篇文章添加一个按钮来改变页面的字体，我们可以这样做:

`src/index.js`:

```
Vue.component("post", {
  props: ["post"],
  template: `
    <div>
      <h2>{{post.title}}</h2>
      <div>{{post.content}}</div>
      <button v-on:click="$emit('change-font')">
        Change Font
      </button>
    </div>
  `
});new Vue({
  el: "#app",
  data: {
    posts: [{ title: "Foo", content: "foo" }, { title: "Bar", content: "bar" }],
    font: ""
  },
  methods: {
    changeFont() {
      this.font = this.font === "" ? "courier" : "";
    }
  }
});
```

我们添加了`changeFont`方法，让我们在 Vue 应用程序中改变字体。当`post`组件发出`change-font`事件时，将会调用这个函数。

当点击改变字体按钮时，从`post`发出`change-font`事件。

该事件是用`$emit`方法发出的。我们传入的字符串是事件的名称。

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
      <div v-bind:style="{'font-family': font}">
        <post
          v-for="post in posts"
          v-bind:post="post"
          v-on:change-font="changeFont"
        ></post>
      </div>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

在模板中，我们用`v-bind:style`属性来动态设置`div`的字体。

当收到`change-font`事件时，将调用`changeFont`方法在 Courier 和 default 之间切换字体。

`$emit`方法还接受第二个参数，该参数带有我们希望发送回父对象的值。

例如，我们可以将字体更改代码重写如下:

`src/index.js`:

```
Vue.component("post", {
  props: ["post"],
  template: `
    <div>
      <h2>{{post.title}}</h2>
      <div>{{post.content}}</div>
      <button v-on:click="$emit('change-font', 'courier')">
        Change Font
      </button>
    </div>
  `
});new Vue({
  el: "#app",
  data: {
    posts: [{ title: "Foo", content: "foo" }, { title: "Bar", content: "bar" }],
    font: ""
  },
  methods: {
    changeFont(font) {
      this.font = this.font === font ? "" : font;
    }
  }
});
```

在上面的代码中，我们将`'courier'`传递给了`$emit`函数。第二个参数中的项目将作为父组件中的`$event`可用。

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
      <div v-bind:style="{'font-family': font}">
        <post
          v-for="post in posts"
          v-bind:post="post"
          v-on:change-font="changeFont($event)"
        ></post>
      </div>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

在模板中，我们将包含`'courier'`的`$template`传递给`changeFont`方法。

`changeFont`方法在我们作为参数传入的`font`和空字符串之间切换字体。

所以它做的和以前一样。

这让我们可以将数据从子组件传递到父组件。

![](img/6e07b4f96fa5c01727937a27ef6c3dda.png)

照片由[金莎·艾利斯](https://unsplash.com/@kymellis?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 在组件上使用 v 模型

`v-model`同`v-bind:value`和`v-on:input`加在一起。这意味着:

```
<input v-model="text">
```

与以下内容相同:

```
<input v-bind:value="text" v-on:input="text= $event.target.value" >
```

由于组件接受道具并发出事件，我们可以将`value` propr 和`input`事件绑定到`v-model`指令中。

例如，我们可以使用它进行如下自定义输入:

`src/index.js`:

```
Vue.component("styled-input", {
  props: ["value"],
  template: `
    <input
      v-bind:style="{'font-family':'courier'}"
      v-bind:value="value"
      v-on:input="$emit('input', $event.target.value)"
    >
  `
});new Vue({
  el: "#app",
  data: {
    text: ""
  }
});
```

在上面的代码中，我们创建了`style-input`组件来将输入的字体更改为 Courier。

同样，我们使用`v-bind:value`来获取`value`属性的值，并在文本输入到输入框时发出`input`事件。

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
      <styled-input v-model="text"></styled-input>
      <br />
      {{text}}
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

在上面的代码中，我们可以使用`v-model`绑定到`text`数据字段，因为`v-model`与组合`v-bind:value`和`v-on:input`是一样的。

`styled-input`发出了`input`事件并获得了`value`道具，所以我们可以将它们合并成`v-model`。

# 结论

我们可以从子组件向父组件发出带有`$emit`的事件。它需要两个参数。第一个参数是事件名称的字符串，第二个参数是我们要传递给父对象的对象。

父进程可以通过使用`v-on`指令监听事件来访问从子进程传递的对象，然后使用`$event`对象从子进程中检索项目。

`v-bind:value`和`v-on:input`与`v-model`相同，所以`v-bind:value`和`v-on:input`可以合二为一，`v-model`可以使用定制组件。

这让我们可以轻松地创建自定义输入。