# 在浏览器中使用 JavaScript 模块

> 原文：<https://levelup.gitconnected.com/using-javascript-modules-in-the-browser-2c55bfc5f5e9>

![](img/27e41fa3f228d388335b588097657576.png)

由 [Samuel Sng](https://unsplash.com/@samuelsngx?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

从 ES6 开始，浏览器模块被推向世界。这比使用 script 标签加载代码要好得多，因为它是异步的，并且不依赖于它们的添加顺序。

因此，用模块来引用和组织代码要容易得多。

它可以在大多数现代浏览器中使用，将代码与 Webpack 等程序捆绑在一起。

在本文中，我们将看看如何使用它们来编写浏览器代码。

# 在浏览器中使用模块

Safari、Chrome、Firefox 和 Edge 都支持在浏览器中使用 ES6 模块。

它的工作原理是浏览器通过发出 GET 请求来下载模块。

它是异步完成的，所以不会阻止其他东西的加载。

我们可以通过定义模块并使用脚本标签来加载它，从而在代码中加载模块。

例如，我们可以如下定义一个名为`hello.js`的模块:

```
export const hello = "Hello";
```

然后我们可以定义另一个叫做`index.js`的模块如下:

```
import { hello } from "./hello";
document.querySelector("#app").innerHTML = hello;
```

然后我们可以如下加载它们:

```
<script type="module" src="src/hello.js"></script>
<script type="module" src="src/index.js"></script>
```

我们接着补充:

```
<div id="app"></div>
```

我们应该会在屏幕上看到`Hello`。

我们可以如下翻转它们:

```
<script type="module" src="src/index.js"></script>
<script type="module" src="src/hello.js"></script>
```

和`Hello`仍然会显示，因为它们是由另一个模块导入时加载的。

我们也可以将其内联定义如下:

```
<div id="app"></div><script type="module">
  import { hello } from "./src/hello.js";
  document.querySelector("#app").innerHTML = hello;
</script>
```

默认导出也可以。例如，如果`hello.js`有:

```
const hello = "Hello";
export default hello;
```

那么如果`index.js`和`hello.js`在同一个文件夹里，我们可以写:

```
import hello from "./hello.js";
document.querySelector("#app").innerHTML = hello;
```

来导入它。

# 重复的脚本标记

重复的模块`script`标签被忽略。他们被视为一体。所以:

```
<script type="module" src="./src/hello.js"></script>
<script type="module" src="./src/hello.js"></script>
<script type="module" src="./src/index.js"></script>
```

与以下内容相同:

```
<script type="module" src="./src/hello.js"></script>
<script type="module" src="./src/index.js"></script>
```

这是因为模块只有在被导入的时候才会被加载。

![](img/fca0a2358ed3c4a986610bc7b16fb654.png)

照片由 [Lydia Torrey](https://unsplash.com/@soulsaperture?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 动态导入

我们可以用`import`函数动态导入一个模块。它接受一个字符串，该字符串包含模块相对于当前模块的路径。

例如，如果我们想导入`hello.js`，它有:

```
export const hello = "Hello";
```

从与`hello.js`相同的文件夹中的`index.js`，我们可以编写:

```
(async () => {
  const helloModule = await import("./hello.js");
  document.querySelector("#app").innerHTML = helloModule.hello;
})();
```

`import`函数返回一个承诺，因为它是异步的。它将解析为一个带有导出的对象。

那么我们应该再次得到`Hello`，因为我们已经添加了 ID 为`app`的元素。

可以使用`default`属性检索默认导出。所以如果我们把`hello.js`改成:

```
const hello = "Hello";
export default hello;
```

那么我们必须将`index.js`改为:

```
(async () => {
  const helloModule = await import("./hello.js");
  document.querySelector("#app").innerHTML = helloModule.default;
})();
```

从`hello.js`获取默认导出。

# 不支持模块的浏览器的回退

我们可以给`script`标签添加一个`nomodule`属性，以包含一个在浏览器不支持模块加载页面时运行的脚本。

例如，我们可以创建`fallback.js`并添加:

```
document.querySelector("#app").innerHTML = 'Browser doesn\'t support modules.';
```

然后我们可以写:

```
<div id="app"></div><script type="module">
  import { hello } from "./src/hello.js";
  document.querySelector("#app").innerHTML = hello;
</script>
<script nomodule src="./src/fallback.js"></script>
```

当我们使用不支持 ES6 模块的现成浏览器(如 Internet Explorer)时，我们会得到:

```
Browser doesn't support modules.
```

# 第三方库

一些第三方库可以作为一个模块从 CDN 加载。

例如，我们可以像这样加载 Lodash 库。我们可以如下加载它:

```
<script type="module">
  import _ from '[https://unpkg.com/lodash-es'](https://unpkg.com/lodash-es')
</script>
```

然而，我们可能不应该这样做，因为它将库的每个部分都作为一个请求加载。

这意味着加载一个库需要数百个请求。

加载 Lodash 需要 600 多个请求。

# 和睦相处

ES6 模块无需开箱即可在大多数现代浏览器上使用。最新版本的 Chrome、Firefox、Edge、Safari 以及它们的移动版本都支持开箱即用。

除了 Internet Explorer，大多数流行的浏览器都可以自己加载 ES6 模块。

# 结论

ES6 模块支持现已内置于大多数现代浏览器中。

我们可以用它们来定义我们自己的模块，并随意加载它们。它们的加载顺序并不重要。它们会在被某个东西导入的时候被加载。

动态导入可以用`import`函数来完成，它接受一个字符串，该字符串包含我们想要导入的模块的相对路径。它返回一个承诺，所以我们必须用`async`和`await`或`then`获得值。