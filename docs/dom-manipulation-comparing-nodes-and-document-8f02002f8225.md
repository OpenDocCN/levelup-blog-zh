# DOM 操作—比较节点

> 原文：<https://levelup.gitconnected.com/dom-manipulation-comparing-nodes-and-document-8f02002f8225>

![](img/882732208279827dc2dda4ed7151fab0.png)

罗宾·斯蒂克尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是世界上最流行的编程语言之一。为了有效地使用它，我们必须了解它的基本知识。

在本文中，我们将了解如何比较节点。

# 检查两个节点是否相同

我们可以根据 DOM 3 规范检查两个节点是否相同。根据规范，对于被视为相同的两个节点，这两个节点必须是相同的类型。

`nodeName`、`localName`、`namespaceURI`、`prefix`和`nodeValue`的值必须相同。

`NameNodeMaps`属性必须相等。`children`节点列表也必须相等。

标准化可能会影响到`children`中的条目，因此应该在比较它们之前完成。

`isEqualNode`方法可以为我们做所有的检查。

例如，如果我们有:

```
<div id='foo'>
  <p>
    foo
  </p>
  <p>
    bar
  </p>
</div>
```

我们可以写:

```
const p = document.querySelectorAll('div > p')
console.log(p[0].isEqualNode(p[1]));
```

检查两个 p 元素是否相同。

它们不是，因为它们有不同的内容，所以它记录`false`。

如果我们有:

```
<div id='foo'>
  <p>
    foo
  </p>
  <p>
    foo
  </p>
</div>
```

然后相同的代码记录`true`，因为 p 元素是相同的。

# 文档节点

`HTMLDocument`构造函数从`document`继承而来，代表一个特定的文档节点。

# 文档属性和方法

`HTMLDocument`实例的属性和方法包括以下内容:

*   `doctype`
*   `documentElement`
*   `implementation`
*   `activeElement`
*   `body`
*   `head`
*   `title`
*   `lastModified`
*   `referrer`
*   `URL`
*   `defaultview`
*   `compatMode`
*   `ownerDocument`
*   `hasFocus()`

`doctype`有文档的 doctype。

`documentElement`拥有整个文档。

`implementation`拥有`DOMImpkementation`对象，该对象拥有一些不依赖于任何特定文档的方法。

`activeElement`具有活跃的元素。

`body`拥有文档的主体。

`title`有`title`元素。

`lastModified`有最后修改日期。

`referrer`有链接到页面的 URL。

`URL`是当前页面的网址。

`defaultView`是指与文档相关联的`window`对象。

`compatMode`表示文件是处于特殊模式还是标准模式，

`ownerDocument`拥有该节点的顶层文档对象。

`hasFocus`返回元素是否有焦点。

# 获取常规 HTML 文档信息

我们可以通过编写以下内容来获取关于文档的一般信息:

```
const {
  title,
  URL,
  referrer,
  lastModified,
  compatMode
} = document;console.log(
  title,
  URL,
  referrer,
  lastModified,
  compatMode
)
```

我们从`document`对象本身获得一般信息。

# 记录子节点

`document`可以包含一个`DocumentType`节点对象和一个`Element`节点对象。

大多数文件的`DocumentType`应为`<!doctype html>`。

子元素应该是`html`元素本身。

我们可以使用`document.childNodes`来获得那些元素。

# 文档类型、html、标题和正文的快捷方式

`document`对象具有直接获取这些节点的属性。

`document.doctype`指`<!doctype html>`。

`document.documentElement`是指`<html>`元素。

`document.head`指`<head>`,`document.body`指`<body>`。

# 检测 DOM 规范或特性

`document.implement.hasFeatures`方法让我们检查 DOM API 是否支持某些特性。

例如，我们可以写:

```
document.implementation.hasFeature('Core','3.0');
```

检查我们的浏览器是否实现了 DOM Core 3.0 规范。

支持以下功能:

*   核心—在 1.0、2.0 和 3.0 版中受支持
*   XML — 1.0、2.0、3.0
*   HTML — 1.0，2.0
*   视图— 2.0
*   样式表— 2.0
*   CSS — 2.0
*   CSS2–2.0
*   事件— 2.0、3.0
*   UIEvents — 2.0、3.0
*   鼠标事件— 2.0、3.0
*   突变事件— 2.0、3.0
*   html 事件— 2.0
*   范围-2.0
*   遍历-2.0
*   验证— 3.0

我们不应该信任`hasFeature`去检测特征。

`isSupported`也可以对支持节点做同样的检查一起支持的特性:

```
element.isSupported(feature, version)
```

其中`element`是我们想要检查的特定元素。

![](img/5b23dc0af8400d2ef8a7643971ef2974.png)

[Eiliv-Sonas Aceron](https://unsplash.com/@shootdelicious?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 获取对聚焦/活动节点的引用

我们可以使用`document.activeElement`属性来获取当前处于焦点的元素。

# 检查文档或其中的任何节点是否有焦点

我们可以调用`document.hasFocus()`方法来检查页面中是否有元素被聚焦。

# 文档.默认视图

`document.defaultview`是浏览器中全局 JavaScript 对象的快捷方式。

这意味着`document.defaultView`是浏览器中的`window`对象。

# 结论

我们可以使用`isEqualNode`方法检查两个节点是否相同。

`activeElements`获取焦点节点。

我们可以通过使用`document.defaultview`来获取`window`对象。