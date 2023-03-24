# 引导 5 —调整大小、间距、包装和对齐

> 原文：<https://levelup.gitconnected.com/bootstrap-5-sizing-spacing-wrapping-and-alignment-aaea929045e3>

![](img/3eee69e07fb5cb6b0e1793f07786c493.png)

Matthew Foulds 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

**写这篇文章时，Bootstrap 5 处于 alpha 状态，可能会更改。**

Bootstrap 是任何 JavaScript 应用程序的流行 UI 库。

在本文中，我们将研究如何使用 Bootstrap 5 相对于视口、间距、换行和对齐来调整元素的大小。

# 相对于视口调整大小

Boostrap 5 有相对于视口来调整项目大小的类。

为此，我们可以使用以下代码:

```
<div class="min-vw-100">Min-width 100vw</div>
<div class="min-vh-100">Min-height 100vh</div>
<div class="vw-100">Width 100vw</div>
<div class="vh-100">Height 100vh</div>
```

`min-vw-100`将 div 的最小宽度定为 100vw。

`min-vh-100`将 div 的最小高度设置为 100vh。

`vw-100`将 div 的宽度调整为 100vw。

并且`vh-100`将 div 的大小设置为 100vh。

# 间隔

Bootstrap 提供了各种快捷方式来响应边距和填充大小。

我们可以应用类来添加边距和填充。

这些类的格式是`xs`的`{property}{sides}-{size}`和其他断点的`{property}{sides}-{breakpoint}-{size}`。

`property`是以下之一:

*   `m`为保证金
*   `p`用于填充

`sides`是下列之一:

*   `t` -对于设置了`margin-top`或`padding-top`的类
*   `b` -对于设置了`margin-bottom`或`padding-bottom`的类
*   `l` -对于设置了`margin-left`或`padding-left`的类
*   `r` -对于设置了`margin-right`或`padding-right`的类
*   `x` -对于设置了`*-left`和`*-right`的类
*   `y` -对于设置了`*-top`和`*-bottom`的类
*   空白—对于在元素的所有 4 个边上都设置了`margin`或`padding`的类

`size`是下列之一:

*   `0` -对于通过将`margin`或`padding`设置为`0`来消除它们的类
*   `1` -(默认情况下)用于将`margin`或`padding`设置为`$spacer * .25`的类
*   `2` -(默认情况下)用于将`margin`或`padding`设置为`$spacer * .5`的类
*   `3` -(默认)对于将`margin`或`padding`设置为`$spacer`的类
*   `4` -(默认)对于将`margin`或`padding`设置为`$spacer * 1.5`的类
*   `5` -(默认)对于将`margin`或`padding`设置为`$spacer * 3`的类
*   `auto` -用于将`margin`设置为自动的类别

# 水平居中

如果元素是块元素，我们可以用`.mx-auto`类将内容水平居中。

任何为`display: block`并设置了宽度的东西都是块元素。

例如，我们可以写:

```
<div class="mx-auto" style="width: 500px;">
  Centered
</div>
```

我们有一个内容居中的 div。

# 压缩边界

在 Bootstrap 5 中，负边距是默认禁用的，但是我们可以通过将`$enable-negative-margins`属性设置为`true`在 SASS 代码中启用它。

然后我们可以应用带有`n`的类来应用负边距。

比如我们可以写`.mt-n1`加一个负边距。

# 文本

Bootstrap 5 有用于对齐文本的实用程序类。

例如，我们可以写:

```
<p class="text-left">Left aligned.</p>
<p class="text-center">Center aligned.</p>
<p class="text-right">Right aligned.</p>

<p class="text-sm-left">Left aligned sm or wider.</p>
<p class="text-md-left">Left aligned md or wider.</p>
<p class="text-lg-left">Left aligned lg or wider.</p>
<p class="text-xl-left">Left aligned xl or wider.</p>
```

`text-left`类左对齐文本。

`text-center`类中心对齐文本。

`text-right`右对齐文本。

`text-sm-left`当断点为 sm 或更宽时，左对齐文本。

`text-md-left`如果断点为`md`或更宽，则文本左对齐。

`text-lg-left`如果断点为`lg`或更宽，则文本左对齐。

如果断点为`xl`或更宽，则`text-xl-left`左对齐文本。

# 文本换行和溢出

我们可以用`.text-wrap`类包装文本。

例如，我们可以写:

```
<div class="badge bg-primary text-wrap" style="width: 50px;">
  This text should wrap.
</div>
```

那么文本应该换行。

我们也可以使用`.text-nowrap`类来禁用包装:

```
<div class="badge bg-primary text-nowrap" style="width: 50px;">
  This text should wrap.
</div>
```

![](img/d6d1db4c05ad774ead330826b4be9c23.png)

由[在](https://unsplash.com/@reskp?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的

# 结论

我们可以用各种 Bootstrap 5 类添加间距和对齐。

此外，我们可以启用或禁用其提供的类的包装。

也可以进行文本对齐。