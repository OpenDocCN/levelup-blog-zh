# 如何使用 Vue-Meta 在 Vue 中添加元数据

> 原文：<https://levelup.gitconnected.com/how-to-add-metadata-in-vue-using-vue-meta-55c593f4bbf5>

## 在本帖中，我们将探讨如何将 vue-meta 添加到我们的项目中，并使用它来处理组件内部的元数据

![](img/bdb17f232b1bd341fa8a2f69c03c0e44.png)

照片由[万花筒](https://unsplash.com/@kaleidico)拍摄

# 什么是 vue-meta？

[“vue-meta](https://vue-meta.nuxtjs.org/)”是一个提供 Vue 插件的模块，它允许我们从组件中动态添加元数据。

这意味着在我们有多条路线的项目中，我们希望根据页面上当前呈现的路线动态更新 SEO 的 meta 标签，vue-meta 将为我们处理这些，同时让我们控制应用程序元数据。

# 设置

首先，我们需要将 vue-meta 添加到我们的项目中，并让 vue 知道我们希望将它作为一个插件用于所有组件。

```
npm install vue-meta --save
```

然后，我们将 vue-meta 添加到我们的主 js 文件中。

```
// main.js or index.js
import Vue from "vue";
import App from "./App.vue"; // main component
import Meta from "vue-meta";Vue.use(Meta);new Vue({
  render: h => h(App)
}).$mount("#app");
```

# 添加元数据

现在我们来看一个如何向组件添加元数据的例子。

```
<template>
  <div id="app">
    <img width="25%" src="./assets/logo.png">
    <HelloWorld msg="Hello Vue in CodeSandbox!"/>
  </div>
</template><script>
import HelloWorld from "./components/HelloWorld";export default {
  name: "App",
  components: {
    HelloWorld
  },
  metaInfo() {
    return {
      title: "test meta data with vue",
      meta: [
        {
          vmid: "description",
          name: "description",
          content:
            "hello world, this is an example of adding a description with vueMeta"
        }
      ]
    };
  }
};
</script>
```

正如我们所看到的，我们可以通过调用“metaInfo”函数并返回一个包含元数据的对象值来实现。

此外，我们可以基于一些逻辑动态地设置元值，因为我们可以在组件级别访问它。

```
<template>
  <div id="app">
    <img width="25%" src="./assets/logo.png">
    <HelloWorld msg="Hello Vue in CodeSandbox!"/>
  </div>
</template><script>
import HelloWorld from "./components/HelloWorld";export default {
  name: "App",
  components: {
    HelloWorld
  },
  metaInfo() {
    const a = "test";
    return {
      title: "test meta data with vue",
      meta: [
        ...(a === "test" && [
          {
            vmid: "description",
            name: "description",
            content:
              "hello world, this is an example of adding a description with vue-meta"
          }
        ])
      ]
    };
  }
};
</script>
```

# 元数据的类型

我们可以使用“vue-meta”插件添加或多或少任何类型的元数据，无论是元数据、标题、链接还是脚本。

在下文中，我们将看到一个如何添加这些元数据的例子。

```
<script>
import HelloWorld from "./components/HelloWorld";export default {
  name: "App",
  components: {
    HelloWorld
  },
  metaInfo() {
    const a = "test";
    return {
      title: "test meta data with vue",
      meta: [
        ...(a === "test" && [
          {
            vmid: "description",
            name: "description",
            content:
              "hello world, this is an example of adding a description with vue-meta"
          }
        ])
      ],
			script: [
        { src: '<https://services.postcodeanywhere.co.uk/js/address-3.91.js>', async: true, defer: true, body: true }
      ],
			link: [
        {
          rel: 'canonical',
          href: '<https://malikgabroun.com/>'
        }
			]
    };
  }
};
</script>
```

在上面的例子中，我们可以看到如何使用 vue-meta 将外部脚本添加到正文中。在我们希望脚本包含在头部的情况下，我们可以通过移除 body 标志来实现。

# Vmid

到目前为止，我们研究了如何设置 vue-meta 并向组件动态添加元数据，但是，如果我们想在多个组件中为特定属性设置值，又该如何解决呢？

为此，我们可以使用 **vmid** ，这是 vue-meta 提供给我们的一个特殊属性，用于解析组件树中的值。因此，如果两组元具有相同的 vmid，它将覆盖它，使用最后更新的组件(即子组件)的值，而不是合并它。

# 结论

总之，vue-meta 是一个插件，在大多数 vue 框架中，它可以让我们控制元数据在网站中的显示方式。