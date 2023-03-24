# 网站性能优化

> 原文：<https://levelup.gitconnected.com/website-performance-optimization-cd1647498274>

![](img/19895d9a062b02fa5e18309df84c22eb.png)

由 [Traf](https://unsplash.com/@traf?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/speed?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

会有那些紧张的日子，你会疯狂地思考和研究如何优化你的网站的性能和速度。

嗯，我已经经历过了，完全理解这种痛苦，所以在这里分享所有帮助我和我的朋友的发现，并且肯定会帮助你改善你的网站的页面加载时间。

# **代码拆分**

在 SPAs 中，您可能已经体验过主 Javascript 包变得如此之大(因为单个 Javascript 文件负责加载您的整个网站),以至于浏览器需要花费大量时间来加载、解析、编译和执行您的脚本，从而增加了页面的交互时间。

嗯，你很幸运，c *颂分裂*是*的*来救你了。它允许您将应用程序的 Javascript 分成更小的块，允许您发送加载用户可见页面所需的最少代码，其余代码可以随时随地按需加载。最佳实践是将每个块的大小保持在 150KB 以下。即使在糟糕的网络上，该应用程序也能在 5 秒内变得交互式。

有两种流行的实现代码拆分的方法，基于路由的和基于组件的。你真的需要注意如何拆分你的应用程序代码，因为这将对你的页面速度产生重大影响。

现在，webpack 提出了许多实现代码拆分的方法。

*   使用 Javascript 的动态导入 api `import()`的动态导入
*   使用`entry`配置分割代码，以及
*   `SplitChunksPlugin`

查看 webpack 关于代码分割的[文档](https://webpack.js.org/guides/code-splitting/),了解更多相关信息。您可能还想检查 React 的`lazy` [api](https://reactjs.org/docs/code-splitting.html#reactlazy) ，这有助于将动态导入渲染为常规组件。

如果你想在一个服务器渲染的应用中进行代码分割，检查[可加载组件](https://github.com/gregberge/loadable-components)。

# 树摇晃

摇树意味着从你的脚本中删除死代码。它提出了 ECMAScript 2015 对静态导入和导出的支持。因为这些是静态导入和导出，所以我们可以在编译时从包中移除未使用的导出。

请记住，树抖动发生在编译时，而不是运行时，以防您有一些动态导入(我们不能确定代码的哪一部分将保持不使用)。

要启用树抖动，可以使用`package.json`的`sideEffects`属性。如果在你的模块和代码中没有副作用，将它设置为 false，在这种情况下，webpack 将在编译时从包中删除未使用的代码。

```
{
  "name": "project",
  "sideEffects": false
}
```

`sideEffects`属性也接受一个数组，您可以在其中指定带有副作用的文件的路径，比如样式表或模块。路径可以是相对的、绝对的或全局的模式。

```
{
  "name": "project",
  "sideEffects": [
    "*.css",
    "./src/side-effect-file.js"
  ]
}
```

如果你想指定每个函数的副作用(例如，在一个函数中你可能声明了没有被使用的变量)，你可以检查一个叫做`usedExports`的 webpack 优化。你也可以在函数的顶部添加一个注释`/*#__PURE__*/`。

请注意，webpack 在生产模式下从包中删除了死代码，因此您需要在配置中将`mode`设置为`production`，这也将启用缩小功能。如果你问我关于`development`模式的问题，那么 webpack 只是在未使用的代码上面添加了一个注释，提到这段代码是未使用的，但不会将其从包中移除。

除了`mode`之外，你还可以使用`--optimize-minimize`标志来启用 Terser 插件。这里需要注意的是，ModuleConcatenationPlugin 是在生产模式下自动添加的，因此如果您不使用生产模式，您需要手动添加插件。

一旦您完成了添加 sideEffects 状态，继续，再次运行构建并检查死代码是否从生成的包中删除。

查看 webpack 的摇树指南[此处](https://webpack.js.org/guides/tree-shaking/)了解更多详情。

# 惰性装载

延迟加载意味着延迟图像、视频等资源，直到它们出现在视窗中并被用户实际看到。这减少了不必要的内容权重、所需的处理并节省了内存，使您的页面性能更好。

换句话说，这意味着在实现时，我们将只加载在视口中可见的内容，其余的内容将在它进入视口或与视口有一定距离时加载。

您可以通过以下方式实现延迟加载。

*   交叉点观察器 API:应用最广泛。跨浏览器兼容性可能是一个问题，因为它可能不是所有的浏览器都支持。[查看此处](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)。
*   滚动和调整事件处理程序:性能较低，但浏览器兼容性良好。如果您更关心兼容性，并且可以使用性能较低的方法，那么您可以使用这种方法。
*   原生加载属性:在 Chrome 76 中，图像和 iframe 元素支持一个名为`loading`的属性，可以根据需要延迟或提前加载这些资源。虽然不是所有的浏览器都完全支持，但是如果你想避免编写 JS 或者使用不同的库和 API 来实现延迟加载，用这个实现延迟加载真的很容易。`<img src="image.jpg" alt="Test Image" loading="lazy" />`

请不要忘记增加`<img>`元素的宽度和高度，以避免图片下载时的回流。下一点将让你对此有更深入的了解。

**视频的延迟加载**取决于用户的需求，比其他的需要更多的关注。我认为你正在使用`<video>`元素在你的页面上包含视频，这是大多数时候的情况。

如果您禁用了视频的自动播放，则在用户播放视频之前，您无需预加载视频内容。`<video>`元素带有一个`preload`属性，该属性给浏览器一个提示，告诉目标用户视频内容的行为应该是什么。您应该将其值设置为`none`以避免预加载任何视频数据，并提供一个`poster`属性来添加占位符。

```
<video controls preload="none" poster="video-placeholder.jpg">
  <source src="video.mp4" type="video/mp4">
</video>
```

请记住，这并不强制浏览器遵循您的定义，而是作为浏览器的一个提示。

在 Chrome 中`preload`的默认设置曾经是`auto`，现在从 Chrome 64 开始，是`metadata`。像这样，不同的浏览器在不同的条件下有不同的默认值，例如在数据保护模式下，它被设置为`none`。

现在预加载并不是推迟视频内容加载的唯一方式，点击[这里](https://developers.google.com/web/fundamentals/media/fast-playback-with-video-preload)。
勾选*链接预加载* [此处](https://developers.google.com/web/fundamentals/media/fast-playback-with-video-preload#preload_the_first_segment)仅预加载第一个片段，如果您启用了自动播放，这将有助于后续操作。

*注意:*自动播放优先于预载。

# 指定图像尺寸

重要提示:一定要定义图片的宽度和高度，以使你的页面更有表现力，并有流畅的用户体验。

```
<img src="image.jpg" width="400" height="300" />
```

这通过在下载图像时为页面上的图像保留适当的占位空间来避免布局偏移(jank 问题)。

现在，在响应式网页设计的时代，当你想让你的图片具有响应性时，你就开始使用 css 来调整图片的大小，比如添加

```
.responsive-image {
  width: 100%;
  height: auto;
}
```

在您的样式表和我们的解决方案中，防止 jank 在这里不再工作，也就是说，我们再次开始看到布局的变化，从而多次重画。

最近，Mozilla 想出了一个主意，使用图像元素中的宽度和高度属性来计算其纵横比，从而在图像完全加载之前计算图像尺寸。这可以防止加载图像时不必要的重新布局，并大大提高性能，尤其是在网速极慢的情况下。

该支持最近已于 2019 年 12 月在 Firefox 71 和 Chrome 79 中发货。CSS 属性仍处于试验阶段，以方便进一步扩展对所有元素的支持。

查看[这个](https://www.youtube.com/watch?v=4-d_SoCHeWE)惊人的视频，了解更详细的解释。

# 用视频替换 gif

动画 gif 可以大到几兆字节。幸运的是，我们可以将我们的 GIF 文件转换成瘦的快速视频文件，并使用`<video>`元素来实现 GIF 外观行为，方法是启用自动播放并让它们无声地循环，就像

```
<video autoplay muted loop playsinline>
  <source src="video.mp4" type="video/mp4">
</video>
```

这不仅意味着从你的页面中砍掉兆字节，还意味着减少 CPU 时间(视频比 gif 占用更少的 CPU 时间),这是除了减少文件大小以外改善页面加载时间的一个重要方面。

# 避免文档.写

您应该避免包含使用`document.write`的脚本，因为它们可能会被解析器阻塞。此外，如果在文件加载后运行`document.write`，它将再次清除文件并写入文件，从而影响您的性能。

因此，使用它并不被认为是一个好的做法，从 Chrome 55 继续，任何包含使用`document.write`的脚本都不会被执行(如果在 2G 连接以及其他一些条件下)。

您计划导入的所有第三方脚本可能都不支持异步加载，这将阻止您的页面进一步执行，直到该脚本完全下载并执行，如果该脚本使用其他脚本，可能会导致大量往返网络行程，从而大大降低您的页面速度。

如果您包含一些第三方代码片段，不要忘记使用 async/defer 异步加载它们(请检查哪种方式更适合您)。如果必须选择的话，用`appendChild()`代替`document.write`。

另外，请确保您使用的第三方脚本支持异步加载，或者找到一个支持异步加载的替代方案。

# 移除未使用的 CSS

相信我，这是有帮助的，因为我们总是在没有意识到的情况下，拥有大量未使用的 CSS。

删除所有重复的 css 属性和不必要的覆盖。使用 [css-modules](https://github.com/css-modules/css-modules) 来局部限定 css 选择器的范围。这将让你为每个组件拥有一个单独的 css 模块。因此，当你实现比如说树抖动、代码分割等时，css 模块也将被抖掉，或者与它的作用域组件模块放在一个单独的包中。

这样做将消除未使用的 CSS，从而在更大程度上改善页面加载时间。

需要记住的一点是，CSS 被视为呈现阻塞资源，这意味着在 CSS 对象模型构建完成之前，浏览器不会呈现任何经过处理的内容。使用媒体类型和媒体查询来帮助浏览器将一些 CSS 资源标记为非呈现阻塞。

# 为你的选择器使用以类为中心的方法

降低你使用的 CSS 选择器的复杂性。有时你最终会像这样使用选择器:

```
.list:nth-last-child(n-1) .headline {
  /* styles */
}
```

为了让浏览器知道将这些样式应用于哪个元素，它必须问一个类似于“这是一个具有标题类的元素吗？它的父元素是第 n 个子元素减去一个具有列表类的元素”。根据浏览器的不同，确定这一点可能需要很长时间，因此您应该采用以下方法:

```
.custom-list-headline {
  /* styles */
}
```

请不要介意类名，重点是现在浏览器可以更容易地定位和计算带有 *custom-list-headline* 类的元素的样式。BEM 遵循以类为中心的方法优化这种行为，还有其他有效的方法接近你的选择器。

# 减少回流

重排是一种用户阻止的操作，用于重新计算文档中元素的位置和尺寸，从而重新呈现部分或整个文档。

你会惊讶地发现，我们是多么容易地在文档中完成大量的重排版，例如，仅仅通过改变 css 样式或元素的类，像`:hover`这样的伪类，动画，在 DOM 中添加/删除/更新元素，等等。

这会影响性能，因为更新单个元素会影响它的子元素、祖先元素和兄弟元素，导致花费更多时间来执行回流。这就是为什么总是建议减少 DOM 深度的原因。

还有很多关于 reflow 的内容，如果你感兴趣的话，我在这里找到了一些好的信息[作者 *Lindsey Simon* 和](https://developers.google.com/speed/docs/insights/browser-reflow)作者 *Charis Theodoulou* 。这里有一个提到的 API 列表[在 JS 中被调用时会强制一个回流。](https://gist.github.com/paulirish/5d52fb081b3570c81e3a)

## 减少油漆面积

画图通常是渲染页面管道中运行时间最长的任务，所以我们应该尽量减少画图区域并简化其复杂性。在此检查[层提升](https://developers.google.com/web/fundamentals/performance/rendering/simplify-paint-complexity-and-reduce-paint-areas#promote_elements_that_move_or_fade)以减少油漆区域。

# 滑块

在为你的网站选择滑块库的时候一定要小心，因为它们真的会降低你的页面速度，例如`react-slick`会导致你的布局产生昂贵的强制回流。

我会推荐使用`[Glide.js](https://www.npmjs.com/package/@glidejs/glide)`，因为它重量轻，速度快，到目前为止还没有给我们带来任何性能问题。

# 分析和优化您的产品包

使用类似`[webpack-bundle-analyzer](https://www.npmjs.com/package/webpack-bundle-analyzer)`的包分析器对您的包进行可视化分析，并移除不再需要的包。检查所用包的大小，如果可能的话，使用轻量级的替代品。

在你的应用程序中运行`npm dedupe`来删除 npm 包的副本(同一个包的不同版本)并优化你的包树。

此外，迁移到最新版本的包，因为它们本身经过了一些性能优化，可能会更有效。

# 导入特定模块，而不是整个库

只导入您需要的模块，而不是导入整个库。例如:

跟随

```
import get from “lodash/get”;
```

代替

```
import { get } from “lodash”;
```

在第一种情况下，您只导入了模块(~2kB)，而在第二种情况下，您最终导入了整个库(~25kB)。

# 限制每个函数的行数

您应该遵循编写较小函数的习惯，限制自己每个函数不超过一定的行数。

如果你实现了树抖动，除了它的其他好处之外，用这种方式编写的函数将有助于消除每一小段无用的代码。

# 贮藏

缓存你的代码以最小化网络行程，你可以实现 HTTP 缓存，服务工作者缓存，如果使用 webpack，那么检查文件名散列，cdn 等。

首先，你可以使用 HTTP 缓存，因为它很容易实现，虽然没有太大的灵活性，但它是有效的，并且在所有浏览器中都受支持。

缓存本身是一个广阔的领域，我不能在这里告诉你所有的内容，但是将来我一定会告诉你，包括其他几种缓存类型的细节，比如数据库缓存。

# 优化广告加载

如果你在页面上显示广告，你需要非常小心你是如何加载它们的。

总是异步加载你的广告，或者在你的页面加载后加载，因为它们会长时间阻塞你的主线程，影响你的页面性能。

# 小心处理 target="_blank "

最后但同样重要的是，看看这个[博客](https://medium.com/@iamgarima/performance-security-and-target-blank-7f05956d9eb5)，我已经解释了在锚标签中使用`target`属性时的一点疏忽会如何影响你的应用程序的性能和安全性。

*我已经试着把我在研究过程中发现的所有东西和帮助我优化页面速度的东西都包括进去了。希望你也觉得有用。您可以随时深入这些主题，了解更多关于它们的实现，因为现在您知道要寻找什么了。*

*如果你有更多关于如何优化页面速度的建议，以及什么帮助你改善了页面加载时间，我会很乐意知道的。请写在下面的回应，或者你可以直接 ping 我。*