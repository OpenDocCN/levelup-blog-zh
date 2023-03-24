# 赋予 CSS 中的颜色函数新的含义

> 原文：<https://levelup.gitconnected.com/giving-new-meanings-to-the-color-functions-in-css-3b62618e9335>

## 一篇关于 CSS 和颜色的半开玩笑的文章

![](img/fc2a4acca94e7b7e629645dbaf686222.png)

Pawel Czerwinski 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

谈到 HTML 和 CSS 中的颜色，大多数人只知道“经典三种”:RGB、Hex(是 RGB 的一种)、HSL。但是在 CSS 中有[多种方式来定义颜色。](https://alvaromontoro.com/blog/67865/the-ultimate-guide-to-css-colors-2020-edition)

这篇文章是对在 CSS 中编写颜色的一些不同方法的半开玩笑的评论。这不是一篇深入的评论，也不那么有趣，但如果它刷新了一些知识(或提供了一个新的)，我会认为它是成功的。

开始吧！先是旧的，然后是新的。

# RGB

你以为 RGB 代表红绿蓝吗？再猜！也许过去是这样，但现在它应该更像是一个递归的缩写: **RGB 变得无趣**。让我们面对现实:RGB 无处不在，每个人都喜欢它，就像香草一样——如果你不爱香草，你就是一个怪物！-.但就像香草一样，是平淡的味道。味道很好，它结合了许多其他不同的口味，但它也很快变老。

RGB 在颜色数量上受到限制，在目前市场上新的超大型超高清晰度显示器上播放效果不如以前的大型 CRT。但它会派上用场，所以了解它是有好处的。

以下是用 RGB 书写颜色的一些方法:

```
.rgb-got-boring {
  color: rgb(255, 255, 255);
  color: rgba(255, 255, 255, 0.5);
  /* RGB's new notation? Interesting... but still boring */
  color: rgb(255 255 255);
  color: rgb(255 255 255 / .5);
}
```

# 十六进制

如上所述， [HEX](https://alvaromontoro.com/blog/67870/hexadecimal-rgb-hex) 是 RGB 的一种，作为 RGB，它变得有点老和无聊。因此，代替“十六进制”的缩写，新的意思应该是“**大喊大叫的 EXes** ”因为这听起来是个好主意(尤其是当你喝多了的时候)，而且可能行得通；但是，相信我，你不会真的想这么做的。

不过，如果你有心情，这里有一些用十六进制写颜色的方法:

```
.hollering-exes {
  color: #fff;
  color: #fff8;     /* the last digit is for alpha */
  color: #ffffff;
  color: #ffffff88; /* the last two digits are for alpha */
}
```

# high-speedlaunch 高速快艇

[HSL](https://alvaromontoro.com/blog/67871/hsl) 也改变了意思。它过去指的是色调、饱和度和亮度，但这个缩写需要一点更新。这个功能更像**高鞋带**:它们看起来很酷很时尚，它们可能会搭配一些款式……但在内心深处，你知道它们已经过时了，并不真正适合你穿的衣服。

HSL 是在 CSS 中进行主题化和创建颜色样本的“最佳方式”的日子已经一去不复返了(现在有了更好的颜色空间和相对颜色)…但是我不会阻止你使用它:

```
.high-shoe-laces {
  color: hsl(180, 50%, 50%);
  color: hsla(180, 50%, 50%, 0.5);
  /* HSL's new notation! (among others) */
  color: hsl(180 50% 50%);
  color: hsl(180 50% 50% / .5);
}
```

# HWB

作为颜色级别 4 模块的一部分， [HWB](https://alvaromontoro.com/blog/67874/hwb) 对 CSS 来说是新的，它代表色调-白色-黑色…但它也可以代表“**不怎么样？**“是的，与 HSL 或 RGB 相比，这是一种不同的、更自然的(对人类而言)表达颜色的方式。尽管如此，与作为色阶 4 模块的一部分引入的其他色彩空间相比，它感觉还是有所欠缺。

```
.howbout-no {
  color: hwb(180 50% 50%);
}
```

# 实验室

[Lab](https://alvaromontoro.com/blog/67875/lab) 代表亮度、a*和 b*，老实说，这并没有说太多关于色彩空间的事情。一个更好的首字母缩略词应该是“**看起来很酷**”，因为这就是实验室里的颜色:A-MA-ZING！

说真的，它们很神奇，是人类如何看待和感知亮度和颜色的更好方法。Safari(即使没有“技术预览”部分)目前支持 Lab。你绝对应该试试:

```
.looking-all-badass {
  color: lab(66% -35 -42);  
  color: lab(66% -35 -42 / 1);
}
```

# 组织郎格罕细胞增生症

与 Lab 相关(同样都是坏蛋)，在 [LCH](https://alvaromontoro.com/blog/67876/lch) 看颜色就像**在高清**看颜色。谁会关心亮度、色度、色调或色相——或者是"*嗯？*——当你的指尖(或你的浏览器窗口)有如此鲜明而又冷艳的色彩时。)

Safari 上也有，Firefox 上的一些(彩色)旗帜后面也有:

```
.looking-at-colors-in-hd {
  color: lch(66% -35 -42);  
  color: lch(66% -35 -42 / 1);
}
```

# 用于印刷的四分色

如果当我说“**你知道的彩色机器**”时，你的第一个想法是打印机，给自己一个饼干吧！因为你知道只有一台机器使用[青色、品红色、黄色和黑色(主色)](https://alvaromontoro.com/blog/67877/cmyk)，那就是打印机。别无其他。没什么。

这个颜色定义主要是针对你知道的那些色机。即使你家里可能没有，因为你不是一个 GenX 或者年长的千禧一代，而是一个很酷的千禧一代或者 GenZ。绝对不是婴儿潮一代。

为打印机编写颜色很酷也很容易:只需使用百分比…不幸的是，浏览器不喜欢这个:

```
.color-machines-you-know {
  color: device-cmyk(100% 0% 0% 0%);    
  color: device-cmyk(100% 0% 0% 0% / 1);
}
```

# OKLab + OKLCH

你还记得小时候，征求父母的同意，他们一直说“不”，但最终还是说“好吧，你可以的”？这有点像那个。我们已经有了 Lab 和 LCH，它们很好，但是我们要求更好的东西，所以我们得到了 OKLab 和 OKLCH，它们更容易使用和预测。类似于“**好的，这里有一个专门为你准备的新实验室和 LCH**

想尝尝吗？在 Safari 上检查它们(技术预览):

```
.ok-looking-all-badass-in-hd {
  color: oklab(66% -35 -42);    
  color: oklch(66% -35 -42 / 1);
}
```

# P3

没有人知道 P3 代表什么(好吧，好吧，我打赌也许有人知道)…所以我们可以想出任何我们想要的缩写。还有什么比 3 P 更好的 P3 缩写呢:**故意现象级&幻想级**。

注意:有人告诉我，幻想症可能不仅指奇异，还指一些致幻物质……这就更好了！尤其是如果我们考虑到你在使用 P3 时会体验到的充满活力色彩的全新世界。当然，你会觉得那些颜色是高清的。

P3 可用于以下功能:

```
.purposely-phenomenal-phantastic {
  color: color(display-p3 0 1 1);
}
```

*原载于 2022 年 3 月 13 日*[*【https://alvaromontoro.com】*](https://alvaromontoro.com/blog/67999/a-new-meaning-for-color-functions-in-css)*。*