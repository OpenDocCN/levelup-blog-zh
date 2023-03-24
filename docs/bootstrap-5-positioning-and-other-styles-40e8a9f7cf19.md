# 引导 5 —定位和其他风格

> 原文：<https://levelup.gitconnected.com/bootstrap-5-positioning-and-other-styles-40e8a9f7cf19>

![](img/32ae206e1f5772339c6ceb6e5f988a36.png)

[豪尔赫·萨帕塔](https://unsplash.com/@jorgezapatag?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

**写这篇文章时，Bootstrap 5 处于 alpha 状态，可能会更改。**

Bootstrap 是任何 JavaScript 应用程序的流行 UI 库。

在本文中，我们将看看如何用 Bootstrap 5 添加浮动和许多其他样式。

# 浮动

我们可以用 Bootstrap 5 切换元素中的浮点数。

它可以应用于任何断点。

`floart`类让我们将 CSS float 属性添加到元素中。

例如，我们可以写:

```
<div class="float-left">Float left</div><br>
<div class="float-right">Float right</div><br>
<div class="float-none">Don't float</div>
```

使 div 向左浮动。

`float-right`使 div 向右浮动。

和`float-none`禁用浮动。

# 应答的

我们只能将浮点应用于某些断点。

例如，我们可以写:

```
<div class="float-sm-left">Float left on sm or wider</div><br>
<div class="float-md-left">Float left on md or wider</div><br>
<div class="float-lg-left">Float left on lg or wider</div><br>
<div class="float-xl-left">Float left on xl or wider</div><br>
```

我们有`sm`、`md`、`lg`和`xl`断点来指定何时应该应用浮动。

它们总是应用于该断点或更宽的断点。

其他浮动类包括:

*   `.float-left`
*   `.float-right`
*   `.float-none`
*   `.float-sm-left`
*   `.float-sm-right`
*   `.float-sm-none`
*   `.float-md-left`
*   `.float-md-right`
*   `.float-md-none`
*   `.float-lg-left`
*   `.float-lg-right`
*   `.float-lg-none`
*   `.float-xl-left`
*   `.float-xl-right`
*   `.float-xl-none`
*   `.float-xxl-left`
*   `.float-xxl-right`
*   `.float-xxl-none`

# 相互作用

Bootstrap 5 提供了一些类来改变我们与它们交互时选择内容的方式。

例如，我们可以写:

```
<p class="user-select-all">lorem ipsum.</p>
<p class="user-select-auto">lorem ipsum.</p>
<p class="user-select-none">lorem ipsum.</p>
```

`user-select-all`如果我们点击它，使所有文本被选中。

`user-select-auto`是默认的选择行为。

`user-select-none`当用户点击时，使段落不可选。

# 指针事件

`pe-none`和`pe-auto`类改变了我们与元素交互的方式。

`pe-none`禁用元素交互。

`pe-auto`添加元素引入。

例如，我们可以写:

```
<a href="#" class="pe-none">can't click</a>
<a href="#" class="pe-auto">can click</a>
<p class="pe-none">
  <a href="#">can't click</a>
  <a href="#" class="pe-auto"can click</a>
</p>
```

`pe-none`将链接设置为`pointer-events: none`，无法点击。

`pe-auto`设置默认链接行为。

这些类是继承的，所以它们将传播到子元素，除非我们像在段落中那样覆盖它们。

# 泛滥

Bootstrap 5 提供了设置溢出的实用程序类。

例如，我们可以写:

```
<div class="overflow-auto">...</div>
<div class="overflow-hidden">...</div>
```

第一个 div 将溢出设置为 auto，这是默认行为。

第二个将溢出设置为隐藏。

# 位置

Bootstrap 还为位置项提供了类。

我们可以通过书写来设置定位:

```
<div class="position-static">...</div>
<div class="position-relative">...</div>
<div class="position-absolute">...</div>
<div class="position-fixed">...</div>
<div class="position-sticky">...</div>
```

按照后缀的指示设置`position` CSS 属性的位置。

# 阴影

我们可以用 Bootstrap box-shadow 实用程序类给元素添加或移除阴影。

例如，我们可以写:

```
<div class="shadow-none p-3 mb-5 bg-light rounded">No shadow</div>
<div class="shadow-sm p-3 mb-5 bg-white rounded">Small shadow</div>
<div class="shadow p-3 mb-5 bg-white rounded">Regular shadow</div>
<div class="shadow-lg p-3 mb-5 bg-white rounded">Larger shadow</div>
```

添加各种阴影。

类让我们添加不同大小的阴影。

# 胶料

我们可以用一些提供的类来改变元素的宽度。

我们可以使用`w-*`类来设置宽度。

例如，我们可以写:

```
<div class="w-25 p-3" style="background-color: lightgray;">Width 25%</div>
<div class="w-50 p-3" style="background-color: lightgray;">Width 50%</div>
<div class="w-75 p-3" style="background-color: lightgray;">Width 75%</div>
<div class="w-100 p-3" style="background-color: lightgray;">Width 100%</div>
<div class="w-auto p-3" style="background-color: lightgray;">Width auto</div>
```

设置宽度。

`h-*`让我们设定高度:

```
<div style="height: 100px; background-color: rgba(255,0,0,0.1);">
  <div class="h-25 d-inline-block" style="width: 120px; background-color: lightgray">Height 25%</div>
  <div class="h-50 d-inline-block" style="width: 120px; background-color: lightgray">Height 50%</div>
  <div class="h-75 d-inline-block" style="width: 120px; background-color: lightgray">Height 75%</div>
  <div class="h-100 d-inline-block" style="width: 120px; background-color: lightgray)">Height 100%</div>
  <div class="h-auto d-inline-block" style="width: 120px; background-color: lightgray">Height auto</div>
</div>
```

我们将高度设置为外部 div 高度的一部分。

![](img/db06a0dc477956d47df87af34bb11ab6.png)

安娜·杜德科娃在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

我们可以使用 Bootstrap 5 类来设置元素的高度、宽度、位置、阴影和溢出，以及链接的交互性。