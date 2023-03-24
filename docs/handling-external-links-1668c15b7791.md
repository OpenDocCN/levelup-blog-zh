# 处理外部链接

> 原文：<https://levelup.gitconnected.com/handling-external-links-1668c15b7791>

![](img/8915c3e0b9d98bc47efea313195ab61c.png)

在我参与的几乎每个 web 项目的 QA 过程中，总会有人提交这样的错误报告:

> 这些链接应该会在新窗口中打开。

我通常会尝试与他们讨论在新窗口中打开链接是一种意想不到的行为，这可能会令人不快，我们应该让用户决定如何使用他们的浏览器，[等等](https://css-tricks.com/use-target_blank/)，但我从未赢得这场战斗，最终总是实现`target="_blank"`。

不过，在我接手的一个项目中，一堆链接已经在一个新窗口中打开了。我在做可访问性审计和补救。为了保持链接在新窗口中打开并符合 WCAG 准则，在新窗口中打开的链接需要:

*   它们在新窗口中打开的视觉指示
*   文本指示链接将在屏幕阅读器的新窗口中打开
*   `rel="noopener noreferrer"`为[安全](https://web.dev/external-anchors-use-rel-noopener/)

# 设置

我没有通过梳理标记来添加这一点，而是创建了一个小的 JavaScript 模块来完成这三项。我是这样做的。

在我们的初始化中，我们需要两样东西:

1.  当前主机(例如，您当前在[https://Ben Robertson . io](https://benrobertson.io)
2.  页面上所有链接的列表。

用`window.location.host`可以轻松获得当前主机的 JavaScript。我们将用它来比较每个链接。

为了获取所有的链接，我们可以使用一个针对`href`属性的属性选择器。我们已经知道我们只需要外部 URL，所以我们将获取以`http`开头的 href 元素。

```
// This will grab every single <a> on the page
document.querySelectorAll('a');// This will grab only <a> elements that have
// a full url in the href, either http or https
document.querySelectorAll('a[href^="http"]');
```

# 解析 URL 数组

接下来，我们需要遍历每个数组，并确定是否需要对它做些什么。

```
// Create an array from all the links we found in the
// last step and loop through it.Array.from(this.links).forEach((link) => {
  // If the host doesn't match the current host,
  // that means it's an external link.
  // Otherwise do nothing.
  if (link.host !== this.host) {
    // Add the external link attributes
    link.rel = 'noopener noreferrer';
    link.target = '_blank';
    this.addIndicator(link);
  }
})
```

# 添加指标

对于这个特殊的站点，我们已经使用了[Bootstrap+glyphicon](https://getbootstrap.com/docs/3.3/components/)，所以这给了我们一个可以使用的漂亮图标。我们唯一需要做的就是在每个链接中添加一些带有正确类名的标记。我们还想添加一些屏幕阅读器文本(视觉上隐藏)，以便使用辅助技术的人知道链接也将在新窗口中更新。

```
addIndicator: function(link) {
  // Create a span element to add the icon to.
  var icon = document.createElement('span');

  // Add the icon classes.
  icon.classList.add(
    'glyphicon',
    'glyphicon-new-window',
    'glyphicon--small',
    'glyphicon--space-left'
  ); // Create another span that will add some screen reader
  // friendly text to announce that the window will
  // open in a new window.
  var span = document.createElement('span');
  span.textContent = 'new window';
  span.classList.add('screen-reader-text'); // Add the markup that we added to the page.
  icon.appendChild(span);
  link.appendChild(icon);
}
```

# 最终代码

这是所有的东西。将脚本添加到页面，然后调用`ExternalLinks.init()`添加标记和指示器。您也可以在库中打包类似这样的东西(我猜已经有一个或多个了！).

```
var ExternalLinks = {
  host: '',
  links: [], init: function() {
    // Get the current host
    this.host = window.location.host;

    // find all links
    this.links = document.querySelectorAll('a[href^="http"]'); // Parse all links
    this.parse();
  }, parse: function() {
    Array.from(this.links).forEach((link, index) => {
      if (link.host !== this.host) {
        link.rel = 'noopener noreferrer';
        link.target = '_blank';
        this.addIndicator(link);
      }
    })
  }, addIndicator: function(link) {
    var icon = document.createElement('span');
    icon.classList.add(
      'glyphicon',
      'glyphicon-new-window',
      'glyphicon--small',
      'glyphicon--space-left'
    ); var span = document.createElement('span');
    span.textContent = 'new window';
    span.classList.add('screen-reader-text'); icon.appendChild(span);
    link.appendChild(icon);
  }
};
```

*原载于 2020 年 8 月 9 日*[*https://Ben Robertson . io*](https://benrobertson.io/accessibility/handling-external-links)*。*