# 如何在 JavaScript 中旋转图像并保持其纵横比

> 原文：<https://levelup.gitconnected.com/how-to-rotate-an-image-in-javascript-and-keep-its-aspect-ratio-e42060190889>

## 以及如何在 React 钩子中实现？

![](img/c6c36835cab1fbdc9dfac2256b6072b1.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Karsten Würth](https://unsplash.com/@karsten_wuerth?utm_source=medium&utm_medium=referral) 拍摄

在 web 开发中，您可能需要旋转图像，这在 CSS 中很容易做到。像这样简单的代码`transform: rotate(90deg);`。但是如果我们想用 JS 来做呢？

# TLDR

在浏览器环境中将图像绘制到画布上并旋转它。但在此之前，我们需要做一些数学运算来保持原始图像的纵横比。

# 核心

假设我们已经加载了图像，可以按如下方式计算旋转后的图像:

```
const { PI, sin, cos, abs } = Math;
const angle = (degree * PI) / 180;
const sinAngle = sin(angle);
const cosAngle = cos(angle);

const rotatedWidth = abs(imageWidth * cosAngle) + abs(imageHeight * sinAngle);
const rotatedHeight = abs(imageWidth * sinAngle) + abs(imageHeight * cosAngle);
```

接下来，我们使用一些 canvas APIs 来进行实际的旋转:

```
const canvas = document.createElement('canvas');

const { width: canvasWidth, height: canvasHeight } = canvas;
const canvasCtx2D = canvas.getContext('2d');

canvasCtx2D.clearRect(0, 0, canvasWidth, canvasHeight);
canvasCtx2D.translate(canvasWidth / 2, canvasHeight / 2);
canvasCtx2D.rotate(angle);

canvasCtx2D.drawImage(
  image,
  -imageWidth / 2,
  -imageHeight / 2,
  imageWidth,
  imageHeight,
);

return canvas.toDataURL('image/png');
```

# 包裹

有了核心代码，我们可以进行一些优化，并编写专用的 React 挂钩来使用它:

这里我重用了同一个 canvas 元素，以减少重复创建。其次，需要注意的是，我在每次旋转后都将其宽度和高度设置为`0`，以减少内存使用。对了，我还做了清画布的操作。这是因为在 [HTML 规范](https://html.spec.whatwg.org/multipage/canvas.html#concept-canvas-set-bitmap-dimensions)中当你修改画布的宽度和高度(是否和之前一样)会清空画布，这和`canvasCtx2D.clearRect(0, 0, canvasWidth, canvasHeight)`是一样的，现代浏览器都支持。

在`useRotateImage`中，我保留了一个对`image`元素的引用，并在`[image.decode()](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement/decode)`后设置旋转后的图像状态，这在图像数据准备就绪后解决。

下面是一个使用案例:

*感谢阅读。如果你喜欢这样的故事，想支持我，请考虑成为* [*中会员*](https://medium.com/@islizeqiang/membership) *。它每月收费 5 美元，可以无限制地访问媒体内容。如果你通过* [*我的链接*](https://medium.com/@islizeqiang/membership) *报名，我会得到一点佣金。*

你的支持对我来说很重要——谢谢。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)