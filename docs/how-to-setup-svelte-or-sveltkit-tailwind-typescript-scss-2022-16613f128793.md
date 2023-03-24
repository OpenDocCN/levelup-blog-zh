# 如何设置苗条或苗条+顺风+打字+ SCSS (2022)

> 原文：<https://levelup.gitconnected.com/how-to-setup-svelte-or-sveltkit-tailwind-typescript-scss-2022-16613f128793>

![](img/58b6771adc9640f067abe2eccf3aa922.png)

费伦茨·阿尔马西在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

这是我目前最喜欢的前端设置，我发现使用它非常愉快，因为苗条和顺风允许快速、简单和有效的开发。

Typescript 允许我们编写更健壮的代码，更早地捕捉错误，以及智能感知的好处。

它还使用 Vite 进行闪电般的快速热重装，这将随着项目的增长而保持快速。

最后，当顺风还不够时，SCSS 将允许你编写可管理的定制 CSS。

# 这很容易，而且你有多种选择

整个设置大约需要 4-5 个命令，应该不会花很长时间。

我将提供选项，以便您可以选择使用或不使用 SvelteKit，或者使用或不使用 Typescript。

# 如何设置

首先，你需要决定是否要使用 SvelteKit。

简单来说，你可以把 SvelteKit 看作是 Vue 的 Nuxt 或者 React 的 Next 的等价物。

它提供了 SSR、路由、布局，您还可以创建 API 端点。

如果你犹豫不决，我会推荐使用 SvelteKit。

做出你的选择并跳转到相应的部分，你可以在那里选择 TS 或 JS。

# 苗条套装

```
npm create svelte@latest my-app
```

命令行将要求您选择一个模板:

*   骨架项目:从零开始一个新的苗条项目，通常是你想要的
*   SvelteKit 演示应用程序:如果你是 SvelteKit 的新手，你可能想从一些示例代码开始
*   库框架项目:如果你的目标是为 Svelte 构建一个工具或库，然后可以作为 npm 包发布，这就是你想要的选项

接下来，您可以选择 JS、TS 或两者的混合:

*   使用类型脚本语法:使用普通的类型脚本，这是我通常使用的
*   使用带有 JSDoc 注释的 Javascript:这是一个混合选项，您可以编写 JS，但用键入的注释对其进行注释，[查看这篇文章以获得完整的解释](https://www.swyx.io/jsdoc-swyxkit)
*   不:如果你只是想要普通的 JS

接下来，它会问你是否想使用 ESLint，我建议你这样做，因为它会使你的代码标准化。

接下来，它会问你是否想使用更漂亮的代码格式，我也建议你说是。

接下来是剧作家，这是如果你想 E2E 测试，我不熟悉它，所以没有意见在这里。你可能更熟悉 Cypress，如果你愿意，你可以用它来代替。

然后

```
cd my-app
npm install
```

这就是苗条装备的设置，你现在可以跳到顺风部分。

# 苗条的

以打字打的文件

```
npm create vite@latest my-app -- [--template svelte](https://github.com/vitejs/vite/tree/main/packages/create-vite/template-svelte)-ts
```

香草 JS

```
npm create vite@latest my-app -- [--template svelte](https://github.com/vitejs/vite/tree/main/packages/create-vite/template-svelte)
```

然后

```
cd my-app
npm install
```

# 顺风

多亏了`[svelte-add](https://github.com/svelte-add/tailwindcss)` npm 包，它就像运行这个命令一样简单，并且对 SvelteKit 和 Svelte 都有效

```
npx svelte-add@latest tailwindcss
```

之后运行`npm install`来安装依赖项，否则会得到一堆错误。

现在你可以在你的应用中使用 Tailwind 了。

如果你选择了 Svelte，在`src/App.svelte`中添加下面一行，如果你用的是 SvelteKit，在`src/routes/+page.svelte`中添加它来测试它是否真的工作。

`npm run dev`并导航至`[http://localhost:5173](http://localhost:5173)`

您应该会看到新段落以蓝绿色显示。

如果没有，可能是因为有些 CSS 覆盖了 Tailwind 类。检查组件本身周围是否有`styles.css`或一些`<style>`块，以及`p`元素的一些颜色规则。

如果你使用的是 VSCode，我还会推荐你安装令人敬畏的[“顺风 CSS 智能感知”扩展](https://marketplace.visualstudio.com/items?itemName=bradlc.vscode-tailwindcss)，它将为你提供顺风类的自动完成功能。我发现颜色自动补全特别有用。

# SCSS

如果你也想使用 SCSS，你需要做的就是安装`sass`和`node-sass`

```
npm install sass node-sass
```

然后，您可以像以前一样将这段代码添加到文件中进行测试

现在，您应该会看到红色的新段落。

最后，如果你用 Prettier 安装了 SvelteKit，你可能想把`useTabs`选项改为`false`，这样你就有了真正的空格字符，而不是缩进的表格。

这取决于个人偏好，但我认为空格更可靠，因为它们在编辑器之间总是保持相同的缩进。例如，当将上面的代码粘贴到 gist 中时，它会在编辑时显示 2 个空格(与 VSCode 中相同)，但在文章中，它会显示 4 个空格缩进。

# 你都准备好了

现在，您已经有了一个可以继续构建的项目。

SvelteKit 已经负责路由，如果你需要的话，你可以添加一个路由器库。

对于应用程序范围内的状态管理，可以很简单地在任何组件中导入苗条的商店。

[X-state](https://xstate.js.org/) 是创建复杂健壮的可重用逻辑的另一种选择。这需要一些学习，但它非常强大。

我希望你能喜欢这个设置，并和我一样喜欢它。