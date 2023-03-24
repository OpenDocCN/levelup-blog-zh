# 前端:如何重置你的 CSS 文件的简称

> 原文：<https://levelup.gitconnected.com/front-end-a-short-for-how-to-reset-your-css-files-9987404060ee>

![](img/7edd8ed8a1ce29d6f72100c0c4fdec8d.png)

默认情况下，浏览器有一个“用户代理”样式表，以便在浏览器中呈现一个没有样式的网页，使其对来访用户来说更容易阅读。

例如，大多数浏览器将链接设为蓝色，将访问过的链接设为紫色。以及设置 h1、h2、h3、h4、h5 和 h6 标题的大小，以及段落的大小。还设置了特定的填充量和边距。

重新设置样式表的“最佳”方式是在需要时将元素单独添加到 CSS rest 中。如果您确实选择使用通用选择器( ***)** 来重置您的样式，是的，这是一个更简单的方法来完成这个，但是缺点是您将它应用到您可能创建的任何新类，如果您开始有 50 个甚至 1，000 个类选择器，会导致加载时间变慢！

我想提到的重置打包框建模是可以应用到元素的属性样式，它重置了浏览器计算内容、填充、边框和边距的方式。这个属性叫做*框大小*你可以设置*内容框*和*边框*之间的值。两者的区别在于*内容框*关注元素的宽度，而*边框*负责添加到样式中的边框或填充。如果有人想看文档，这里有一个[链接](https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing)。

除了那些复制粘贴类型的文件之外，这里还有元素的重置 CSS 文件:

```
/* http://meyerweb.com/eric/tools/css/reset/     
v2.0 | 20110126    
License: none (public domain) */html, body, div, span, applet, object, iframe, h1, h2, h3, h4, h5, h6, p, blockquote, pre, a, abbr, acronym, address, big, cite, code, del, dfn, em, img, ins, kbd, q, s, samp, small, strike, strong, sub, sup, tt, var, b, u, i, center, dl, dt, dd, ol, ul, li, fieldset, form, label, legend, table, caption, tbody, tfoot, thead, tr, th, td, article, aside, canvas, details, embed,  figure, figcaption, footer, header, hgroup,  menu, nav, output, ruby, section, summary, time, mark, audio, video {margin: 0;  
padding: 0;  
border: 0;  
font-size: 100%;  
font: inherit;  
vertical-align: baseline;}/* HTML5 display-role reset for older browsers */ article, aside, details, figcaption, figure,  footer, header, hgroup, menu, nav, section {display: block;}body {  
line-height: 1; 
}ol, ul {  
list-style: none; 
}blockquote, q {  
quotes: none; 
}blockquote:before, blockquote:after, q:before, q:after {  
content: '';  
content: none; 
}table {  
border-collapse: collapse;  border-spacing: 0; 
}
```

你也可以在这里获取文件[的副本。](https://meyerweb.com/eric/tools/css/reset/reset.css)