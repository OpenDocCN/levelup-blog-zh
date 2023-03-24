# 使用 vue-js-modal 库向 Vue 应用程序添加对话框

> 原文：<https://levelup.gitconnected.com/add-a-dialog-box-to-a-vue-app-with-the-vue-js-modal-library-81a72eee6936>

![](img/91f62f0a85668f89cd8325a5429394af.png)

照片由 [chuttersnap](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本文中，我们将看看如何用 vue-js-modal 库添加一个模型。

# 对话

对话框是模态的简化版本，默认设置了大多数参数。

这对于快速构建原型和显示警告非常有用。

当我们调用`Vue.use`来显示对话框时，我们可以将`dialog`设置为`true`。

例如，我们可以写:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import VModal from "vue-js-modal";
Vue.use(VModal, {
  dialog: true
});Vue.config.productionTip = false;new Vue({
  render: (h) => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div>
    <v-dialog/>
  </div>
</template>
<script>
export default {
  name: "App",
  methods: {
    show() {
      this.$modal.show("dialog", {
        title: "Dialog title",
        text: "Lorem ipsum dolor sit amet.",
        buttons: [
          {
            title: "Cancel",
            handler: () => {
              this.$modal.hide("dialog");
            }
          },
          {
            title: "Like",
            handler: () => {
              alert("Like action");
            }
          },
          {
            title: "OK",
            handler: () => {
              alert("success");
            }
          }
        ]
      });
    }
  },
  mounted() {
    this.show();
  }
};
</script>
```

我们用一个属性为`dialog`的对象将插件注册到`true`。

然后在`show`方法中，我们添加了`this.$modal.show`方法来显示对话框。

第一个参数是对话框的名称。

第二个参数中的对象有各种选项，我们可以设置为对话框。

`title`有对话框标题。

`text`有正文。

`buttons`属性有一个带有按钮选项的数组。

它们是由对象定义的。`title`有按钮文本。

`handler`有点击按钮时运行的事件处理程序。

`this.$modal.hide`让我们隐藏模态。我们引用对话框来关闭我们引用对话框的名称。

# 事件

模态发出各种事件。要听它们，我们可以写:

```
<template>
  <modal name="example" @before-open="beforeOpen" @before-close="beforeClose">
    <span>Hello, {{ name }}!</span>
  </modal>
</template>
<script>
export default {
  name: "Example",
  data() {
    return {
      name: "world"
    };
  },
  methods: {
    beforeOpen(event) {
      console.log("Opening...");
    },
    beforeClose(event) {
      console.log("Closing...");
      if (Math.random() < 0.5) {
        event.cancel();
      }
    }
  },
  mounted() {
    this.$modal.show("example");
  }
};
</script>
```

我们监听从`modal`组件发出的`before-open`和`before-close`事件。

`before-open`模态打开时发出。

`before-close`模态关闭时发出。

`event`参数是一个带有`cancel`方法的对象，我们可以用它来取消操作。

由于我们在`beforeClose`方法中调用了`event.cancel`，它将取消关闭模态关闭动作。

# 时间

组件有一个槽，我们可以用自己的内容填充它。

例如，我们可以写:

```
<template>
  <modal name="example">
    <div slot="top-right">
      <button @click="$modal.hide('example')">❌</button>
    </div>Hello, ☀️!
  </modal>
</template>
<script>
export default {
  name: "App",
  mounted() {
    this.$modal.show("example");
  }
};
</script>
```

`top-right`槽将内容添加到模态的右上角。

默认槽有主模态内容。

# 结论

我们可以很容易地添加对话框，并用 vue-js-modal 库定制其内容。