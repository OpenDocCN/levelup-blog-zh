# DOM 操作— HTML 元素

> 原文：<https://levelup.gitconnected.com/dom-manipulation-html-elements-21143c3fda77>

![](img/81c139f209a4e195c06571930c971597.png)

Andrew Small 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是世界上最流行的编程语言之一。为了有效地使用它，我们必须了解它的基本知识。

在本文中，我们将看看如何操作 HTML 元素。

# html 元素

`HTMLElement`是一个构造函数，用于创建表示 HTML 文档中节点的对象。

它包括以下子构造函数:

*   `HTMLHtmlElement`
*   `HTMLHeadElement`
*   `HTMLLinkElement`
*   `HTMLTitleElement`
*   `HTMLMetaElement`

还有很多。

它们代表页面上的各种元素。

# HTMLElement 对象属性和方法

实例有许多属性和方法让我们操作它们。

我们可以使用`createElement`来创建一个元素。

`tagName`获取标签名称。

`children`获取子元素。

`getAttributes`获取子属性

`setAttribute`设置一个属性。

`hasAttribute`检查元素是否存在。

`removeAttribute`删除一个属性。

`classList`是一个用来操纵类集合的对象。

`dataset`返回任何以`data-`开头的属性。

`attributes`有属性列表。

完整的名单在 https://developer.mozilla.org/en-US/docs/Web/API/element。

# 创建元素

我们可以用`createElement`方法创建元素。

例如，我们可以通过编写以下内容来创建输入:

```
const input = document.createElement('input');
document.body.appendChild(input);
```

然后我们在屏幕上看到一个输入元素。

# 获取元素的标记名

属性返回一个元素的名称。

它返回的值与`nodeName`返回的值相同。

标签名称将以大写形式返回。

例如，如果我们有:

```
<a href="http://example.com">hello</a>
```

然后:

```
console.log(document.querySelector('a').tagName);
```

圆木`'A'`。

# 获取元素属性的列表或集合

属性返回一个元素当前已经定义的属性节点的集合。

如果我们有:

```
<a href="http://example.com" title="example" data-foo="dataFoo" class="example">hello</a>
```

然后，我们可以通过编写以下内容将所有节点放入一个数组中:

```
const attrs = [...document.querySelector('a').attributes];
```

我们使用 spread 操作符将属性类似数组的对象转换成一个数组。

返回的属性是活动的，这意味着内容可以随时更改。

由`attributes`返回的实际对象是一个`NameNodeMap`，它是一个类似数组的对象。

它有一个`length`属性。

布尔属性显示在`attributes`列表中，但没有值。

# 获取、设置和删除元素的属性值

我们可以使用`getAttribute()`、`setAttribute()`和`removeAttribute()`方法来操作属性。

例如，如果我们有:

```
<a href="http://example.com" title="example" data-foo="dataFoo" class="example">hello</a>
```

然后我们可以调用:

```
const a = document.querySelector('a');
a.removeAttribute('href');
```

删除`href`属性。

我们可以写:

```
a.setAttribute('href', '#');
```

设置`href`属性。

# 验证元素具有特定属性

要检查一个元素是否有特定的属性，我们可以使用`hasAttribute`方法。

例如，如果我们有:

```
<a href='http://example.com' title="example" data-foo='bar' class="example"></a>
```

然后，我们可以通过编写以下代码来检查它是否具有给定的属性:

```
const a = document.querySelector('a');
a.hasAttribute('title');
```

如果属性存在，无论它是否有值，该方法都将返回`true`。

# 获取类属性值列表

我们可以使用`classList`属性来获取 class 属性的值。

例如，如果我们有:

```
<div class="foo bar baz"></div>
```

我们可以通过写出以下内容来获得类名:

```
const div = document.querySelector('div');
console.log(div.classList);
```

这将在一个类似数组的对象中分别返回每个类的名称

还有一个属性`className`将它们全部返回到一个字符串中:

```
console.log(div.className);
```

# 在类属性中添加和删除子值

`classList`对象有一个`add`用于将类名添加到 class 属性中。

它也有`remove`方法来移除它。

例如，如果我们有:

```
<div class="foo"></div>​
```

然后我们可以写:

```
const div = document.querySelector('div');
div.classList.add('bar');
```

用`foo`增加`bar`。

并且:

```
div.classList.remove('foo');
```

移除`foo`并将`bar`留在类属性中。

# 切换类别属性值

`classList`也有一个`toggle`方法来切换一个类名。

例如，如果我们有:

```
<div class="foo"></div>​
```

然后我们可以写:

```
const div = document.querySelector('div');
div.classList.toggle('foo');
```

关闭它，我们可以用相同的类名再次调用`toggle`来添加它。

![](img/afb18d434dc798e707b714d4a958b283.png)

[食客集体](https://unsplash.com/@eaterscollective?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

DOM 对象有我们可以检索的属性和操作它的各个部分的方法。

有一些方法可以操作属性、更改类名等等。