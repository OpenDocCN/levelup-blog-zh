# DOM 操作—更改节点

> 原文：<https://levelup.gitconnected.com/dom-manipulation-changing-nodes-184ab4b63ed1>

![](img/bd12dd8a529bcfbfeeb8fd241f2456f2.png)

照片由[阿曼达·卡瑞拉](https://unsplash.com/@amandakariella?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是世界上最流行的编程语言之一。为了有效地使用它，我们必须了解它的基本知识。

在本文中，我们将研究如何创建和操作节点。

# 创建元素和文本节点

有两种方法可以让我们创建节点。一个是`createElement`来创建元素节点。还有创建文本节点的`createTextNode`方法。

我们可以使用`createAttribute`方法创建属性，使用`createComment`创建注释。

要创建一个元素，我们可以这样写:

```
const elementNode = document.createElement('div');
```

然后我们创建一个 div。

为了创建一个文本节点，我们编写:

```
const textNode = document.createTextNode('hello');
```

# 使用 JavaScript 字符串创建和添加元素和文本节点

我们可以通过用字符串设置`innerHTML`方法来添加节点。

例如，我们可以写:

```
document.getElementById('foo').innerHTML = '<strong>foo</strong>';
```

将一个节点放入下面的 div:

```
<div id="foo"></div>
```

我们有 ID 为`foo`的 div。

设置`innerHTML`属性将创建`strong`元素节点并将其放入 div。

要替换封闭节点，我们可以写:

```
document.getElementById('foo').outerText = 'foo';
```

用文本节点`'foo'`替换 div。

`insertAdjancentHTML`方法只适用于元素节点。这比设置`innerHTML`或创建一个新节点更精确。

例如，如果我们有下面的 HTML:

```
<div id='foo'>
  <p>
    foo
  </p>
</div>
```

我们可以写:

```
const foo = document.getElementById('foo');foo.insertAdjacentHTML('beforebegin', '<p>bar</p>');
```

然后我们在带有文本`foo`的 p 元素前添加一个 p 标签。位置是第一个参数，内容是第二个参数。

其他位置包括`'afterbwegin'`、`'beforeend'`和`'afterend'`。

`innerHTML`将字符串中的 HTML 元素转换成实际的 DOM 节点。

`textContent`只能构造文本节点。

# 将 DOM 树的一部分提取为 JavaScript 字符串

`innerHTML`、`outerHTML`和`textContent`是我们用来创建和添加节点的。

我们也可以用它们来获取内容。

例如，如果我们有:

```
<div id='foo'>
  <p>
    foo
  </p>
</div>
```

然后我们可以写:

```
console.log(document.getElementById('foo').innerHTML);
```

获取 div 的内容。

`innerText`会给我们文本节点。例如，我们可以写:

```
console.log(document.getElementById('foo').innerText);
```

为了得到`foo`。

# 向 DOM 添加节点对象

`appendChild`和`insertBefore`可以用来向 DOM 添加节点对象。

`appendChild`是给给定的元素增加一个子元素。

`insetBefore`让我们在当前元素之前添加一个元素。

例如，我们可以通过编写来使用`appendChild`:

```
const elementNode = document.createElement('p');
elementNode.innerText = 'hello';document.getElementById('foo').appendChild(elementNode);
```

假设我们有下面的 HTML:

```
<div id='foo'>
  foo
</div>
```

我们在文本`foo`后添加了一个新节点作为子节点。

`insertBefore`接受两个参数。第一个是要插入的元素。

第二个是我们要在第一个参数中的节点之前插入的元素。

我们之前插入的节点必须是我们调用`insertBefore`的节点的子节点。

所以给定下面的 HTML:

```
<div id='foo'>
  <p>
    foo
  </p>
</div>
```

我们可以这样写，在 p 元素之前插入一个元素:

```
const elementNode = document.createElement('p');
elementNode.innerText = 'hello';const foo = document.getElementById('foo')
foo.insertBefore(elementNode, foo.firstChild);
```

我们用 p 元素创建了一个 p 元素，然后将它传递给`insertBefore`方法。

`foo.firstChild`是 p 元素，因为父 div 是`foo`。

然后我们让`hello`显示在`foo`之前。

# 使用 removeChild()删除节点

要删除一个子元素，我们可以使用`removeChild`方法来删除一个子元素。

例如，给定以下 HTML:

```
<div id='foo'>
  <p>
    foo
  </p>
</div>
```

我们可以写:

```
const p = document.querySelector('p');
p.parentNode.removeChild(p);
```

从 div 中移除 p 元素。

我们得到了想要移除的元素的直接父元素，然后我们可以移除我们想要的元素。

# 用 replaceChild()替换子元素

`replaceChild`方法与`removeChild`相似。我们获取父项，然后调用`replaceChild`来替换该项。

例如，如果我们有:

```
<div id='foo'>
  <p>
    foo
  </p>
</div>
```

我们可以写:

```
const p = document.querySelector('p');
const bar = document.createTextNode('bar');p.parentNode.replaceChild(bar, p);
```

用包含`bar`的文本节点替换 p 元素。

第一个参数是我们想要用来替换现有节点的新节点。

第二个参数是我们想要替换的原始节点。

![](img/d39e602e1310bacf8af50b6663fcec5d.png)

兹登尼克·马赫亚克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

我们可以用内置的 DOM 节点方法创建文本和元素节点。

此外，我们可以通过获取直接的父元素来移除和替换子元素，然后调用方法来移除或替换我们想要的元素。