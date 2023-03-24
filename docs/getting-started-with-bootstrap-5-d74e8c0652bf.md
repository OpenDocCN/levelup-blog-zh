# Bootstrap 5 入门

> 原文：<https://levelup.gitconnected.com/getting-started-with-bootstrap-5-d74e8c0652bf>

![](img/1cef523430f53bf0565df87d8ac0faa9.png)

照片由 [Kate Remmer](https://unsplash.com/@studioktr?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

写这篇文章时，Bootstrap 5 处于 alpha 状态，它可能会发生变化。 Bootstrap 是一个流行的 UI 库，适用于任何 JavaScript 应用程序。

在本文中，我们将了解 Bootstrap 5 的新特性以及如何开始使用它。

# 新的外观和感觉

Bootstrap 5 给了我们一个不同于 4.5.0 版本的新外观。Internet Explorer 支持已被删除，因此该包现在更小了。

字体大小现在是响应式的，而不是固定大小的。

卡片叠被移除。

Navbar 现在需要添加更少的标记。

默认情况下，它不再显示为内嵌块元素。

还有一个新的自定义 SVG 图标库，包括许多图标。

# jQuery 和 JavaScript

jQuery 作为 Bootstrap 的依赖项被删除，因为 JavaScrtipt 可以提供相同的功能，而无需额外的依赖项。

既然我们不需要它，那么我们的应用程序中就少了一个依赖项。

按钮插件现在只有 CSS。

# CSS 自定义属性

Bootstrap 现在使用 CSS 自定义属性进行样式设置。

这允许 Bootstrap 重用值进行样式化。

# 增强型网格系统

电网系统有所改善。Bootstrap 5 有一个新的网格层，即`xxl`层。这是现在最小的断点。

`.gutter`分类已被替换为用于装订线间距的`.g*`实用程序。

新的网格系统替换了表单布局选项。

添加了垂直间距类别。

默认情况下，列不再是`position: relative`。

要定义新的檐槽，我们可以写:

```
<div class="row g-5">
  <div class="col">...</div>
  <div class="col">...</div>
</div>
```

我们有定义黄油的`g-5`类，

# 快速启动

我们可以通过进入[https://V5 . get Bootstrap . com/docs/5.0/getting-started/download/](https://v5.getbootstrap.com/docs/5.0/getting-started/download/)的下载页面下载 Bootstrap 包。

此外，我们可以将它包含在 CDN 中。可以通过编写以下内容来添加 CSS:

```
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/5.0.0-alpha1/css/bootstrap.min.css" integrity="sha384-r4NyP46KrjDleawBgD5tp8Y7UzmLA05oM1iAEQ17CSuDqnUK2+k9luXQOfXJCJ4I" crossorigin="anonymous">
```

可以通过编写以下代码来添加 JavaScript:

```
<script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script><script src="https://stackpath.bootstrapcdn.com/bootstrap/5.0.0-alpha1/js/bootstrap.min.js" integrity="sha384-oesi62hOLfzrys4LxRF63OJCXdXDipiYWBnvTl9Y9/TRlw5xlKIEHpNyvvDShgf/" crossorigin="anonymous"></script>
```

我们可以一起写下:

```
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1"> <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/5.0.0-alpha1/css/bootstrap.min.css" integrity="sha384-r4NyP46KrjDleawBgD5tp8Y7UzmLA05oM1iAEQ17CSuDqnUK2+k9luXQOfXJCJ4I" crossorigin="anonymous"> <title>Hello, world!</title>
  </head>
  <body>
    <h1>Hello</h1> <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script> <script src="https://stackpath.bootstrapcdn.com/bootstrap/5.0.0-alpha1/js/bootstrap.min.js" integrity="sha384-oesi62hOLfzrys4LxRF63OJCXdXDipiYWBnvTl9Y9/TRlw5xlKIEHpNyvvDShgf/" crossorigin="anonymous"></script>
  </body>
</html>
```

开始吧。

meta 标签用于使内容在任何屏幕上适当缩放。

链接标签有引导 CSS。脚本标签有引导 JavaScript。JavaScript 是可选的。

因为 Bootstrap 需要 Popper.js，所以顺序必须与列出的顺序相同。

# 包管理器

Bootstrap 在 NPM 也可以打包购买。

要安装它，我们运行:

```
npm install bootstrap@next
```

然后我们可以写下:

```
const bootstrap = require('bootstrap')
```

或者:

```
import bootstrap from 'bootstrap';
```

我们也可以通过运行以下命令安装纱线:

```
yarn add bootstrap
```

# RubyGems

它也可作为红宝石宝石。

要安装它，我们添加:

```
gem 'bootstrap', '~> 5.0.0-alpha1'
```

到我们的 Gemfile 并运行`bundle install`。

我们也可以运行:

```
gem install bootstrap -v 5.0.0-alpha1
```

来安装它。

# 设计者

它也可以作为一个 Composer 包使用。为了安装，我们运行:

```
composer require twbs/bootstrap:5.0.0-alpha1
```

# 努格特

如果我们在. NET 应用程序中使用它，我们可以运行:

```
Install-Package bootstrap
```

在 Powershell 中。

# HTML5 文档类型

HTML5 文档类型是必需的。

如果我们没有，那么我们会看到奇怪的造型。

所以我们必须写:

```
<!doctype html>
<html lang="en">
  ...
</html>
```

# 响应元标签

Bootstrap 必须有一个响应性的 meta 标记，以使其能够在所有设备上缩放。

这必须在 head 标签中。

所以我们必须写:

```
<meta name="viewport" content="width=device-width, initial-scale=1">
```

# 盒子尺寸

Bootstrap 将框大小值从内容框切换到边框框，以使大小调整更容易。

我们可以用以下内容覆盖它:

```
.foo {
  box-sizing: content-box;
}
```

包括`::before`和`::after`在内的所有子元素都将继承为`.foo`指定的框大小。

# 重新启动

重新启动用于纠正浏览器和设备之间的不一致。

![](img/3f783e90584e0d8d845c3115a0973433.png)

照片由 [niko photos](https://unsplash.com/@niko_photos?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

Bootstrap 5 是即将推出的 Bootstrap 版本。

它删除了 IE 支持和许多其他膨胀。

外观和代码也有很多变化。

它有多种软件包，也可以从他们的 CDN 下载。