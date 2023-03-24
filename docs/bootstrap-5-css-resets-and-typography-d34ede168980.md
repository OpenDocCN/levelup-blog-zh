# Bootstrap 5 — CSS 重置和排版

> 原文：<https://levelup.gitconnected.com/bootstrap-5-css-resets-and-typography-d34ede168980>

![](img/178f58ee67815e0ae6c7d98fc8358e97.png)

照片由 [Mazlin Massey](https://unsplash.com/@mazlinrhea?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

**写这篇文章时，Bootstrap 5 处于 alpha 状态，可能会更改。**

Bootstrap 是任何 JavaScript 应用程序的流行 UI 库。在本文中，我们将研究由 Bootstrap 5 和 style text 提供的工作重置的最佳实践。

# 抽样输出

为了显示样本输出，我们可以使用`samp`标签。

例如，我们可以写:

```
<samp>computer output</samp>
```

`samp`标签用于显示计算机输出的文本。

# 桌子

Bootstrap 5 调整了标题的表格样式，折叠了边框，并确保了一致的`text-align`。

`.table`类有更多的格式变化。

# 形式

助推器对形态做了很多调整。

`fieldset`没有边框、填充或空白，因此可以很容易地用作单个输入和输入组的包装器。

`legends`也被重新设计为标题显示。

`label`设置为`display: inline-block`以允许应用边距。

`input` s、`select` s、`textarea` a 和`button` s 大多通过规格化复位。

重启移除它们的`margin`并将`line-height`设置为`inherit`。

被修改为只能垂直调整大小，因为水平调整大小经常会破坏页面布局。

`button` s 和`input`按钮元件未禁用时有`cursor: pointer`。

# 按钮上的指针

指针更改应用于按钮。

这是在一个按钮元素上自动完成的，也适用于添加了`role='button'`的任何东西。

# 地址元素

`address`被更新为休息浏览器默认`font-style`由斜体变为正常。

`line-height`现已继承。

`margin-bottom: 1rem`已添加。

`address`元素用于呈现最近祖先的联系信息。

可以用`br`元素保存格式。

# 引用

Blockquotes 的缺省值`margin`被设置为`0 0 1rem`，以使它们与其他元素更加一致。

# 内嵌元素

`abbr`元素接受了基本样式，使其在段落文本中脱颖而出。

# 摘要

`summary`元素的`cursor`设置为`pointer`以转换当我们点击它时该元素可以与之交互。

# HTML5 `[hidden]`属性

将`hidden`属性设置为`display: none important`有助于防止`display`被意外覆盖。

# 排印

Bootstrap 5 为我们提供了一套排版风格。

它使用本地字体堆栈，为显示页面的操作系统和设备选择最佳的`font-family`。

假定浏览器默认根目录`font-size`，通常为 16px。

它使用`$font-family-base`、`$font-size-base`和`$line-height-base`属性作为应用于`body`的排版基础。

Bootstrap 5 还用`$link-color`变量设置全局链接颜色，并仅在我们悬停时应用链接下划线。

将`$body-bg`设置为`body`至`#fff`上的`background-color`。

# 标题

样式应用于从 h1 到 h6 的所有标题标签。

还有从`.h1`到`.h6`的课程。

我们可以写:

```
<h1>h1 heading</h1>
<h2>h2 heading</h2>
<h3>h3 heading</h3>
<h4>h4 heading</h4>
<h5>h5 heading</h5>
<h6>h6 heading</h6>
```

或者:

```
<p class="h1">h1heading</p>
<p class="h2">h2 heading</p>
<p class="h3">h3 heading</p>
<p class="h4">h4 heading</p>
<p class="h5">h5 heading</p>
<p class="h6">h6 heading</p>
```

# 自定义标题

我们可以用实用程序类定制标题，从 Bootstrap 3 中重新创建小的二级标题文本。

例如，我们可以使用`text-muted`类来改变使文本颜色变浅:

```
<h3>
  <small class="text-muted">faded text</small>
</h3>
```

# 显示标题

Bootstrap 5 提供了比常规标题更大的显示标题类。

例如，我们可以写:

```
<h1 class="display-1">Display 1</h1>
<h1 class="display-2">Display 2</h1>
<h1 class="display-3">Display 3</h1>
<h1 class="display-4">Display 4</h1>
<h1 class="display-5">Display 5</h1>
<h1 class="display-6">Display 6</h1>
```

来添加它们。

课程都是以`display-`开头的。

# 铅

我们可以用`.lead`类让段落突出。

例如，我们可以写:

```
<p class="lead">
  Lorem ipsum dolor sit amet, consectetur adipiscing elit. Donec nec mi auctor, blandit nisi ac, venenatis erat. Proin aliquam auctor ipsum eu congue. Mauris consequat dolor id posuere posuere. Curabitur in blandit eros. Donec at dui turpis. Curabitur faucibus est enim, quis facilisis enim ultrices a. Vivamus sed tellus tortor. Fusce malesuada ante dui. Morbi dignissim sapien eu mattis facilisis. Nunc vel enim nec risus rhoncus mollis at accumsan diam. Aliquam sed volutpat turpis.
</p>
```

给我们的文本添加特殊的样式。

![](img/0b2ae0b341f1be1756b257992d0352ff.png)

[娜塔莉·斯科特](https://unsplash.com/@natysctt?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

我们可以在 Bootstrap 5 的默认风格上构建自己的风格。

此外，我们可以用多种方式设计文本样式。