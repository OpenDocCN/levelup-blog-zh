# 如何使用 JavaScript 创建圆形包装图

> 原文：<https://levelup.gitconnected.com/how-to-create-a-circle-packing-chart-using-javascript-6771648882cc>

## 一个清晰的分步指南，用于构建一个交互式 JS Circle 包装图表，以可视化 Spotify 上最热门的 100 首歌曲。

![](img/6bb1f3747c1089841c009e5773a6b769.png)

作者图片

想学习如何以令人惊叹的圆形包装图可视化分层数据，并以轻松的方式添加到网页或应用程序中吗？不要感到不知所措，只要按照我的逐步数据可视化教程！使用 Spotify 上最热门的 100 首歌曲的数据，我将向您展示如何使用 JavaScript 轻松创建和定制一个漂亮的交互式圆形包装图。

# 什么是圆形包装图

让我先给你简单介绍一下什么是圆形包装图，以及如何使用它。

也称为圆形树，圆形包装图基本上是展示分层数据的树形图，其中圆形表示节点，子节点是节点圆形内的圆形。

圆圈的大小表示节点的值。

圆形包装表示法很好地表示了层次结构，通过组和子组提供了方便的可视化分解。

# 预览将要制作的圆形包装图

看看我将要构建的东西——最终的 JS circle 包装图将在本教程结束时创建——并加入这场音乐之旅！

![](img/1354f41a7931517153fe8555359fb9eb.png)

作者图片

# 用 4 个简单的步骤构建 JS 圆形包装图

一个可嵌入的圆形包装图看起来令人兴奋，但很难创建。然而，有许多专门用来帮助每个人构建各种数据可视化的 JavaScript 图表库。一旦你找到了一个开箱即用的内置圆形打包选项，即使你是一个编程技能有限的初学者，通常也能非常快速和直接地得到这样一个交互式图表。

从技术上讲，创建圆形包装数据可视化的整个过程所包含的步骤与任何 JS 图表库大致相同。在本教程中，我将使用 [AnyChart](https://www.anychart.com/) 进行说明。这很容易开始，详细的[文档](https://docs.anychart.com/)和许多现成的[示例](https://www.anychart.com/products/anychart/gallery/)可以作为快速构建任何类型图表的良好起点，包括这张。同样重要的是，该库对于非商业用途是免费的。

所以，创建一个 JS 四号圆形包装图的基本步骤如下:

1.  为图表制作一个 HTML 页面。
2.  包括必要的 JS 文件。
3.  添加数据。
4.  编写绘制图表所需的 JavaScript 代码。

## 1.创建 HTML 页面

我做的第一件事是创建一个放置图表的基本 HTML 页面。接下来，我创建一个 HTML 块元素，`div`,并给它分配一个 ID 属性，如“container ”,以便在代码中容易地识别它。

我定义了块的样式，通过给高度和宽度属性赋予 100%的值，使图表呈现在整个页面上。当然，您可以随意指定您想要完成任务的方式。

## 2.包括必要的 JavaScript 文件

然后，我需要添加我要用来创建我想要开发的圆形包装图的脚本。通常可以从您正在使用的库的 CDN 中引用必要的文件，或者将它们下载到您的本地机器上。

为了在本教程中创建这个图表，我使用了 AnyChart 库。它有一个模块化的结构，可以很容易地只连接您当前需要的图表类型和特性，从而减少运行 JavaScript 代码的大小。在这种情况下，我需要[核心](https://docs.anychart.com/Quick_Start/Modules#core)模块和特定的[圆形包装](https://docs.anychart.com/Quick_Start/Modules#circle_packing)模块。所以我在第一步中创建的 HTML 页面的`head`部分包含了这两者。

## 3.添加数据

我决定使用来自 [Kaggle](https://www.kaggle.com/pavan9065/top-100-most-streamed-songs-on-spotify) 的专用数据集来可视化 Spotify 上 100 首最热门的歌曲。我稍微修改了一下数据，使它看起来像我需要的那样，并把它保存在一个 [JSON 文件中。](https://gist.githubusercontent.com/shacheeswadia/17dc3b3d4ac9b63ac5ac6833944f3a94/raw/07c4bec103d22ec2824453a33d41868fd476db3d/dataPackedCircles.json)

为了从 JSON 文件加载数据，我将利用一个叫做[数据适配器](https://docs.anychart.com/Quick_Start/Modules#data_adapter)的便利模块。所以我将它包含在`head`部分的引用脚本列表中，并使用`anychart.data.loadJsonFile`函数将这个数据文件添加到代码中。

现在所有的准备工作都完成了，让我们进入创建这个令人印象深刻的、交互式的、基于 JS 的包装圆图的最后一步！

## 4.为您的图表编写 JavaScript 代码

当创建可视化和编写看似复杂的代码时，一些像 HTML 和 JavaScript 这样的 web 开发技术的背景知识总是一个优势。然而，用这种方式创建一个圆形包装图只需要 6 到 7 行代码。所以这一点也不复杂。你不觉得那是音乐吗？

最初，我添加了一个包含所有代码的函数，确保一旦页面准备好就执行它。然后我把数据包含在这个函数里。

现在，我用 data 参数定义一个函数，并使用 data.tree 函数映射数据。我将映射的数据添加到 circlePacking 函数中。

最后，我还做了一个标题，添加了对之前定义的容器的引用，并画出了结果的圆形装箱图。

Tada！一个功能齐全的圆形包装图就这么不费吹灰之力就搭建好了！

![](img/70f1f2acd6af1a5cd703d5da92a654f7.png)

作者图片

在 Spotify 的 100 首最热门的流媒体歌曲中，流行音乐显然是最受欢迎的。我本人更倾向于舞蹈流派，而你可能是摇滚或节奏布鲁斯的粉丝。但不出所料，流行音乐和嘻哈音乐绝对是大众的最爱。

你可以在 [CodePen](https://codepen.io/shacheeswadia/pen/ExwXmOd?editors=0010) 上找到这个基本 JavaScript 打包圆图的完整代码。

# 定制 JS 圆形包装图

这个基于 JavaScript 的圆形包装图的初始版本看起来非常漂亮，可以马上使用。但是我还想向您展示一些简单的方法，使这种类型的数据可视化更加令人印象深刻、时尚和强大。

## **颜色修改**

为了给图表添加更多的爵士乐，我改变了气泡的颜色。使用 AnyChart 的一个预先构建的设计主题可以非常容易地做到这一点。我选择“黑暗魅力”主题，所以我添加它的脚本并在代码中设置主题。

我想给圆形一个透明的背景效果，所以我把圆形的背景和填充设置为相同的颜色。我将圆形的笔画加粗，并将笔画的颜色设置为主题颜色。

## **标题改进**

为了使标题更加突出，我使用 HTML 来指定字体大小和颜色。我还加了第二行做字幕。

点击这里查看 JS 打包圆图表[的定制版本。](https://codepen.io/shacheeswadia/pen/MWEomxQ)

## **工具提示格式**

使交互式数据可视化更具信息性的最好方法之一是在工具提示中添加更多的细节。该数据集包含关于每首歌曲的艺术家和年份的信息，以及其流行度值。因此，我使用 HTML 将所有这些信息包含在圆形包装图的工具提示中。

仅此而已！一个优雅而富有启发性的 JavaScript Circle Packing Chart 被设计出来并准备好了。

![](img/fa9327bd91df74a830e0d7927b125d85.png)

作者图片

使用 [CodePen 上的所有定制检查整个代码。](https://codepen.io/shacheeswadia/pen/rNGwwYb)

# 结论

你可以看到构建一个像包装好的圆圈这样有创意的图表是多么简单。你可以学习使用各种 [JS 图表库](https://en.wikipedia.org/wiki/Comparison_of_JavaScript_charting_libraries)创建大量不同类型的数据可视化。或者从查看 AnyChart 中的其他[图表选项](https://docs.anychart.com/Quick_Start/Supported_Charts_Types)开始。

如果您对构建此图表有任何问题或任何其他疑问，请告诉我。音乐有益于灵魂，创造形象有益于大脑，所以让我们多听些歌，多做些图表吧！