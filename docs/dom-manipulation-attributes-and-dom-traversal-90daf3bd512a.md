# DOM 操作—属性和 DOM 遍历

> 原文：<https://levelup.gitconnected.com/dom-manipulation-attributes-and-dom-traversal-90daf3bd512a>

![](img/febdaf14bba2f2949917f944fd1ac4bc.png)

戴维·坎特利在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是世界上最流行的编程语言之一。为了有效地使用它，我们必须了解它的基本知识。

在本文中，我们将了解如何处理一些 HTML 元素的方法和属性，以及如何遍历 DOM。

# 检查类属性值是否有特定值

我们可以使用`classList.contains`方法来检查一个类名是否在 class 属性的类名列表中。

例如，如果我们有:

```
<div class="foo bar baz"></div>​
```

然后我们可以写:

```
const div = document.querySelector('div');
console.log(elm.classList.contains('foo'));
```

然后它记录下`true`，因为`foo`在类属性中。

# 获取和设置数据属性

我们可以使用`datset`属性来设置任何以`data-`开头的属性。

例如，如果我们有:

```
<div data-foo-bar='foo'></div>​
```

然后我们可以写:

```
const div = document.querySelector('div');
console.log(elm.dataset.fooBar);
```

返回`data-foo-bar`的值。

HTML 中任何带有 kebab case 的内容都被转换成 camelCase。

要设置它，我们可以写:

```
elm.dataset.baz = 'baz'
```

然后我们在值为`baz`的 div 上得到一个`data-baz`属性。

# 选择特定的元素节点

获取对单个元素节点的引用的最常见方法是`querySelector`或`getElementById`。

`querySelector`用于用任何 CSS 选择器获取单个元素。

例如，我们可以写:

```
const div = document.querySelector('div');
```

要获得页面上的第一个 div，

我们可以使用`getElementById`来获得一个具有`id`属性集的元素。

例如，如果我们有:

```
<p id='foo'>foo</p>
```

然后我们可以使用`getElementById`通过写来获得 p 元素:

```
const foo = document.getElementById('foo');
```

# 选择或创建元素节点的节点列表

我们可以使用`querySelectorAll`、`getElementsByTagName`或`getElementsByClassName`来分别获取具有给定选择器、标签名或类名的多个元素。

例如，如果我们有:

```
<ul>
  <li class="item">foo</li>
  <li class="item">bar</li>
  <li class="item">baz</li>
</ul>
```

然后我们可以使用`querySelectorAll`通过写得到所有的李:

```
const lis = document.querySelectorAll('li');
```

为了使用`getElementsByTagName`做同样的事情，我们写:

```
const lis = document.getElementsByTagName('li');
```

同样，我们可以使用如下的`getElementsByClassName`来获得相同的元素:

```
const lis = document.getElementsByTagName('item');
```

从`getElementsByTagName`和`getElementsByClassName`创建的`NodeList`永远是活的。

这意味着即使文档被更新，它们也总是反映文档的状态。

`querySelectorAll`不返回元素的动态列表。

它返回文档创建时的快照。

所有方法也在元素节点上定义。

这样我们就能锁住他们。

例如，我们可以写:

```
const lis = document.getElementById('list').getElementsByTagName('li');
```

我们可以将`'*'`传递给`querySelectorAll`或`getElementsByTagName`来获取页面上的所有元素。

`childNodes`也会返回一个`NodeList`。

# 选择所有直接子元素节点

我们可以使用`children`来选择给定节点的所有子节点。

如果我们有:

```
<ul>
  <li class="item">foo</li>
  <li class="item">bar</li>
  <li class="item">baz</li>
</ul>
```

所以我们可以通过书写得到 li 元素:

```
const lis = document.querySelector('ul').children;
```

`children`给出了所有的直接元素节点，不包括其他类型的节点。

它们按照在 DOM 中出现的顺序返回。

# 上下文元素选择

我们可以在一个元素节点上调用`querySelector()`、`querySelectorAll()`、`getElementsByTagBName()`、`getElementsByClassName()`。

例如，如果我们有:

```
<ul>
  <li class="item">foo</li>
  <li class="item">bar</li>
  <li class="item">baz</li>
</ul>
```

我们可以写:

```
const lis = document.querySelector('ul').getElementsByTagName('li');
```

得到李元素。

# 元素节点的预配置选择/列表

在`document`对象中有许多属性可以返回选择节点，而不需要调用上面的方法。

`document.all`返回 HTML 文档中的所有元素。

`document.forms`返回 HTML 文档中的所有表单元素。

`document.images`返回文档中的所有 img 元素。

`document.links`返回文档中的所有 `a`元素。

`document.scripts`返回文档中的所有脚本元素。

返回 HTML 文档中的所有链接和样式对象。

他们返回对象，所以他们是活的。

`document.all`是从`HTMLAllCollection`构造函数创建的，在 Firefox 中不受支持。

![](img/73e4c2c9cbe8bcb1381383d49e465e19.png)

由 [Kouji 鹤](https://unsplash.com/@pafuxu?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 使用 matches()验证元素将被选中

我们可以使用`matches` 来检查一个元素的选择器是否匹配一个给定的元素。

例如，如果我们有:

```
<ul>
  <li class="item">foo</li>
  <li class="item">bar</li>
  <li class="item">baz</li>
</ul>
```

我们可以这样称呼它:

```
console.log(document.querySelector('.item').matches('li:first-child'));
```

它应该返回`true`，因为类`item`的第一个元素与`li`相同。

# 结论

我们可以用各种 DOM 属性和方法来获取和设置属性。

此外，我们可以用多种方式遍历 DOM。