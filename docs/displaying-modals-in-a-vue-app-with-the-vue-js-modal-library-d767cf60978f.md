# 使用 vue-js-modal 库在 Vue 应用程序中显示模态

> 原文：<https://levelup.gitconnected.com/displaying-modals-in-a-vue-app-with-the-vue-js-modal-library-d767cf60978f>

![](img/45671170fc40351e2d041e9fba039df1.png)

杰罗姆·霍伊泽在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在这篇文章中，我们将看看用 vue-js-modal 库添加 modal 的 Vue 包。

# vue-js-modal

vue-js-modal 包是一个简单易用的库，用于向我们的应用程序添加模型。

要使用它，我们通过运行以下命令来安装它:

```
npm i vue-js-modal
```

然后，我们通过添加以下内容来注册它:

```
import Vue from "vue";
import App from "./App.vue";
import VModal from "vue-js-modal";Vue.use(VModal);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

接下来，我们可以使用`modal`组件将它添加到我们的组件中:

```
<template>
  <div id="app">
    <button @click="show">open</button>
    <modal name="hello-world">
      <button @click="hide">hide</button>
      <p>hello, world!</p>
    </modal>
  </div>
</template><script>
export default {
  name: "App",
  methods: {
    show() {
      this.$modal.show("hello-world");
    },
    hide() {
      this.$modal.hide("hello-world");
    }
  }
};
</script>
```

我们通过向`show`方法传递`name`属性的值来打开模态。

同样，我们用`hide`方法来隐藏它。

我们可以在模态打开之前调用一个方法。

例如，我们可以写:

```
<template>
  <div id="app">
    <button @click="show">open</button>
    <modal name="hello-world" @before-open="beforeOpen">
      <p>hello, world!</p>
    </modal>
  </div>
</template><script>
export default {
  name: "App",
  methods: {
    show() {
      this.$modal.show("hello-world", { foo: "bar" });
    },
    beforeOpen(event) {
      console.log(event.params.foo);
    }
  }
};
</script>
```

我们向`show`方法传递了一个对象。

然后我们将在`event.params`属性中获取该对象。

它还带有一个简单版本的模态。

为了使用它，我们用`Vue.use`的`dialog`选项传入一个对象:

```
import Vue from "vue";
import App from "./App.vue";
import VModal from "vue-js-modal";Vue.use(VModal, { dialog: true });
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

然后我们可以通过写来使用它:

```
<template>
  <div id="app">
    <button @click="show">open</button>
    <v-dialog/>
  </div>
</template><script>
export default {
  name: "App",
  methods: {
    show() {
      this.$modal.show("dialog", {
        title: "Message",
        text: "hello",
        buttons: [
          {
            title: "OK",
            handler: () => {
              alert("closed");
            }
          },
          {
            title: "Close",
            default: true,
            handler: () => {}
          },
          {
            title: "Cancel"
          }
        ]
      });
    }
  }
};
</script>
```

我们设置`title`来显示标题，`text`来显示里面的内容。

`buttons`将我们想要的按钮添加到对话框的底部。

`v-dialog`让我们在添加到模板时显示对话框。

我们也可以传入模板和道具。

例如，我们可以写:

`main.js`

```
//...
Vue.use(VModal, { dynamic: true, injectModalsContainer: true })
//...
```

`App.vue`

```
<template>
  <div id="app">
    <button [@click](http://twitter.com/click)="show">open</button>
    <modals-container/>
  </div>
</template><script>
export default {
  name: "App",
  methods: {
    show() {
      this.$modal.show(
        {
          template: `
            <div>
              <h1>Message</h1>
              <p>{{ text }}</p>
            </div>
          `,
          props: ["text"]
        },
        {
          text: "hello world"
        },
        {
          height: "auto"
        },
        {
          "before-close": event => {
            console.log("this will be called before the modal closes");
          }
        }
      );
    }
  }
};
</script>
```

我们在传递给`show`的对象中定义了`text`属性。

然后我们可以用第二个参数中的道具传入一个对象。

第三个参数有样式。

我们可以发出一个`close`事件来关闭模态:

```
<template>
  <div id="app">
    <button [@click](http://twitter.com/click)="show">open</button>
    <modals-container/>
  </div>
</template><script>
export default {
  name: "App",
  methods: {
    show() {
      this.$modal.show(
        {
          template: `
            <div>              
              <p>{{ text }}</p>
              <button @click="$emit('close')">Close</button>
            </div>
          `,
          props: ["text"]
        },
        {
          text: "hello world"
        },
        {
          height: "auto"
        },
        {
          "before-close": event => {
            console.log("this will be called before the modal closes");
          }
        }
      );
    }
  }
};
</script>
```

我们可以通过 props 设置许多其他选项，包括宽度、高度、过渡、可拖动、可滚动等等。

![](img/231c43a7f6dd4883bfa208d9cf3e4780.png)

由[安迪·霍尔姆斯](https://unsplash.com/@andyjh07?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们可以使用 vue-js-modal 库向我们的应用程序添加模态。

它有许多选项，我们可以用来使模态看起来像我们的方式。

我们可以向它传递数据，显示和隐藏，拖动它，以及用它做更多的事情。