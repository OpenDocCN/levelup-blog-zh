# 设置在 Vite 上运行的 Vue 应用程序

> 原文：<https://levelup.gitconnected.com/set-up-a-vue-app-running-on-vite-e816247a24e2>

![](img/4e3b4e6dda04255dedd61d838489c3c1.png)

> 本文原载于[我的个人博客](https://aymanemx.com/posts/set-up-vue-app-running-on-vite/)。

这是一个初学者指南，介绍如何在 Vite 上运行 vue.js 应用程序。我还将为林挺和代码格式化添加和配置 ESLint & Prettier，为样式化设置 Tailwind CSS，最后配置 VueX 和 Vue 路由器。

那么，让我们开始吧！

# 设置 Vite.js

等等，Vite.js 是什么鬼东西？

主要是 Vue 开发人员将使用 Vue CLI 来编译他们的项目，这带来了一些缺点:你必须等到你的整个应用程序被捆绑才能开始开发，这可能会使冷服务器启动非常慢。较大的项目也可能遭受缓慢的热模块更换(HMR)。Vite 通过按需编译代码来解决这些问题，只编译当前屏幕上导入的代码，HMR 性能与模块总数无关，无论应用程序有多大，HMR 都能保持快速运行。

实际上，Vite 有一个真正好的[文档](https://vitejs.dev/guide/)，看看吧。

如果您使用的是`npm`，只需运行这个命令并按照步骤操作；

```
$ npm init @vitejs/app
```

# 设置顺风 CSS

```
$ npm install -D tailwindcss@latest postcss@latest autoprefixer@latest
```

创建配置文件:

```
$ npx tailwindcss init -p
```

在你的 CSS 中包含顺风

```
/* ./src/index.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

最后，确保您的 CSS 文件被导入到您的`./src/main.js`文件中:

```
// src/main.js
import { createApp } from 'vue'
import App from './App.vue'
import './index.css'createApp(App).mount('#app')
```

更多信息见[文档](https://tailwindcss.com/docs/guides/vue-3-vite)。

# 安装 ESLint &更漂亮

```
$ npm install --save-dev eslint prettier eslint-plugin-vue eslint-config-prettier
```

创建文件`.eslintrc.js`并通过那些配置；

```
module.exports = {
    extends: [
        'plugin:vue/vue3-essential',
        'prettier',
    ],
    rules: {
        // override/add rules settings here, such as:
        'vue/no-unused-vars': 'error',
    },
}
```

并使用以下配置创建一个`.prettierrc.js`文件:

```
module.exports = {
    semi: false,
    tabWidth: 4,
    useTabs: false,
    printWidth: 80,
    endOfLine: 'auto',
    singleQuote: true,
    trailingComma: 'es5',
    bracketSpacing: true,
    arrowParens: 'always',
}
```

# 安装 Vue 路由器

为了什么！为什么我们需要 Vue 路由器？

Vue 路由器是 Vue.js 的官方路由器，它与 Vue.js core 深度集成，让用 Vue.js 构建单页面应用变得轻而易举。

我们需要 Vue 3 的版本 4，所以让我们运行，

```
$ npm install vue-router@4
```

然后，用下面的代码创建`src/router/index.js`文件，我们将在其中创建路由器，用一个链接到路径`/`的名为`Home`的组件初始化它(我们将在接下来的步骤中创建组件):

```
import { createRouter, createWebHistory } from 'vue-router'
import Home from '/src/components/Home.vue'const routes = [
    {
        path: '/',
        name: 'Home',
        component: Home,
    },
]const router = createRouter({
    history: createWebHistory(),
    routes,
})export default router
```

在`App.vue`中，将`helloworld`组件更换为`<router-view/>`。

创建一个主构件，例如:

```
<template>
  <h1>Home!!</h1>
</template>
```

在`main.js`中导入路由器，在 app 挂载前使用！

```
import router from "./router/index"createApp(App).use(router).mount('#app')
```

# 安装 Vuex

它基本上是一个状态管理库，如果你正在构建一个简单的应用程序，你可能不需要它。

```
$ npm install vuex@next --save
```

更多信息见[文档](https://vuex.vuejs.org/guide/#the-simplest-store)。

现在，您已经准备好设计您的 Vue 应用程序了！

最后，这是我的演示应用程序的源代码:

[](https://github.com/aymaneMx/vite-app) [## aymaneMx/vite-app

### kolchi kayn f doc，$ NPM install-D tailwindcss @ latest post CSS @ latest autoprefixer @ latest create config file:$ npx…

github.com](https://github.com/aymaneMx/vite-app) 

尽情享受吧！下次再见。

# 资源:

*   [开始使用 Vite，这是 Vue.js 的一个无捆绑器开发环境](https://medium.com/@wearethreebears/getting-started-with-vite-a-no-bundler-dev-environment-for-vue-js-217a6eb7c9d0)。
*   [2021 年开始使用 Vue 3+Vite](https://youtu.be/O8epzPrsADI)。