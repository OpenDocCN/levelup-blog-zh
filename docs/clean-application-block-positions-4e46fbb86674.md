# 清洁应用—阻挡位置

> 原文：<https://levelup.gitconnected.com/clean-application-block-positions-4e46fbb86674>

## WEB 开发

## 分步教程的第二部分，构建一个干净、轻量级、速度极快的 web 应用程序。

![](img/fbc3cac5d427a0ab0e26cb4364326152.png)

乔安娜·科辛斯卡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

这是一步一步的教程的第二部分，该教程将在没有快餐式框架的情况下构建一个干净、轻量级、速度极快的 web 应用程序。如果您还没有阅读第一部分，请阅读:

[](/clean-application-flexible-box-layout-98e439bb7d96) [## 清洁应用—灵活的盒子布局

### 简单地说，圣杯就是一个多列的网页布局，这些列具有相同的高度，并且用…

levelup.gitconnected.com](/clean-application-flexible-box-layout-98e439bb7d96) 

# 阻止位置

当我们处理在页面上定位内容时，有各种有用的属性可以帮助我们操作元素的不同位置。

```
│<---------SCREEN-WIDTH--------->│
    │<------SITE-WIDTH------>│
+---+------------------------+---+
│...│     HEADER SECTION     │...│
│...+------------------------+...│
│...│                        │...│
│...│        CONTENT         │...│
│...│        SECTION         │...│
│...│                        │...│
│...│                        │...│
│...+------------------------+...│
│...│     FOOTER SECTION     │...│
+---+------------------------+---+
```

# 了解职位

为简单起见，我们将重用之前的代码，创建新的 [02-positions](https://github.com/vpodk/clap/blob/master/02-positions) 文件夹，并将 [01-layout](https://github.com/vpodk/clap/blob/master/01-layout) 的内容复制到其中。我们将使用以下目录结构:

```
02-positions
├── styles
│   ├── app.css
│   ├── flex.css
│   └── theme.css
└── index.html
```

我们现在可以为我们的主要应用程序样式创建 [app.css](https://github.com/vpodk/clap/blob/master/02-positions/styles/app.css) 文件:

# 说明

带有`position: fixed`的`header`元素将相对于视窗定位，这意味着即使页面滚动，它也总是停留在同一位置。

设置`margin: 0 auto`属性，使`header`和`footer`元素在`body`容器中水平居中。元素将占据指定的宽度，剩余的空间将在左边距和右边距之间平均分配。

为了解决 Safari 中元素位置固定到页面中心的问题，我们将对 header 元素使用`translateX(-50%)`属性。

接下来，我们将 [app.css](https://github.com/vpodk/clap/blob/master/02-positions/styles/app.css) 文件链接到我们的[index.html](https://github.com/vpodk/clap/blob/master/02-positions/index.html)页面:

```
<link href="./styles/app.css" rel="stylesheet">
```

一旦完成，我们就用标签`<h1>`包装`header`来提高可见性:

```
<header><h1>Header</h1></header>
```

我们现在可以使用任何浏览器打开 index.html 的[页面。您可以尝试滚动和调整页面大小，甚至使用 Chrome 开发工具来模拟任何移动设备。](https://vpodk.github.io/clap/02-positions/)

在下一个教程中，我们将为更好的用户体验创建平滑的动画菜单按钮——汉堡包菜单。

[](https://medium.com/@vpodk/clean-application-hamburger-menu-5736d4c459d9) [## 干净的应用程序—汉堡菜单

### 分步教程的第三部分，构建一个干净、轻量级、速度极快的 web 应用程序。

medium.com](https://medium.com/@vpodk/clean-application-hamburger-menu-5736d4c459d9) 

感谢阅读这篇文章！如果你有任何问题，请在下面留言。另外，看看我以前的文章，你可能会喜欢:

[](https://medium.com/@vpodk/progressive-web-apps-and-their-advantages-70d3e119f165) [## 渐进式网络应用及其优势

### 许多人甚至不知道很多流行的日常应用实际上是进步的网络应用。

medium.com](https://medium.com/@vpodk/progressive-web-apps-and-their-advantages-70d3e119f165) [](https://medium.com/@vpodk/javascript-loose-equals-and-strict-equals-ab2144fcbe) [## JavaScript:宽松等于和严格等于

### 隐性强制是邪恶的、有害的吗？在某些情况下，是的！从广义上说，也不尽然。

medium.com](https://medium.com/@vpodk/javascript-loose-equals-and-strict-equals-ab2144fcbe)