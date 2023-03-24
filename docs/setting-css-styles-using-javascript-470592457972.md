# 使用 JavaScript 设置 CSS 样式

> 原文：<https://levelup.gitconnected.com/setting-css-styles-using-javascript-470592457972>

## 设置 CSS 样式的 4 种方法

![](img/35e0c51590c67138e4004e8b473a2f04.png)

由 [KOBU 机构](https://unsplash.com/@kobuagency?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 前言

开发页面通常使用 CSS 文件来设计样式。如果你去看网站的源代码，你可以发现它基本上包含了至少一个样式文件。CSS 文件适合处理不需要交互的演示文稿。如果需要根据用户行为改变元素在页面上的显示，就需要依靠 JavaScript 来改变元素的样式。下面将介绍 4 种通过 JavaScript 设置 CSS 样式的方法。

# 设置单个 CSS 样式

每个 dom 元素都有一个`style`属性，这是一个 JavaScript 对象。通过`style`对象，可以获取或设置用户设置的样式。

假设页面的背景色是白色，那么通过 CSS 文件初始设置给页面的样式是:

```
body { background-color: #000;}
```

如果我们需要将页面背景色修改为红色，我们可以通过以下方式使用`style`对象来实现:

```
document.body.style.backgroundColor = '#f00';
```

看起来很简单，但是有一天你发现样式集不起作用了。你这么聪明，打开 chrome 的 devtools 就发现问题了，原来的 CSS 样式文件被人修改了:

```
body { background-color: #000 !important;}
```

Javascript 设置的样式优先级不够，导致样式失败。那么有什么办法可以解决这个问题呢？
当然，`style`对象也提供了一个 API 来设置样式

```
element.style.setProperty(propertyName, value?, priority?);
```

第三个参数是设置 CSS 优先级，可以是`important`，未定义，空字符串。所以我们可以用它来设置 CSS 样式:

```
document.body.style.setProperty('background-color', '#f00', 'important' );
```

太酷了，页面显示又正常了。但是很快就会有新的变化，不仅需要改变页面背景颜色，还需要改变字体颜色和页边距，所以你可以马上想到:

```
document.body.style.backgroundColor = '#f00';document.body.style.color = '#0f0';document.body.style.margin = '20px';
```

随着越来越多的风格被改变，这是很难维持的。有什么方法可以批量设置属性？

# 批量设置 CSS 样式

`style`对象提供了一个`cssText`属性，支持设置多种 CSS 样式
如果需要修改页面的背景颜色，同时改变字体颜色和边距，可以使用:

```
document.body.style.cssText = "background-color: #f00; color: #0f0; margin: 20px;";
```

当然，你也可以像在 CSS 文件中一样设置属性的优先级:

```
document.body.style.cssText = "background-color: #f00 !important; color: #0f0; margin: 20px;";
```

另一个想法是通过构造 html 的样式标签来动态插入样式文件。并将需要修改的 CSS 样式包装成 CSS 类。

```
function styleInject(cssText) { const head = document.head || document.getElementsByTagName('head')[0]; const style = document.createElement('style'); style.type = 'text/css'; style.appendChild(document.createTextNode(cssText)); head.appendChild(style);}
// insert the css styles
styleInject(`
  body {
    background-color: #f00 !important; 
    color: #0f0; 
    margin: 20px;
  }
`);
```

这是一种动态插入 CSS 文件的方式，也在 Webpack 中使用。

# 结论

如果你需要使用 JavaScript 来修改一个元素的样式，有 4 种方法，你都掌握了吗？

1.element . style[属性名] =值

2.element.style.setProperty(属性名，值，优先级)

3.element . style . CSS text = values string

4.动态插入 html 的样式标签

如果是单一的 CSS 样式修改，建议使用前两种。如果需要批量修改，建议使用后两种。