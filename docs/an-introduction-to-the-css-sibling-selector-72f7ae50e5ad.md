# CSS 同级选择器简介

> 原文：<https://levelup.gitconnected.com/an-introduction-to-the-css-sibling-selector-72f7ae50e5ad>

## 如何只使用 HTML 和 CSS 在鼠标悬停时显示视频标题

![](img/0add5befa372a3258344188ebeafc3e7.png)

同级选择器是 CSS 的一个强大特性，它允许我们在相邻的 HTML 元素之间创建交互性，而不需要 JavaScript。例如，你有没有想过如何使用 HTML 和 CSS 创建一个标题，当你把它悬停在视频上时，它只在视频上显示？

这个解决方案是现成的，并且内置在 CSS 中——兄弟选择器！

## 仅在用户悬停时显示视频标题

许多人在遇到这个问题时，会直接使用 JavaScript。虽然这是完全可以接受的，但为什么要添加事件监听器，并把仅用 2 行 CSS 和一个小的 HTML 更改就可以完成的事情复杂化呢？

首先，让我们创建一个显示视频的简单 HTML 页面:

```
<body>
    <div class=”video-wrapper”>
        <div class=”overlay-header”>
            <h2>Joker</h2>
        </div>
        <video class=”video” autoplay controls>
            <source src=”../../assets/movie.mp4" type=”video/mp4" />
            Your browser does not support HTML5 video.
        </video>
    </div>
</body>
```

用一些 CSS 来显示标题。

```
* {
  box-sizing: border-box;
}body {
  margin: 0;
  font-family: Arial;
  font-size: 17px;
}video {
  background-color: black;
  outline: none;
  position: fixed;
  right: 0;
  bottom: 0;
  width: 100%;
  height: 100%;
}.overlay-header {
  color: white;
  position: absolute;
}
```

我们现在成功地展示了一个带标题的视频。

现在让我们隐藏标题，将`.overly-header`类改为:

```
.overlay-header {
  display: none;
  color: white;
  position: absolute;
}
```

## 那么，我如何改变这一点，只显示在视频悬停？

嗯…我可以给视频标签`video:hover .overlay-header`添加一个悬停选择器，然后把我的标题放在视频标签里面。但是这破坏了我的语义 HTML，因为标题不是我的视频的一部分。

相反，我们可以使用兄弟选择器，正如它的名字所说，在相同的 CSS 深度选择元素，紧跟在目标元素之后。所以，`select + label`会找到元素`<select /> <label />`。

在我们的例子中，我们可以重新安排 HTML，将标题放在视频之后，然后像以前一样使用悬停选择器。导致:

```
<body>
    <div class=”video-wrapper”>
        <video class=”video” autoplay controls>
            <source src=”../../assets/movie.mp4" type=”video/mp4" />
            Your browser does not support HTML5 video.
        </video>
        <div class=”overlay-header”>
            <h2>Joker</h2>
        </div>
    </div>
</body>
```

和

```
* {
  box-sizing: border-box;
}body {
  margin: 0;
  font-family: Arial;
  font-size: 17px;
}video {
  background-color: black;
  outline: none;
  position: fixed;
  right: 0;
  bottom: 0;
  width: 100%;
  height: 100%;
}.overlay-header {
  display: none;
  color: white;
  position: absolute;
}.video:hover + .overlay-header {
  display: block;
}
```

## 最后润色

如果你将鼠标悬停在视频上，你会看到标题出现——万岁！然而，如果你把光标移到标题上，你会注意到它在闪烁。这是由于此时此刻，DOM 认为你不再悬停在视频上，而是现在的 h2 元素。要解决这个问题，将悬停效果也应用到`.overly-header`:

```
.overlay-header:hover,
.video:hover + .overlay-header {
  display: block;
}
```

## 摘要

为了在鼠标悬停时显示和隐藏标题，所涉及的代码是 2 行新的 CSS 和一个小的 HTML 更改——很简单！下面是一个[代码打开](https://codepen.io/adamjamesturner/pen/PowMLve)的工作示例。