# 探索 JavaScript 窗口对象:documentURI、嵌入和字体

> 原文：<https://levelup.gitconnected.com/introducing-the-javascript-window-object-document-embeds-and-fonts-5cae7083aec0>

![](img/fb0af901025c78da88e0960d58a6daf9.png)

照片由 [Alexandre Chambon](https://unsplash.com/@goodspleen?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

`window`对象是一个全局对象，具有与当前 DOM 文档相关的属性，即浏览器选项卡中的内容。

在本文中，我们将了解一些`window.document`对象以及如何使用它们，包括`documentURI`、`embeds`和`fonts`属性。

# window.document.documentURI

`documentURI`是一个返回文档位置的字符串属性。它最初是一个读/写属性，但现在是只读的。例如，如果我们有:

```
console.log(document.documentURI);
```

我们会得到这样的结果:

```
"[https://developer.mozilla.org/en-US/docs/Web/API/Document/documentURI](https://developer.mozilla.org/en-US/docs/Web/API/Document/documentURI)"
```

从`console.log`输出。

# window.document.embeds

`embeds`属性是一个只读属性，它返回文档中嵌入的`object`元素的列表。例如，如果我们有下面的`embed`元素来添加一个视频到我们的网页:

```
<embed type="video/webm" src="[https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4](https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4)" width="250" height="200">
```

然后在脚本文件中，我们可以使用编写以下代码来获得我们的 embed 元素:

```
console.log(document.embeds);
```

然后我们得到一个类似 HTMLCollection 数组的对象，它包含我们视频的`embed`元素。HTMLCollection 对象有一个`item`方法，让我们通过索引获取 HTMLCollection 对象中的 DOM 元素。此外，由于它是一个类似数组的对象，我们可以像下面的代码一样将它与`for...of`循环一起使用:

```
for (const el of document.embeds) {
  console.log(el);
}
```

如果我们运行上面的代码，我们将得到网页中的`embed`元素。我们还可以获得`embed`元素的属性，如以下代码所示:

```
for (const el of document.embeds) {
  const {
    height,
    src,
    type,
    width
  } = el;
  console.log(height, src, type, width);
}
```

我们应该得到这样的结果:

```
200 [https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4](https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4) video/webm 250
```

如果我们有上面的`embed`元素。这些值与我们传入的属性值相同。我们可以用 HTMLCollection 对象可用的`item`方法通过索引获取元素来替换`for...of`:

```
console.log(document.embeds);
for (let i = 0; i < document.embeds.length; i++) {
  const {
    height,
    src,
    type,
    width
  } = document.embeds.item(i);
  console.log(height, src, type, width);
}
```

![](img/e9510a817f758a4693000f1714060d73.png)

[法比奥·桑塔尼耶洛·布鲁恩](https://unsplash.com/@fabiosbruun?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 窗口.文档.字体

属性返回一个有各种属性和事件的对象，让我们处理字体的加载。例如，如果我们有以下代码:

```
console.log(document.fonts);
```

我们得到了`FontFaceSet`对象。`FontFaceSet`对象具有以下属性。它有`status`只读属性，这是一个字符串，表示文档字体的加载状态。该值可以是`'loading'`或`'loaded'`。

`FontFaceSet`的另一个属性是`ready`只读属性，这是一个承诺，一旦字体被加载并且布局操作完成，这个承诺就解决了。例如，一旦所有字体都已加载，我们可以编写以下内容来记录“加载的字体”:

```
(async () => {
  await document.fonts.ready;
  console.log('fonts loaded');
})()
```

`document.fonts`属性也有 3 个事件——`onloading`事件、`onloadingdone`事件和`onloadingerror`事件。当 loading 事件被触发时，调用`onloading`事件处理程序，表示字体已经开始加载。

每当触发类型为`loadingdone`的事件时，就会调用`onloadingdone`事件处理程序，这意味着字体集已经成功加载。每当触发`loadingerror`类型时，就会调用`onloadingerror`事件处理程序，这表明加载字体集时发生了错误。

例如，我们可以使用如下代码中的事件处理程序:

```
document.fonts.onloading = () => {
  console.log('Font is loading');
}document.fonts.onloadingdone = () => {
  console.log('Font successfully loaded');
}document.fonts.onloadingerror = () => {
  console.log('Error loading font');
}(async () => {
  await document.fonts.load("12px Roboto", "A");
})();
```

在 HTML 文件中，我们添加:

```
<link href='[https://fonts.googleapis.com/css?family=Roboto:400,700'](https://fonts.googleapis.com/css?family=Roboto:400,700') rel='stylesheet' type='text/css'>
```

然后，当我们运行代码时，我们应该记录“字体正在加载”和“字体成功加载”,因为我们从 Google 字体 CDN 加载了字体，首先用`link`标签将字体包含在我们的网页中，然后我们将事件侦听器附加到字体加载事件。

最后，我们运行了加载 Roboto 字体的`document.fonts.load`方法，这将触发`onloading`事件和`onloadingdone`事件处理程序，因为我们已经从 Google CDN 加载了字体，应该可以加载了。

我们上面使用的`load`方法返回一个承诺，并接受 2 个字符串参数。第一个是字符串，它使用 CSS value 语法表示字体大小和名称，如上所述。

第二个参数是文本，它将字体加载限制为上面字符串中的字符。该范围应该是文本中的一个字符。第二个参数是可选的。

除了`load`方法之外，`document.fonts`对象中还有一些方法。它包括向字体集添加字体的`add`方法。

它需要一个参数，即`FontFace`对象。我们通过使用带 2 个参数的`FontFace`构造函数创建了`FontFace`对象。

第一个参数是字体名称的字符串，第二个参数是字体的 URL，应该像 CSS 代码中的 URL 一样表示。

例如，要用`add`方法给我们的文档添加一种字体，我们可以写:

```
const fontFace = new FontFace('Bitter', 'url([https://fonts.gstatic.com/s/bitter/v7/HEpP8tJXlWaYHimsnXgfCOvvDin1pK8aKteLpeZ5c0A.woff2)'](https://fonts.gstatic.com/s/bitter/v7/HEpP8tJXlWaYHimsnXgfCOvvDin1pK8aKteLpeZ5c0A.woff2)'));
document.fonts.add(fontFace);
```

作为`loaded`属性的`FontFace`对象，它返回一个承诺，当在`FontFace`构造函数中指定的字体被加载时，该承诺被解析。例如，我们可以写:

```
const fontFace = new FontFace('Bitter', 'url([https://fonts.gstatic.com/s/bitter/v7/HEpP8tJXlWaYHimsnXgfCOvvDin1pK8aKteLpeZ5c0A.woff2)'](https://fonts.gstatic.com/s/bitter/v7/HEpP8tJXlWaYHimsnXgfCOvvDin1pK8aKteLpeZ5c0A.woff2)'));
document.fonts.add(fontFace);
(async () => {
  await document.fonts.load("12px Bitter", "A");
  const loadedFontFace = await fontFace.loaded;
  console.log(loadedFontFace);
})()
```

在上面的代码中，我们使用`add`方法添加了 Bitter 字体，并使用`load`方法加载新添加的字体。最后，我们记录了字体，我们得到了`FontFace`对象的属性，它应该有如下输出:

```
display: "auto"
family: "Bitter"
featureSettings: "normal"
loaded: Promise {<resolved>: FontFace}
status: "loaded"
stretch: "normal"
style: "normal"
unicodeRange: "U+0-10FFFF"
variant: "normal"
weight: "normal"
```

要检查一个字体是否已经被加载并且可用，我们可以使用`check`方法，该方法返回一个布尔值，并接受 2 个参数。

第一个是带有字体名称的字符串，其属性采用 CSS value 语法。

第二个参数是文本，它将字体加载限制为上面字符串中的字符。

该范围应该是文本中的一个字符。第二个参数是可选的。例如，我们可以在下面的代码中使用它:

```
console.log(document.fonts.check("12px Roboto", "a"));
console.log(document.fonts.check("12px Arial", "a"));
```

第一个`console.log`应该输出`false`，因为我们没有通过编写任何代码来加载 Roboto 字体。第二个`console.log`应该是`true`，因为它是一个无需添加任何代码就可以从外部加载的字体。

`clear`方法从字体集中删除所有字体。这不需要争论。例如，我们可以在下面的代码中使用它:

```
const fontFace = new FontFace('Bitter', 'url([https://fonts.gstatic.com/s/bitter/v7/HEpP8tJXlWaYHimsnXgfCOvvDin1pK8aKteLpeZ5c0A.woff2)'](https://fonts.gstatic.com/s/bitter/v7/HEpP8tJXlWaYHimsnXgfCOvvDin1pK8aKteLpeZ5c0A.woff2)'));
document.fonts.add(fontFace);
(async () => {
  await document.fonts.load("12px Bitter", "A");
  console.log(document.fonts.check("12px Bitter", "A"));
  const loadedFontFace = await fontFace.loaded;
  document.fonts.clear();
  console.log(document.fonts.check("12px Bitter", "A"));
})()
```

在上面的代码中，我们首先用`load`方法加载字体，然后用`clear`方法卸载它，所以第一个`console.log`应该记录`true`，因为用`load`方法加载了字体，第二个应该记录`false`，因为我们调用了`clear`方法卸载它。

最后，还有从字体集中删除字体的`delete`方法。它将一个`FontFace`对象作为唯一的参数。例如，我们可以在下面的代码中使用它:

```
const fontFace = new FontFace('Bitter', 'url([https://fonts.gstatic.com/s/bitter/v7/HEpP8tJXlWaYHimsnXgfCOvvDin1pK8aKteLpeZ5c0A.woff2)'](https://fonts.gstatic.com/s/bitter/v7/HEpP8tJXlWaYHimsnXgfCOvvDin1pK8aKteLpeZ5c0A.woff2)'));
document.fonts.add(fontFace);
(async () => {
  await document.fonts.load("12px Bitter", "A");
  console.log(document.fonts.check("12px Bitter", "A"));
  const loadedFontFace = await fontFace.loaded;
  document.fonts.delete(loadedFontFace);
  console.log(document.fonts.check("12px Bitter", "A"));
})()
```

在上面的代码中，我们首先用`load`方法加载字体，然后用`delete`方法卸载它，并传入了`loadedFontFace`，因此第一个`console.log`应该记录`true`，因为用`load`方法加载了字体，第二个应该记录`false`，因为我们调用了`delete`方法，并传入了`loadedFontFace`来卸载它。

我们从本系列的第 5 部分继续，查看了`window.document`对象的更多属性，包括`documentURI`、`embeds`和`fonts`属性。属性为我们获取当前加载的文档的 URL。

属性获取文档中所有的 DOM 元素，并让我们看到属性。属性有很多属性可以让我们得到我们加载的字体的状态，它也有一些方法可以让我们根据需要加载和卸载字体。

它还可以让我们检查您想要加载的字体的状态。对于加载通常不内置于操作系统中的自定义字体，它非常方便，浏览器无需加载即可使用。