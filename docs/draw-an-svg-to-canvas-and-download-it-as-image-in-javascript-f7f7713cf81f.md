# 在画布上绘制一个 SVG，并将其作为图像下载到 JavaScript 中

> 原文：<https://levelup.gitconnected.com/draw-an-svg-to-canvas-and-download-it-as-image-in-javascript-f7f7713cf81f>

![](img/d7ca1e4ad17fb9ccd42f8703c1df9085.png)

约翰·施诺布里奇在 [Unsplash](https://unsplash.com/s/photos/computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 你会学到什么📖？

*   **在画布上绘制一个 SVG**
*   **将画布下载为图像(jpeg || png|| webp)**
*   **选择图像质量**
*   **下载多个 zip 文件**

# 在画布上绘制一个 SVG🎨。

假设我们有一个 id 为`**svg_element**`的 SVG 元素。我们会改变信仰

> SVG →图像→画布

将 SVG 绘制到画布的步骤:

*   找到一个 SVG 的宽度和高度
*   克隆 SVG 节点
*   从 SVG 创建一个 blob 对象
*   为 blob 创建一个 URL
*   将 URL 加载到图像元素中
*   用 SVG 的`width`和`height`创建一个画布
*   将图像绘制到画布上

🔷为了找到 SVG 的宽度和高度，我们可以使用`svgElement.getBBox()`。这个方法将返回一个带有`left , top, width, height`值的对象。换句话说，`svg`元素的边界框。

```
var svgElement = document.getElementById('svg_element');let {width, height} = **svgElement.getBBox()**; 
```

🔷要克隆一个节点，我们可以使用`cloneNode`方法:

```
let clonedSvgElement = svgElement.cloneNode(true);
// true for deep clone
```

🔷现在我们需要从克隆的节点创建一个 blob 对象:

```
let outerHTML = clonedSvgElement.outerHTML, blob = **new Blob([outerHTML],{type:'image/svg+xml;charset=utf-8'})**;
```

🔷要从 blob 对象创建 URL:

```
let URL = window.URL || window.webkitURL || window;let blobURL = URL.createObjectURL(blob);
```

🔷我们有了 blob 的 URL，现在我们需要将`blobURL`加载到图像元素中。此外，我们需要向图像元素添加`onload`事件。一旦图像被加载，这将被触发。一旦图像被加载，我们就可以将图像绘制到画布上。

```
let image = new Image();image.onload = () => {

   let canvas = document.createElement('canvas');

   canvas.widht = width;

   canvas.height = height; let context = canvas.getContext('2d'); // draw image in canvas starting left-0 , top - 0 **context.drawImage(image, 0, 0, width, height );** *//  downloadImage(canvas); need to implement*};image.src = blobURL;
```

现在我们已经将图像绘制到画布上，让我们下载画布

# **将画布下载为图像(png || webp || jpeg)**

我们有一个画布，我们需要将它转换成 png 或 jpg。为此，我们将把画布转换成数据 URL。

```
let png = canvas.toDataURL(); // default pnglet jpeg = canvas.toDataURL('image/jpg');let webp = canvas.toDataURL('image/webp');
```

为了下载图像，我们将编写一个函数来创建一个`link`元素，并用`href`指向从图像创建的 DataURL。

```
var download = function(href, name){
  var link = document.createElement('a');
  link.download = name;
  link.style.opacity = "0";
  document.append(link);
  link.href = href;
  link.click();
  link.remove();
}download(png, "image.png");
```

# **选择🖼.图像的质量**

我们还可以为`jpeg and webp`图像设置图像质量。

质量值是一个介于`0`和`1`之间的数字，表示用于使用有损压缩的图像格式(如`image/jpeg`和`image/webp`)的图像质量。默认值为`0.92`。

```
let jpeg = canvas.toDataURL('image/jpg'); // 0.92let webp = canvas.toDataURL('image/webp', 0.5);
```

# **以 zip 🗂.格式下载多个文件**

考虑到我们有多个文件。我们可以将多个文件压缩成一个文件，然后下载。

为了创建一个 zip，我们可以使用 [JSZip 库](https://stuk.github.io/jszip/)。下载`jszip.js`并包含在您的 html 中。

一旦纳入，我们需要:

*   创建一个 JSZip()对象
*   创建文件夹
*   将文件添加到文件夹中
*   下载压缩文件

**创建 JSZip 对象**

```
let jsZip = new JSZip();
```

**创建文件夹**

```
let folder = jsZip.folder("images");
```

**向文件夹添加图像**

我们有两个图像:

```
let jpeg = canvas.toDataURL('image/jpg'); // 0.92let webp = canvas.toDataURL('image/webp', 0.5);
```

这两个图像是 dataURL 格式的，我们只需要传递 base64 字符串，因为我们需要从 DataURL 中分离 base64 字符串。

通常，dataURL 看起来像这样:

```
data:image/png;base64, base64String
```

我们需要从数据 URL 中分离出`data:image/png;base64`:

```
function getBase64String(dataURL) { var idx = dataURL.indexOf('base64,') + 'base64,'.length; return dataURL.substring(idx);}
```

然后，我们需要将 baseString 作为文件添加到文件夹中，因此最终的代码如下所示:

```
// image as dataURL
let jpeg = canvas.toDataURL('image/jpg'); // 0.92
let webp = canvas.toDataURL('image/webp', 0.5);//zip 
let jsZip = new JSZip();
let folder = jsZip.folder("images");let baseString = getBase64String(jpeg);
folder.file("image1.jpeg", baseString, {base64 : true});let baseString2 = getBase64String(webp);
folder.file("image2.webp", baseString2, {base64 : true});
```

生成 zip 文件

```
zip.generateAsync({type:"blob"}).then(function (content) {
      content = URL.createObjectURL(content);
      let name = `JSJeep.zip`;
      download(content, name); // already written above
});
```

这将生成一个 zip 文件并开始下载。

如果你发现任何错误，请让我知道。因为`Code without bug is like a book without title`。

请在 [paypal](https://paypal.me/jagathishSaravanan) 支持我。

跟随 Javascript Jeep🚙💨获取更多 JavaScript 项目。