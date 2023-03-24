# 不要使用变换使元素居中

> 原文：<https://levelup.gitconnected.com/dont-use-transform-to-center-element-b378587ad1db>

## 一种更好的方法来使我们不知道大小的元素居中

![](img/ac147533b2276dfd009c57f812162b92.png)

照片由[思想目录](https://unsplash.com/@thoughtcatalog?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 前言

我们可以通过谷歌找到很多关于如何将元素居中的解决方案。例如，我们实现了一个居中的对话框，我们不知道它的宽度和高度，大多数文章会用`transform`和`translate`来实现它，可能是这样的:

```
<div class="dialog"></div> .dialog {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```

它工作得很好，直到有一天，我们需要添加一点动画:

![](img/29f3ffedff843b6e8540bbcb97641157.png)

带动画的对话框

动画中使用的变换将与居中元素的变换冲突。

# 如何在不变换的情况下使元素居中

如果我们想让一个已知宽度和高度的块元素居中，我们可以使用简写的值为`auto`的`margin`属性:

```
<div class="dialog"></div>.dialog {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  margin: auto;
  width: 200px;
  height: 200px;
}
```

如果我们能知道对话框的宽度和高度，我们就不需要使用`transform`来居中元素。也许你会像我之前想的那样想通过 JavaScript 获得元素的高度和宽度:

1.  使用 CSS 样式表`visibility:hidden`隐藏对话框
2.  计算对话框的宽度和高度
3.  设置对话框样式

它可以工作，但它是有线的，会让代码有味道。因此，我在谷歌上探索解决方案，我发现了一个更好的方法来解决这个问题，使用 [fit-content](https://developer.mozilla.org/en-US/docs/Web/CSS/fit-content) 可以用作宽度和高度的布局框大小。它会根据内容自动调整大小，从[caniuse.com](https://caniuse.com/mdn-css_properties_width_fit-content)的数据来看几乎支持所有的现代浏览器。所以我们可以用它来使元素居中，就像我们知道元素的高度和宽度一样:

```
<div class="dialog"></div>.dialog {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  margin: auto;
  width: fit-content;
  height: fit-content;
}
```

# 结论

通过将`width`和`height`设置为值`[fit-content](https://developer.mozilla.org/en-US/docs/Web/CSS/fit-content)`，我们可以在没有`transform`的情况下居中元素，该元素将与任何动画配合良好。

希望这篇文章可以帮助你，我期待你**跟随**我学习更多实用技巧，成为一名更好的开发人员。

[](/how-to-implement-scroll-to-top-with-only-css-ae27cb9d4678)[](/how-to-implement-scroll-to-top-with-only-css-ae27cb9d4678) [## 如何只用 CSS 实现滚动到顶部](/how-to-implement-scroll-to-top-with-only-css-ae27cb9d4678) [](/how-to-implement-scroll-to-top-with-only-css-ae27cb9d4678)

### [布局使用 sticky，滚动使用](/how-to-implement-scroll-to-top-with-only-css-ae27cb9d4678)标签

levelup.gitconnected.com