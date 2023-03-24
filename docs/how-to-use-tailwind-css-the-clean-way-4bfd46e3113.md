# 如何干净地使用 Tailwind CSS

> 原文：<https://levelup.gitconnected.com/how-to-use-tailwind-css-the-clean-way-4bfd46e3113>

使用 Tailwind CSS 时不要弄乱你的 HTML。

![](img/6e5e3fe7d47f6d198a0a75660449e13b.png)

官方顺风 CSS logo，来源: [Github](https://camo.githubusercontent.com/53b9876cd8e38928387c6824043b0e2772b15b1bfdb7f42d0864216abbf3dfe8/68747470733a2f2f7265666163746f72696e6775692e6e7963332e63646e2e6469676974616c6f6365616e7370616365732e636f6d2f7461696c77696e642d6c6f676f2e737667)

*对于不清楚顺风 CSS 是什么的人，* [*在 medium*](https://medium.com/search?q=tailwind) *搜索顺风即可，或者直接看*[*https://tailwindcss.com*](https://tailwindcss.com/)*。*

简而言之，根据他们的网站，Tailwind CSS 是一个

> 实用第一的 CSS 框架，包含了像`flex`、`pt-4`、`text-center`和`rotate-90`这样的类，可以直接在你的标记中构建任何设计。

![](img/4b2e224615dd902646d9aa7060ae0f4d.png)

由[萨阿德·乔德里](https://unsplash.com/@saadchdhry?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 序文

> 快速建立现代网站，无需离开你的 HTML。

我知道我在这个教程中完全暗示了逆风标题(见上)的反面。但是请记住，我不想评判这个框架。我只想分享**另一个**选项**的使用方法。**

这个小教程只是一个关于如何使用这个框架而不陷入混乱的 HTML 代码的个人推荐。因此，进入 CSS 肯定是必须的。

你应该永远记住的一件事是:

> 对你的代码来说，最好的退休安排就是写得干净易读。

# 为什么在使用 Tailwind CSS 时会倾向于写乱七八糟的 HTML 代码？

如前所述，Tailwind 是一个实用程序优先的框架，它创建描述它们正在做什么的 CSS 类，例如`text-center`。一般来说，这是一个好主意，因为在编写 HTML 代码时，您不需要查看 CSS 在做什么。坏的一面是，你倾向于用 CSS 类来膨胀你的 HTML，这些 CSS 类很容易超过推荐的每行 80 个字符，使得你的代码几乎不可读。更重要的是，如果你想使用这个框架的响应能力。

# 设置

对于这个小教程，我假设您已经在项目中安装了 SASS 和 Tailwind CSS。您还需要一个本地开发服务器，例如使用`npm install http-server`和`npx http-server`来拥有一个基本服务器。

我在 Github 建立了一个[模板库。只要克隆它并运行`npm install`，你就万事俱备了。](https://github.com/emirror-de/template-sass-tailwindcss)

# 例子

为了演示这个设置，让我们看一下在撰写本文时已经出现在 Tailwinds 网站上的例子:

```
<figure class="md:flex bg-gray-100 rounded-xl p-8 md:p-0">
  <img class="w-32 h-32 md:w-48 md:h-auto md:rounded-none rounded-full mx-auto" src="/sarah-dayan.jpg" alt="" width="384" height="512">
  <div class="pt-6 md:p-8 text-center md:text-left space-y-4">
    <blockquote>
      <p class="text-lg font-semibold">
        “Tailwind CSS is the only framework that I've seen scale
        on large teams. It’s easy to customize, adapts to any design,
        and the build size is tiny.”
      </p>
    </blockquote>
    <figcaption class="font-medium">
      <div class="text-indigo-600">
        Sarah Dayan
      </div>
      <div class="text-gray-500">
        Staff Engineer, Algolia
      </div>
    </figcaption>
  </div>
</figure>
```

实际上，这个例子需要稍微调整一下，因为一些类在默认情况下是不可用的，例如 text-cyan-600 不存在(所以我们使用 Indigo)。除此之外，这个例子本身看起来不像是在他们的网站上“表现”它的那个例子，它看起来有点不同。给定的图片已被替换为[这张来自 flaticon.com 的图片](https://www.flaticon.com/free-icon/portrait_175062)。

![](img/025423add1a83899448d4c69831d747b.png)

作者截图，图标来自 flaticon.com

这个例子已经有了这么多的类，以至于一行中有超过 80 个字符，所以它非常适合我们的情况。

## 方法

*该方法使用前面提到的* [*范例库*](https://github.com/emirror-de/template-sass-tailwindcss) *的文件路径。如果您克隆这个存储库，您已经获得了完整的过程，并且您可以从那里开始开发。*

**构建流程**

为了能够将 Tailwind 与 SASS 一起使用，需要在 SASS 编译器运行之前构建。因此需要在`package.json`中设置一些脚本:

```
// some config before
"scripts": {
    "build-tailwind": "npx tailwindcss-cli@latest build -o build/tailwind.css",
    "concat-css": "concat -o build/main.concat.scss build/tailwind.css scss/main.scss",
    "compile-sass": "node-sass build/main.concat.scss public_html/css/main.css",
    "purge-css": "npx purgecss --config ./purgecss.config.js",
    "build": "npm run build-tailwind && npm run concat-css && npm run compile-sass",
    "build-prod": "npm run build-tailwind && npm run concat-css && npm run compile-sass && npm run purge-css"
  },
// some config afterwards
```

在那里，脚本执行以下操作:

*   `build-tailwind`编译顺风框架
*   `concat-css`连接 tailwind css 和 main.scss，因此 SASS 编译器能够识别 tailwind 生成的所有 css 类
*   `compile-sass`在您的`scss/main.scss`上运行 node-sass 编译器
*   `purge-css`将生成的 CSS 文件与您的 HTML 进行比较，并删除未使用的声明
*   `build`运行上面提到的前三个步骤，用于开发
*   `build-prod`运行构建最终 CSS 以准备生产所需的所有四个步骤

通常当`NODE_ENV=production`被设置时，Tailwind 会清除所有未使用的 CSS 类。因为我们修改了构建过程并将所有的类移动到一个 SCSS 文件中，所以需要我们在生成完整的 CSS 文件后手动运行 PurgeCSS。这就是`purge-css`脚本所做的。

**scss/main.scss**

因为我们的目标是简化 HTML，所以这是我们使用的每个 CSS 类的入口点。具体的实现取决于您的需求。出于演示的目的，我选择将每个类从我们的 HTML 代码移到这个文件中，假设每个图形在我们最终的网页上看起来都是一样的。

最终的 scss/main.scss 文件如下所示:

```
figure {
    [@extend](http://twitter.com/extend) .bg-gray-100;
    [@extend](http://twitter.com/extend) .rounded-xl;
    [@extend](http://twitter.com/extend) .p-8;
    [@extend](http://twitter.com/extend) .md\:flex;
    [@extend](http://twitter.com/extend) .md\:p-0; img {
        [@extend](http://twitter.com/extend) .w-32;
        [@extend](http://twitter.com/extend) .h-32;
        [@extend](http://twitter.com/extend) .rounded-full;
        [@extend](http://twitter.com/extend) .mx-auto;
        [@extend](http://twitter.com/extend) .md\:w-48;
        [@extend](http://twitter.com/extend) .md\:h-auto;
        [@extend](http://twitter.com/extend) .md\:rounded-none;
    } & > div {
        [@extend](http://twitter.com/extend) .pt-6;
        [@extend](http://twitter.com/extend) .text-center;
        [@extend](http://twitter.com/extend) .space-y-4;
        [@extend](http://twitter.com/extend) .md\:p-8;
        [@extend](http://twitter.com/extend) .md\:text-left; blockquote p {
            [@extend](http://twitter.com/extend) .text-lg;
            [@extend](http://twitter.com/extend) .font-semibold;
        } figcaption {
            [@extend](http://twitter.com/extend) .font-medium; div.name {
                [@extend](http://twitter.com/extend) .text-indigo-600;
            } div.work-role {
                [@extend](http://twitter.com/extend) .text-gray-500;
            }
        }
    }
}
```

**public_html/index.html**

生成的 HTML 文件现在看起来干净多了:

```
<figure>
  <img src="img/175062.png" alt="" width="384" height="512">
  <div>
    <blockquote>
      <p>
        “Tailwind CSS is the only framework that I've seen scale
        on large teams. It’s easy to customize, adapts to any design,
        and the build size is tiny.”
      </p>
    </blockquote>
    <figcaption>
      <div class="name">
        Sarah Dayan
      </div>
      <div class="work-role">
        Staff Engineer, Algolia
      </div>
    </figcaption>
  </div>
</figure>
```

# 结论

在这个简短的教程中，我想向您展示一个如何使用 TailwindCSS 的选项，它可以在不增加 HTML 代码的情况下进行扩展。这使您能够以更加模块化的方式进行开发，并为您增加了很多灵活性。

我希望你喜欢阅读！我很乐意回答评论中的任何问题！