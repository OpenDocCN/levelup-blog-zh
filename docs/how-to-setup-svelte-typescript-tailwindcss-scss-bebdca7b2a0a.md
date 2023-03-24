# 如何设置官方支持的 Svelte+TailwindCSS+SCSS

> 原文：<https://levelup.gitconnected.com/how-to-setup-svelte-typescript-tailwindcss-scss-bebdca7b2a0a>

![](img/2db80cca67ce4d42a023d21ae9f855be.png)

由[费伦茨·阿尔马西](https://unsplash.com/@flowforfrank?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 2022 年更新

这篇文章是 2 年前写的，很多东西都变了，我写了一个 2022 年更新的版本，你应该照着做

# 用 Typescript 设置 Svelte

Svelte 刚刚宣布了他们对 Typescript 的完全官方支持，这是一个完整的指南，可以在 TailwindCSS 和 SCSS 的新项目中设置它。

我将使用`yarn`作为包管理器，但是如果你愿意，你也可以使用`npm`。

我们从默认的苗条模板开始

```
npx degit sveltejs/template svelte-ts
cd svelte-ts
yarn
```

然后使用提供的安装脚本添加 Typescript

```
node scripts/setupTypeScript.js
yarn
```

Typescript 支持依赖于几个包，所以在脚本运行后再次运行包管理器。

Svelte 现在为`.svelte`文件提供了一个官方的 VSCode 扩展[。如果你还没有安装它，你应该安装它。](https://marketplace.visualstudio.com/items?itemName=svelte.svelte-vscode)

如果你安装了詹姆斯·比特莱斯以前的扩展，你需要先移除它，你可以通过进入扩展并搜索`@installed`来移除它

或者只是运行命令行来删除它

```
code --uninstall-extension JamesBirtles.svelte-vscode
```

你可以在这里了解更多关于 [Svelte 的类型脚本支持，它也描述了如何手动设置一个现有的项目。](https://svelte.dev/blog/svelte-and-typescript)

这样，我们就可以使用 Typescript 和 Svelte 了。

# 添加 TailwindCSS

添加`tailwindcss`包并生成`tailwind.config.js`配置文件。

```
yarn add tailwindcss
npx tailwindcss init
```

现在我们需要将 Tailwind 的 CSS 类导入到我们的应用程序中，让我们创建一个`Tailwind.svelte`组件，它将通过一个`global`样式标签来完成这个任务。

我们使用`global`是因为我们需要将这些类应用于页面中的每个元素，否则我们的样式将仅限于该组件。

现在我们将这个组件直接导入到我们的主`App.svelte`组件中，以确保它总是被包含在内。我们还将清理默认代码。

首先，删除`public/global.css`中的所有 CSS 代码，然后替换`App.svelte`

如果你现在运行这个应用程序，你会注意到文本不是红色的，而且还没有应用顺风的样式。

原因是`@tailwind`指令还没有任何意义。为了让我们的构建工具用正确的 CSS 样式替换它们，我们必须告诉 Rollup 如何通过`svelte-preprocess`对它们进行预处理。

大部分设置已经为我们完成了，因为 Typescript 设置已经和`svelte-preprocess`一起提供了，但是我们仍然需要告诉它使用`postcss`和一些其他的包。

让我们从添加这些包开始

```
yarn add -D postcss postcss-load-config @fullhuman/postcss-purgecss 
```

创建一个`postcss.config.js`文件，注意这里的名字很重要，因为`postcss-load-config`将会寻找这个文件。

由于 Tailwind 有大量的 CSS 类来支持灵活的样式，它默认生成的 CSS 文件相当大(大约 1.6 Mb 未压缩)。

当你只使用其中的一小部分时，你不会想运送那么多的 CSS。

这就是为什么配置`purgecss`很重要的原因，它将负责删除我们不使用的任何类，使我们的产品 CSS 包更小。

配置文件告诉`purgecss`查看`src`文件夹中的所有`.html`和`.svelte`文件。

`whitelistPatterns`选项告诉`purgecss`不要删除从`svelte-`开始的类。Svelte 生成这些类来保持每个组件的样式范围。因为这些是生成的，所以它们不会出现在我们组件的代码中，并且会被`purgecss`移除，以避免我们将它们列入白名单。

如果您正在使用其他生成类的工具，您需要使用这个，提供`font-awesome`支持的`svelte-awesome`是一个很好的例子，因为它添加了`fa-icon`类，您还需要将`/fa-icon/`添加到白名单中。

一般来说，如果您的任何风格在产品构建中消失了，这可能是白名单问题。

现在我们需要通过`svelte-preprocess`告诉 Rollup 使用`postcss`

在`rollup.config.js`中，在调用`svelte`函数的`plugins`数组中，你会看到`preprocess`选项不带任何参数地调用`sveltePreprocess`。

我们需要告诉`sveltePreprocess`我们想要使用`postcss`，为此我们将传递给它一个配置对象

现在你可以运行`yarn dev`了，你应该会看到我们的`h1`现在如预期的那样是红色的。顺风现在设置好了。

# 添加 SCSS 支持

使用 TailwindCSS，您很少需要编写 CSS，但仍有需要自定义 CSS 的情况。

如果也支持 SCSS 就好了，主要是能够编写嵌套的 CSS。

我们将增加几个包

```
yarn add -D node-sass autoprefixer
```

以及更多的汇总配置

仅此而已！现在你可以通过添加`lang="scss"`到你的风格标签中来使用 SCSS。

```
yarn dev
```

耶，我们的文字又红又大！

# 现成的项目模板

我还制作了这个设置的模板，这样你就可以快速启动一个项目，只需运行

```
npx degit [https://github.com/skflowne/svelte-ts-scss-tailwind-template](https://github.com/skflowne/svelte-ts-scss-tailwind-template) my-app
```

然后 cd 进入你的应用程序并运行`yarn`或`npm install`

# 结论

对我来说，这似乎是一个可靠的设置，可以构建一个轻量级的应用程序，使用 Typescript 享受类型安全，借助 Tailwind 快速构建一个响应性良好的 UI，并能够在需要时使用 SCSS 编写更高级的 CSS。

如果你用它造了什么东西就告诉我。