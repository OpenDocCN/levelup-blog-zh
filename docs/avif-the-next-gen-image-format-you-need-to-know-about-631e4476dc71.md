# AVIF——你需要了解的下一代图像格式

> 原文：<https://levelup.gitconnected.com/avif-the-next-gen-image-format-you-need-to-know-about-631e4476dc71>

![](img/33328ff3f452471b0a3d18f7f96bd859.png)

# 什么是 AVIF？

缩写“AVIF”代表“AV1 图像文件格式”，本质上是以 HEIF 文件格式存储用 AV1 压缩的静态和动态图像的规范。AV1 是免版税的视频编码格式，大多数技术专家认为它是媒体压缩的下一步。

基于 AV1 视频格式的现代图像格式。AVIF 通常比 WebP、JPEG、PNG 和 GIF 有更好的压缩效果，并被设计用来取代它们(我将在后面详细介绍)。这是一种图像文件格式规范，用于以 HEIF 文件格式存储用 AV1 压缩的图像或图像序列。它与 HEIC 竞争，后者使用相同的容器格式，构建于 ISOBMFF 之上，但压缩采用 HEVC。AVIF 规范 1.0.0 版于 2019 年 2 月定稿。

# 大型科技公司正在大规模测试 AVIF

2018 年 12 月 14 日，网飞发表了第一份。avif 样本图像，并在 VLC 增加了支持。微软宣布支持 Windows 10“19 h1”预览版，包括对文件资源管理器、画图和多个 API 的支持，以及示例图像。Paint.net 在 2019 年 9 月添加了对打开 AVIF 文件的支持，并在 2020 年 8 月的更新中添加了保存 AVIF 格式图像的能力。Colorist format conversion 和 Darktable RAW image data 都发布了对 libavif 的支持，并提供了 libavif 的参考实现，还开发了一个 GIMP 插件实现，支持 3.x 和 2.10.x 插件 API。

# 网飞的例子

网飞描述了他们对 JPEG 替代品的需求:

1.  得到广泛支持
2.  具有更好的压缩效率
3.  具有更广泛的功能集。>“我们相信 AV1 图像文件格式(AVIF)有潜力。使用我们开源的框架，可以在工作中看到 AVIF 压缩效率，并与之前的一系列图像编解码器进行比较。”

网飞的完整故事，有大量的视觉例子和比较:[https://netflixtechblog . com/avif-for-next-generation-image-coding-b1d 75675 Fe 4](https://netflixtechblog.com/avif-for-next-generation-image-coding-b1d75675fe4)

# 支持的功能

*   高动态范围(HDR)
*   8、10、12 位颜色深度
*   无损压缩和有损压缩
*   单色(alpha/深度)或多分量
*   任何色彩空间，包括宽色域、ISO/IEC CICP 和 ICC 配置文件
*   4:2:0、4:2:2、4:4:4 色度子采样
*   胶片颗粒

# 图形示例和比较:

*   [AVIF 由](https://jakearchibald.com/2020/avif-has-landed/)[杰克·阿奇博尔德](https://twitter.com/jaffathecake)降落
*   [AVIF 图像格式——下一代压缩编解码器](https://www.lambdatest.com/blog/avif-image-format/)

# AVIF vs WebP

总的来说，在过去的一年里，围绕这个话题有很多争论。从去年的测试来看，与其他格式相比，WebP 的表现似乎更好。然而，今年经过一些优化后，WebP 可能会落后。

虽然我不认为 WebP 会很快消失，但开始学习使用 AVIF 可能是个好主意。像网飞、谷歌和微软这样的大玩家已经开始实施他们的一些服务了。这让我相信，在 2020 年和 2021 年，我们将能够在野外看到更多的 AVIF。

如需进一步的技术比较，请查看这篇文章。

# 支持的浏览器:

web 浏览器中的 AVIF 支持正在开发中。2020 年 8 月，谷歌 Chrome 版发布，全面支持 AVIF。Mozilla 正在努力支持 Firefox 中的图像格式。

已经支持 AVIF 的浏览器和具体版本的全面回顾:【https://caniuse.com/avif】T2

# 你自己试试

*   该页面收集了一些已知的支持 AVIF 的实现，并提供了一些测试文件。【https://github.com/AOMediaCodec/av1-avif/wiki 
*   如何使用 AVIF 的一个又酷又简单的指南:[https://reachlightspeed . com/blog/using-the-new-high-performance-avif-image-format-on-the-web-today/](https://reachlightspeed.com/blog/using-the-new-high-performance-avif-image-format-on-the-web-today/)

[*daily.dev*](https://api.daily.dev/get?r=gitconnected) *每一个新标签都提供最好的节目新闻。我们将为您排列数百个合格的来源，以便您可以侵入未来。*

[![](img/00f9f55b8c9d6d293076e144c11d6707.png)](https://api.daily.dev/get?r=gitconnected)