# 如何使用带有 Tailwind CSS/JIT 的 Svelte Kit(即时编译)

> 原文：<https://levelup.gitconnected.com/how-to-use-svelte-kit-with-tailwind-css-jit-just-in-time-compilation-bc04c0c9ec17>

![](img/ab6a69057e2a9d0816c095de1417dcc3.png)

期待已久的 Sveltekit 终于来了，它就像我们想象的那样令人惊叹！它带来了许多很酷的新特性，使开发过程变得更加容易。

苗条并不是唯一的 js 包烹饪一些特别的东西，顺风刚刚发布了他们的 2.2 版本，有一些新的和令人惊讶的功能。最重要的是 JIT，如果你错过了，你可以在这里查看。

它基本上监视你的文件，并编译他们的飞行！输出 CSS 只使用您已经包含在项目中的类。这会产生一个非常非常小的输出文件，并允许使用 tailwind 实现更大的灵活性。

在本教程中，我们将通过这些步骤让你使用最新最好版本的带有 SvelteKit 的 tailwind。
回购的链接将在本指南末尾提供。

我们将在本教程中使用 PostCSS，如果你是新手，请点击[这个链接](https://postcss.org/)来了解它。

# 创建一个苗条工具包应用程序

首先，通过输入以下命令创建一个全新的 svelte kit app。(选择一个框架项目)

```
npm init svelte@next your-awesome-project# Choose a skeleton project from the cli
# We'll choose Javacript for this example instead of Typescriptcd your-awesome-project # Install the dependencies with:yarn or npm
```

# 添加开发依赖项

那很容易！接下来，安装以下开发依赖项。

```
yarn add -D autoprefixer postcss-cli tailwindcss concurrently cross-env#Or if you are using npmnpm install autoprefixer postcss-cli tailwindcss concurrently cross-env --save-dev
```

如果您运行的是 windows，跨 env 包将允许您在 package.json 脚本中设置环境变量，因为通常的 ENV_VAR=value 命令会导致错误。

# 配置 Post CSS 和 Tailwind

在项目的根目录下，创建 Post CSS & Tailwind 配置文件:

```
**./postcss.config.cjs** in the root of the project
and **src/styles/tailwind.css**Then enter the command**npx tailwindcss init tailwind.config.cjs**
```

## **🔴注意到我们是如何使用？cjs 扩展名，而不是。js**

在 **postcss.config.cjs** 中，粘贴以下内容:

```
*module*.*exports* = {
  plugins: {
    autoprefixer: {},
    tailwindcss: {},
  },
}
```

在 src/styles/tailwind.css 中:

```
@tailwind base;
@tailwind components;
@tailwind utilities;
```

# Npm 脚本

将以下脚本添加到 package.json 中(覆盖 svelte 设置的原始值)

```
"scripts": {

  "dev:only": "svelte-kit dev",

  "build:only": "svelte-kit build",

  "preview": "svelte-kit preview",

  "tailwind:watch": "cross-env TAILWIND_MODE=watch cross-env NODE_ENV=development postcss src/styles/tailwind.css -o src/styles/tailwind-output.css -w",

  "tailwind:build": "cross-env TAILWIND_MODE=build cross-env NODE_ENV=production postcss src/styles/tailwind.css -o src/styles/tailwind-output.css",

  "dev": "concurrently \"yarn run dev:only\" \"yarn run tailwind:watch\"", "build": "yarn run tailwind:build && yarn run build:only"
},
```

同时允许服务器和 PostCSS 一起运行，post CSS 会观察你的顺风并在。/src/style/folder。

这个文件以后会用到。

# 运行服务器

```
yarn run dev#Or if you are using npmnpm run dev
```

打开 [http://localhost:3000](http://localhost:3000)

# 将生成的顺风样式导入到项目中

创建名为 __layout.svelte 的布局组件:

```
src/routes/__layout.svelte
```

inner _ _ layout . svelte

```
*<script>
  import "../styles/tailwind-output.css";
</script>*<!-- Try some classes here --><h1 class="uppercase text-indigo-500">
  Hello People of Earth
</h1>
```

就像那样，你已经在你的苗条应用程序中添加了 tailwind CSS。

还有最后一件事。
为了帮助 tailwind 知道从哪里得到要编译的类。我们必须给它我们的源目录的位置。否则，产生的 tailwind-output.css 将为空。

# 最后一个配置

在 **tailwind.config.cjs** (根目录)中，输入以下代码:

```
module.exports = {
  // ...
  mode: 'jit', // ⚠ Make sure to have this
  purge: ["./src/**/*.svelte"],
  // ...
}
```

用 **Ctrl+c** 停止服务器，用**纱线运行开发**重新运行

就是这样！你现在可以在 sveltekit 中使用你所有的顺风职业。

感谢您花时间阅读！

你喜欢这篇文章吗？留下反馈，并与可能会觉得有用的人分享。

# 链接:

🔗[项目库](https://github.com/katendeglory/sveltekit-with-tailwind-jit)