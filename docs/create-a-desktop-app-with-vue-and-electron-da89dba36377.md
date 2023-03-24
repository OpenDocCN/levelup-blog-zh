# 用 Vue 和电子创建一个桌面应用程序

> 原文：<https://levelup.gitconnected.com/create-a-desktop-app-with-vue-and-electron-da89dba36377>

![](img/0403716a1303a2f01067cc9f2f54c7fd.png)

照片由[诺贝特·莱瓦西斯](https://unsplash.com/@levajsics?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Electron 是一个应用程序框架，让我们可以构建基于 web 应用程序的桌面应用程序。这些应用是跨平台的，使用 Chromium 浏览器引擎渲染。我们可以使用[vue-CLI-plugin-electronic-builder](https://github.com/nklayman/vue-cli-plugin-electron-builder)代码生成器来构建一个基于 Vue.js 的电子 app

在本文中，我们将看看如何构建一个简单的电子 Vue 应用程序。

# 入门指南

我们可以从创建一个 Vue.js 项目开始。

为此，我们创建一个空的项目文件夹，进入该文件夹并运行:

```
npx vue create .
```

然后我们按照说明添加我们想要的项目。

接下来，我们运行 Vue CLI 插件电子生成器，将电子文件添加到我们的 Vue 应用程序中。

我们运行:

```
vue add electron-builder
```

自动添加所有文件和设置。

然后，为了启动开发服务器，我们运行:

```
yarn electron:serve
```

或者

```
npm run electron:serve
```

我们应该看到我们的应用程序显示一个窗口。还应该显示开发控制台。

现在我们只需要编写我们的 Vue 应用程序。

# 编写代码

我们像任何其他 Vue 应用程序一样编写代码。

在`App.vue`中，我们写道:

```
<template>
  <div id="app">
    <form @submit.prevent="add">
      <input type="text" v-model="todo" />
      <input type="submit" value="add" />
    </form>
    <div v-for="(t, i) of todos" :key="t.id">
      {{t.todo}}
      <button @click="remove(i)">remove</button>
    </div>
  </div>
</template><script>
import { v4 as uuidv4 } from "uuid";export default {
  name: "App",
  data() {
    return {
      todo: "",
      todos: [],
    };
  },
  methods: {
    add() {
      this.todos.push({ id: uuidv4(), todo: this.todo });
      this.todo = "";
    },
    remove(index) {
      this.todos.splice(index, 1);
    },
  },
};
</script>
```

在我们的应用程序中添加待办事项列表。

我们有一个添加待办事项列表的表单。然后我们有了向`this.todos`添加条目的`add`方法。

我们使用`uuid` NPM 包为每个条目创建唯一的 id，我们通过运行以下命令来安装该包:

```
npm i uuid
```

我们用`remove`删除给定索引的`this.todos`条目。

在表单中监听带有`@submit.prevent`指令的`submit`事件，同时调用`preventDefault`来阻止默认提交行为。

现在我们应该看到我们的 todo 应用程序显示在窗口中。

# 构建我们的应用

为了将我们的应用程序构建成可执行文件，我们运行:

```
yarn electron:build
```

用纱线或:

```
npm run electron:build
```

和 NPM 一起。

# 本机模块

我们可以将本机模块添加到我们的应用程序中的`vue.config.js`文件中:

```
module.exports = {
  pluginOptions: {
    electronBuilder: {
      externals: ['my-native-dep'],
      nodeModulesPath: ['../../node_modules', './node_modules']
    }
  }
}
```

我们可以添加`node_module`路径来定位模块。

`externals`用于列出与我们的应用程序不兼容的模块名称。

# 网络工作者

要添加工人，我们通过编写以下内容将他们添加到`vue.config.js`文件中:

```
const WorkerPlugin = require('worker-plugin')module.exports = {
  configureWebpack: {
    plugins: [new WorkerPlugin()]
  }
}
```

我们通过运行以下命令来安装`worker-plugin`包:

```
npm i worker-plugin
```

现在，我们可以通过编写以下内容来使用工人文件:

`src/worker.js`

```
onmessage = (e) => {
  const { a, b } = e.data;
  const workerResult = +a + +b;
  postMessage(workerResult);
}
```

`src/App.vue`

```
<template>
  <div id="app">
    <form @submit.prevent="send">
      <input type="text" v-model="a" />
      <input type="text" v-model="b" />
      <input type="submit" value="add" />
    </form>
    <p>result: {{result}}</p>
  </div>
</template><script>
const worker = new Worker("./worker.js", { type: "module" });export default {
  name: "App",
  data() {
    return {
      a: 0,
      b: 0,
      result: 0
    };
  },
  mounted() {
    worker.onmessage = this.onMessage;
  },
  methods: {
    send() {
      const { a, b } = this;
      worker.postMessage({ a, b });
    },
    onMessage({data}) {
      this.result = data;
    },
  },
};
</script>
```

我们创建了一个监听`message`事件的 worker。

`message`事件是由我们在`send`方法中发出的`worker.postMessage`方法发出的。

`send`从我们输入的内容中获取数据。

一旦发出了`message`事件，我们就从`e.data`获得数据。

然后我们计算结果，并调用`postMessage`将计算结果发送回`App.vue`。

`App.vue`从`onMessage`方法中获取结果。

我们从中显示出`result`。

# 结论

我们可以用 Vue.js 和[vue-CLI-plugin-electron-builder](https://github.com/nklayman/vue-cli-plugin-electron-builder)生成器创建一个桌面应用。

它支持网络工作者。