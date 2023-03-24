# 关于 Vue 组件我们必须知道的事情

> 原文：<https://levelup.gitconnected.com/things-that-we-have-to-know-about-vue-components-38cc1331cc59>

![](img/98f80096a793cce4516b2c13b8ec72fe.png)

照片由[格雷格·努内斯](https://unsplash.com/@greg_nunes?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在这篇文章中，我们将看看在创建 Vue 组件时应该注意的事情。

# Vue 不处理多个根节点

如果一个组件中有多个根节点，我们会得到一个错误。例如，下面将给出一个错误:

```
Vue.component("app-foo", {
  template: `
    <p>foo</p>
    <p>bar</p>
  `
});
```

相反，我们必须将它们包装在包装元素中，如下所示:

```
Vue.component("app-foo", {
  template: `
    <div>
      <p>foo</p>
      <p>bar</p>
    </div>
  `
});
```

如果我们想要呈现元素列表，我们可以使用呈现函数来呈现组件:

```
Vue.component("app-foo", {
  functional: true,
  render(createElement) {
    return [createElement("p", "foo"), createElement("p", "bar")];
  }
});
```

在上面的代码中，我们将`functional`设置为`true`，使其成为一个功能组件。然后我们渲染了 2 p 个元素来显示一些东西。

功能组件没有单一的根限制。这是因为它们不需要像有状态组件那样在虚拟 DOM 中进行区分。

如果您想在我们的组件中只返回静态 HTML，那么我们不需要返回一个包含组件的根节点。

# 构建使用 v-model 指令绑定数据的组件

当我们构建组件时，我们必须确保它们能够以我们希望的方式与其他组件交互。我们可以用几种方法做到这一点。

首先，我们应该创建接受`v-model`指令的表单字段，将模型绑定到表单值。`v-model`是`value`道具和`input`事件发射的简称。

因此，如果我们创建一个具有这两个特性的组件，那么我们可以使用`v-model`将组件内部的一些东西绑定到我们的输入。

例如，我们可以创建自己的自定义输入组件，如下所示:

`index.js`:

```
Vue.component("app-input", {
  props: ["value"],
  template: `
    <input 
      :value='value' 
      @input="$emit('input', $event.target.value)"
    >
  `
});new Vue({
  el: "#app",
  data: {
    value: ""
  }
});
```

`index.html`:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>App</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/vuex@2.0.0"></script>
  </head>
  <body>
    <div id="app">
      <app-input v-model="value"></app-input>
      <p>{{value}}</p>
    </div>
    <script src="index.js"></script>
  </body>
</html>
```

在上面的代码中，我们将`value`属性绑定到`value`属性的值。此外，我们有一个`input`事件处理程序，它用我们输入到输入中的值发出`input`事件。

这样，我们可以像在`index.html`中一样使用`v-model`来绑定`value`的值作为我们的模型。

当我们在输入中输入值时，我们看到它显示在 p 元素中。

![](img/662f3ba6bf8ec40c0281af2c3ab7cbc5.png)

安德里亚·米尼尼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 对事件透明

Vue 组件事件只从父组件传播到子组件。如果我们想要像从较低级别到较高级别组件那样冒泡事件，那么我们必须使用带有`v-on`的`$listeners`变量将带有`$listeners`的组件的子组件的所有事件发送到带有`$listeners`的组件的父组件，这是子组件的祖父组件。

例如，我们可以编写以下代码，将事件从孙组件传递到顶级祖父组件，如下所示:

`index.js`:

```
Vue.component("app-button", {
  template: `
    <button @click='$emit("app-button-clicked")'>
      Click Me
    </button>    
  `
});Vue.component("app-parent", {
  template: `
    <app-button v-on="$listeners"></app-button>
  `
});new Vue({
  el: "#app",
  methods: {
    showAlert() {
      alert("Clicked");
    }
  }
});
```

在上面的代码中，我们有`app-button`，它在单击 Click Me 按钮时发出`app-button-clicked`事件。

然后我们用带有`v-on='$listeners'`指令的`app-parent`将所有事件从`app-button`发送到根 Vue 实例。

最后，在`index.html`中，我们有:

```
<app-parent @app-button-clicked="showAlert"></app-parent>
```

它监听`app-button-clicked`事件并调用根 Vue 实例中的`showAlert`方法。

最后，当我们单击“Click Me”按钮时，我们将看到一个显示文本为“Clicked”的警报。

# 结论

在大多数类型的组件中，我们不能有多个根节点。碎片在 React 中可用，但在 Vue 中还没有。唯一的例外是功能组件，它们仅用于显示静态 HTML。

为了让表单输入这样的组件与其他组件很好地配合，一个好主意是让它们使用`v-model`来让它们的自定义输入元素绑定到父元素中的数据，方法是使用`value`属性并发出带有输入值的`input`事件。

因为 Vue 组件事件不会传播到父组件之外，所以我们必须使用`v-on='$listeners'`将事件从子组件发送到子组件的祖父组件。