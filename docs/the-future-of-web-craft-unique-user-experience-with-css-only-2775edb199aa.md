# 网络的未来——只用 CSS 创造独特的用户体验

> 原文：<https://levelup.gitconnected.com/the-future-of-web-craft-unique-user-experience-with-css-only-2775edb199aa>

## 未来已经在这里；只是分布不太均匀

![](img/c124237a8fc7db78c3867add70f11e22.png)

图为[诺亚·纳夫](https://unsplash.com/@noahdavis?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/unique?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

想象一下，能够根据您的用户需求和偏好设计一个 web 应用程序。将颜色、对比度、运动、主题调整到用户想要的样子。想象一下只用 CSS 来做这件事。

听起来是不是很棒？

这就是未来。更好的是，你已经可以做这些事情了，尽管浏览器的支持是有限的。

## 媒体查询级别 5

你对 CSS 感兴趣，所以你可能熟悉媒体查询。它们允许为不同的设备类型或特定的特征和参数应用不同的样式。因此，如果您想将 CSS 只用于移动设备，您可以使用媒体查询。

媒体查询第 5 级是一个新媒体查询的提议(正式的 W3C 工作草案),它将扩展您现在正在使用的媒体查询。其中最令人兴奋的主张是用户偏好媒体功能，它允许根据用户偏好应用不同的风格。

例如，如果用户希望将深色主题作为默认，或者喜欢简化的动画，我们可以在代码中解决这个问题，并根据用户设置应用样式。这意味着为用户创造一种独特的体验，最大限度地满足他们的需求。

# 用户偏好媒体特征

我创造了一支笔来展示新媒体询问的力量。请记住，不同的浏览器甚至不同的操作系统对新媒体特性的支持是不同的，所以要看到所有的效果，您需要切换浏览器。

这支笔是基于阿尔伯特·瓦利基的美丽的跳动的心脏动画

以下是当浏览器执行第 5 级媒体查询时，您将能够使用的媒体功能。

## 减少运动

你喜欢 web 应用中华而不实的动画吗？我不知道。动作太多让一切都感觉混乱。`prefers-reduced-motion`媒体功能允许检测用户是否想要最小化动画和动作的数量。它可以取值为`no-preference`或`reduce`。后者意味着用户想要减少运动量。

在钢笔中，你可以看到跳动的心脏的动画被减慢，并且因为减少的运动而变得更加离散。

```
[@media](http://twitter.com/media) (prefers-reduced-motion) {
  .heart {
    animation-name: smallBeat;
    animation-duration: 5s;
  }
}
```

要测试减少运动，您需要调整操作系统或浏览器中的设置。你可以在这里查看怎么做[。](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion#user_preferences)

大多数浏览器已经支持减少运动，只有 Internet Explorer 是唯一受欢迎的例外。

## 默认主题

我喜欢黑色主题。我喜欢将操作系统的默认主题设置为黑暗。使用`prefers-color-scheme`媒体功能，您可以利用用户主题设置并根据它选择默认主题。该特征取值为`light`和`dark`。

在 pen 中，你可以看到应用程序的背景色和心形背景色是为使用深色主题的用户而改变的。

```
[@media](http://twitter.com/media) (prefers-color-scheme: dark) {
  body   { background:  #333 }
 .heart:before,
 .heart:after,
 .heart { background: cyan }
}
```

要测试这是如何工作的，请更改操作系统的默认主题。以下是关于 MAC OS[和 Windows](https://developer.apple.com/design/human-interface-guidelines/macos/visual-design/dark-mode/) 主题改变的教程链接。

大多数浏览器都支持默认主题，Internet Explorer 是唯一受欢迎的例外。

## 默认对比度

好的对比是那些直到你做了才会注意到的事情之一。有些用户需要有更高或更低的对比度才能看清 app 的内容。要满足这一需求，您可以使用`prefers-contrast`媒体功能。它有几个值(`no-preference`、`less,`、`more`、`custom`)，允许为不同偏好的人设置特定的风格。

在 pen 中可以看到，对于偏好更多对比度的用户，`h1`的颜色设置为红色，而对于偏好较少对比度的用户，则设置为浅灰色。

```
[@media](http://twitter.com/media) (prefers-contrast: more) {
  .contrast {
    color: red;
  }
}[@media](http://twitter.com/media) (prefers-contrast: less) {
  .contrast {
    color: lightgray;
  }
}
```

不幸的是，目前唯一默认支持该功能的浏览器是 Safari。要测试它，请更改您的 [macOS](https://support.apple.com/lv-lv/guide/mac-help/unac089/mac) 上的对比度。

## 强制颜色

媒体特征是最严格的特征之一。它允许用户限制整个应用程序的调色板。该特征可以取两个值之一:`none`或`active`。如果强制颜色处于活动状态，应用程序将只使用浏览器指定的颜色，并删除一些其他样式(如框阴影和文本阴影)。

强制颜色是一个复杂的媒体特征；如果你需要更多关于模式，你可以使用的颜色，以及如何启用它的信息，请查看 [MDN 页面](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/forced-colors)。

在钢笔中，在强制颜色模式下,“链接到此笔”锚点下的文本阴影消失，取而代之的是`ButtonText`颜色的下划线。

```
[@media](http://twitter.com/media) (forced-colors: active) {
  .link {
    border-bottom: 2px ButtonText solid;
  }
}
```

目前 Chrome、Firefox 和 Edge 浏览器都支持强制颜色。

## 透明度降低

有些用户不喜欢界面中半透明的图层效果。使用`prefers-reduced-transparency`媒体功能，您可以针对该设置选择合适的风格。它可以取值为`no-preference`或`reduce`。后者意味着用户想要最小化透明效果。

在钢笔中，您可以看到，喜欢透明度较低的用户看到的心形的不透明度值高于默认值。

```
[@media](http://twitter.com/media) (prefers-reduced-transparency: reduce) {
  .heart {
    opacity: 1;
  }
}
```

现在，浏览器还不支持这个特性，但是希望它会很快改变。