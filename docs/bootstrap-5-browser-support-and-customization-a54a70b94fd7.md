# 引导程序 5 —浏览器支持和定制

> 原文：<https://levelup.gitconnected.com/bootstrap-5-browser-support-and-customization-a54a70b94fd7>

![](img/246fbb5a0e15b67f8ec13324ece2ac58.png)

照片由 [niko photos](https://unsplash.com/@niko_photos?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

**写这篇文章时，Bootstrap 5 处于 alpha 状态，可能会发生变化。**

Bootstrap 是任何 JavaScript 应用程序的流行 UI 库。

在本文中，我们将了解如何定制和扩展 Bootstrap 5 特性以及浏览器和设备支持。

# 支持的浏览器

Bootstrap 支持现代浏览器，包括最新版本的传统 Edge。

使用 Webkit、Blink 或 Gecko 引擎的其他浏览器也应该可以正常显示。

# 移动设备

Bootstrap 支持大多数现代 Android 设备。

也支持 Android 6 或更高版本的 Android 浏览器和 webview。

Android 中的 Chrome 和 Firefox 也支持

也支持 iOS Chrome、Firefox 和 Safari。

# 桌面浏览器

在 Mac 上，支持 Chrome、Firefox、Edge、Opera 和 Safari。

在 Windows 上，支持 Chrome、Firefox、Edge 和 Opera。

# 微软公司出品的 web 浏览器

Bootstrap 5 中不再支持 Internet Explorer。

# 手机上的模态和下拉框

`overflow: hidden`on`body`iOS 和 Android 支持有限。

这意味着即使在这些平台中应用了 CSS，我们仍然可以滚动。

# iOS 文本栏和滚动

从 iOS 9.2 开始，当模态打开时，初始触摸模态下方带有输入元素或主体的滚动功能将会滚动，而不是模态本身。

# 导航条下拉菜单

`.dropdown-navbar`由于 z-index 的复杂性，iOS 导航中没有使用元素。

# 浏览器缩放

缩放可能会在页面上显示渲染伪像，目前没有干净的解决方案。

# 厚颜无耻

我们可以从引导包中导入 SASS 模块。

这样，我们可以用自己的代码扩展引导 SASS 代码。

例如，我们可以写:

```
@import "../node_modules/bootstrap/scss/bootstrap";
```

包含所有引导样式。

我们还可以通过编写以下内容来逐个导入它们:

```
[@import](http://twitter.com/import) "../node_modules/bootstrap/scss/functions";
[@import](http://twitter.com/import) "../node_modules/bootstrap/scss/variables";
[@import](http://twitter.com/import) "../node_modules/bootstrap/scss/mixins";[@import](http://twitter.com/import) "../node_modules/bootstrap/scss/reboot";
[@import](http://twitter.com/import) "../node_modules/bootstrap/scss/type";
[@import](http://twitter.com/import) "../node_modules/bootstrap/scss/images";
[@import](http://twitter.com/import) "../node_modules/bootstrap/scss/code";
[@import](http://twitter.com/import) "../node_modules/bootstrap/scss/grid";
```

然后我们可以用自己的代码覆盖这些值。例如，我们可以写:

```
$body-bg: green;
$body-color: orange;
```

# 修改地图

我们可以用自己的代码通过覆盖变量的值来修改`$theme-colors`映射。

例如，我们可以写:

```
$primary: green;
$danger: red;
```

然后他们会覆盖地图上的值。

# 添加到地图

我们可以给`$theme-colors`地图添加新的值。

例如，我们可以写:

```
$theme-colors: (
  "custom-color": brown
);
```

到我们的`$theme-colors`地图。

# 从地图中移除

我们可以用`map-remove`功能从地图上移除颜色。

例如，我们可以写:

```
[@import](http://twitter.com/import) "../node_modules/bootstrap/scss/functions";
[@import](http://twitter.com/import) "../node_modules/bootstrap/scss/variables";
[@import](http://twitter.com/import) "../node_modules/bootstrap/scss/mixins";$theme-colors: map-remove($theme-colors, "info", "light");// Optional
[@import](http://twitter.com/import) "../node_modules/bootstrap/scss/root";
[@import](http://twitter.com/import) "../node_modules/bootstrap/scss/reboot";
[@import](http://twitter.com/import) "../node_modules/bootstrap/scss/type";
```

我们从`$theme-colors`图中移除`info`和`light`值。

# 颜色；色彩；色调

Bootstrap 5 删除了`color()`、`theme-color()`和`gray()`函数，因为这些值可以作为独立变量使用。

例如，我们不使用`theme-color("primary")`来获取原色值，而是使用`$primary`。

# CSS 变量

Bootstrap 包括许多我们可以在应用程序中使用的 CSS 变量。

它们包括:

```
:root {
  --bs-blue: #0d6efd;
  --bs-indigo: #6610f2;
  --bs-purple: #6f42c1;
  --bs-pink: #d63384;
  --bs-red: #dc3545;
  --bs-orange: #fd7e14;
  --bs-yellow: #ffc107;
  --bs-green: #28a745;
  --bs-teal: #20c997;
  --bs-cyan: #17a2b8;
  --bs-white: #fff;
  --bs-gray: #6c757d;
  --bs-gray-dark: #343a40;
  --bs-primary: #0d6efd;
  --bs-secondary: #6c757d;
  --bs-success: #28a745;
  --bs-info: #17a2b8;
  --bs-warning: #ffc107;
  --bs-danger: #dc3545;
  --bs-light: #f8f9fa;
  --bs-dark: #343a40;
  --bs-font-sans-serif: system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Noto Sans", sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji";
  --bs-font-monospace: SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
  --bs-gradient: linear-gradient(180deg, rgba(255, 255, 255, 0.15), rgba(255, 255, 255, 0));
}
```

我们可以将这些值用于颜色和字体。

![](img/e372b31473f07febadecfb5cba1be340.png)

照片由[甘](https://unsplash.com/@carissagan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 结论

Bootstrap 5 支持大多数现代浏览器。

此外，我们可以用各种方式定制值。