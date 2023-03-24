# 引导程序 5 —布局

> 原文：<https://levelup.gitconnected.com/bootstrap-5-layouts-94c1b4d4e83d>

![](img/a8892194d4c5cd3f7ee25a3f2075d020.png)

照片由[Louis Hansel @ shotsoflouis](https://unsplash.com/@louishansel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

**写这篇文章时，Bootstrap 5 处于 alpha 状态，可能会更改。**

Bootstrap 是任何 JavaScript 应用程序的流行 UI 库。

在本文中，我们将了解如何使用 Bootstrap 5 创建布局。

# 断点

断点是响应式设计最基本的部分。

我们可以用它来控制页面的布局，当它适应特定的视窗或设备尺寸时。引导断点是用 CSS 媒体查询构建的。

这种设计是移动优先的，默认情况下是响应式的。

# 可用断点

引导程序 5 有 6 个断点。

它们包括介于 0 和 576 像素之间的 x-small。

小，其中有`sm`类中缀是 576px 或者更大。

中，其中`md`类中缀为 768px 或更大。

大，其中有`lg`类中缀是 992px 或更大。

用`xl`内插的特大号，1200px 以上。

由`xxl`嵌入的 Extra extra large 是 1400px 或更大。

# 最小宽度

Bootstrap 混合使用最小宽度的媒体查询。

它们包括:

```
[@include](http://twitter.com/include) media-breakpoint-up(md) { ... }
[@include](http://twitter.com/include) media-breakpoint-up(lg) { ... }
[@include](http://twitter.com/include) media-breakpoint-up(xl) { ... }
[@include](http://twitter.com/include) media-breakpoint-up(xxl) { ... }
```

它们相当于:

```
[@media](http://twitter.com/media) (min-width: 768px) { ... }
[@media](http://twitter.com/media) (min-width: 992px) { ... }
[@media](http://twitter.com/media) (min-width: 1200px) { ... }
[@media](http://twitter.com/media) (min-width: 1400px) { ... }
```

# 最大宽度

最大宽度也有断点。

例如，我们可以写:

```
[@include](http://twitter.com/include) media-breakpoint-down(sm) { ... }
[@include](http://twitter.com/include) media-breakpoint-down(md) { ... }
[@include](http://twitter.com/include) media-breakpoint-down(lg) { ... }
[@include](http://twitter.com/include) media-breakpoint-down(xl) { ... }
[@include](http://twitter.com/include) media-breakpoint-down(xxl) { ... }
```

这相当于:

```
[@media](http://twitter.com/media) (max-width: 575.98px) { ... }
[@media](http://twitter.com/media) (max-width: 767.98px) { ... }
[@media](http://twitter.com/media) (max-width: 991.98px) { ... }
[@media](http://twitter.com/media) (max-width: 1199.98px) { ... }
[@media](http://twitter.com/media) (max-width: 1399.98px) { ... }
```

`xxl` 没有查询，因为宽度没有上限。

# 断点之间

还有可以跨越多个断点宽度的媒体查询:

```
[@include](http://twitter.com/include) media-breakpoint-between(md, xl) { ... }
```

也就是说:

```
[@media](http://twitter.com/media) (min-width: 768px) and (max-width: 1199.98px) { ... }
```

# 容器

容器让我们展示里面的东西。

它允许我们在给定的设备或视窗中填充和对齐内容。

有`container`、`container-sm`、`container-md`、`container-lg`、`container-xl`、`container-xxl`和`container-fluid`类来确定容器的大小。

# 默认容器

`container`类是一个响应性的固定宽度容器。

例如，我们可以写:

```
<div class="container">
  <!-- ... -->
</div>
```

去使用它。

# 响应容器

我们可以使用容器中的其他类来添加响应容器。

例如，我们可以写:

```
<div class="container-sm">small</div>
<div class="container-md">medium </div>
<div class="container-lg">large </div>
<div class="container-xl">extra large </div>
<div class="container-xxl">extra extra large </div>
```

来添加它们。

它们将是 100%的，直到它们到达由类中的中缀指示的断点。

# 流体容器

要添加跨越整个视口宽度的流体容器，我们可以使用`container-fluid`类:

```
<div class="container-fluid">
  ...
</div>
```

# 网格系统

网格系统让我们通过指定它占据网格的多少列来做出响应。

它基于 flexbox，反应灵敏。

例如，我们可以写:

```
<div class="container">
  <div class="row">
    <div class="col-sm">
      One of three columns
    </div>
    <div class="col-sm">
      One of three columns
    </div>
    <div class="col-sm">
      One of three columns
    </div>
  </div>
</div>
```

`col-sm`表示在低于`sm`断点之前，所有设备上将有 3 个等宽的列。

网格支持 6 个响应断点。

包括`sm`、`md`、`lg`、`xl`、`xxl`。

容器可以水平居中以填充内容。

`container`用于响应像素宽度。

`container-fluid`是跨越所有视窗和设备的`width: 100%`。

行是列的包装器。

列跨越 12 列。每行有 12 个。

水槽也是可响应和可定制的。他们让我们在列之间增加空间。

`.g-0`用于移除檐槽`gx-*`用于改变水平檐槽。

`.gy-*`用于添加垂直檐槽。

![](img/b5ea7f45f703cf39298e4dc228bc00ea.png)

照片由 [Anh Nguyen](https://unsplash.com/@pwign?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 结论

我们可以用容器、列和檐槽来定义自己的布局。

他们都反应迅速，移动优先。