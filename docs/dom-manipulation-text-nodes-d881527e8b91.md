# DOM 操作—文本节点

> 原文：<https://levelup.gitconnected.com/dom-manipulation-text-nodes-d881527e8b91>

![](img/609dad5ecd3c36bb79d1c0ffc9e0a53b.png)

由 [corina ardeleanu](https://unsplash.com/@corina?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是世界上最流行的编程语言之一。为了有效地使用它，我们必须了解它的基本知识。

在本文中，我们将了解如何获取和操作文本节点。

# 创建和注入文本节点

我们可以通过使用`createTextNode`和调用`appendChild`来创建和注入文本节点，以将其作为当前节点的最后一个子节点。

例如，我们可以连接:

```
const textNode = document.createTextNode('hello');
document.querySelector('p').appendChild(textNode);
```

然后我们在 p 元素中得到`hello`。

然后我们可以用`innerText`得到文本节点。

我们可以写:

```
console.log(document.querySelector('p').innerText);
```

来获取文本。

# 获取文本节点值

我们可以使用`data`或`nodeValue`属性来获取文本节点中包含的文本。

例如，如果我们有:

```
<p>hello</p>
```

我们可以写:

```
console.log(document.querySelector('p').firstChild.data);
```

或者:

```
console.log(document.querySelector('p').firstChild.nodeValue);
```

才能拿到`hello` 。

要获得文本的长度，我们可以使用`length`属性，因为它返回一个字符串。

# 操纵文本节点

我们只是将字符串设置为`data`属性来改变文本节点。

给出以下内容:

```
<p>foo</p>
```

我们可以通过编写以下代码向文本节点插入更多数据:

```
document.querySelector('p').firstChild.data = 'bar';
```

将文本节点的值改为`'bar'`。

# 多个同级文本节点

浏览器组合了文本节点，因此不应出现多个文本节点。

然而，如果下一个节点有一个元素节点，那么它可能有一个兄弟节点。

例如，如果我们有:

```
<p>Hi, <strong>bob</strong> welcome!</p>
```

然后我们将节点`Hi,`和`welcome!`作为兄弟节点。

要获得兄弟，我们可以使用`nextSibling`属性。

我们可以写:

```
const p = document.querySelector('p');console.log(p.firstChild.data);
console.log(p.firstChild.nextSibling);
```

然后我们分别得到`Hi,`和`<strong>bob</strong>`。

例如，如果我们有:

```
<p>Hi</p>
```

那么如果我们写:

```
const p = document.querySelector('p');const text = document.createTextNode(' james')
p.appendChild(text);
```

添加文本节点。

那么如果我们写:

```
console.log(document.querySelector('p').childNodes.length);
```

为了记录文本中子节点的数量，我们得到 2。

# 移除标记并返回所有子文本节点

属性可以用来获取所有的子文本节点，并为特定的文本节点设置内容。

如果我们将它用作 getter，那么它将返回一个节点的所有文本内容，作为一个连接的字符串。

例如，如果我们有:

```
<h1>
  foo
</h1>
<p>
  <b>bar</b>
</p>
```

那么如果我们写:

```
console.log(document.body.textContent);
```

然后我们得到这些元素的内容和一些空白。

`textContent`也可用作二传手。我们可以用它来删除所有的子节点，并用一个文本节点替换它们。

例如，我们可以写:

```
document.querySelector('div').textContent = 'hi james!'
```

将 div 的文本节点设置为`hi james!`。

如果在文档或文档类型节点上使用了`textContent`，则返回`null`。

它还返回脚本和样式元素的内容。

# textContent 和 innerText 的区别

除了`Firefox`之外，大多数浏览器都支持一个与`textContent`类似的属性，叫做`innerText`。

它们不一样。

`innerText`意识到 CSS。所以我们隐藏了文本，`innerText`会忽略它。

`innerText`关心 CSS，所以如果我们设置它，它将触发回流。

`innerText`忽略脚本和样式元素中的文本节点。

`innerText`将对返回的文本进行规范化。

当`textContent`在 DOM 规范中时，它被认为是非标准的和特定于浏览器的。

# 将同级文本节点合并为一个

`normalize`方法让我们将兄弟文本节点合并成一个。

例如，如果我们有:

```
const p = document.createElement('p');
const hi = document.createTextNode('hi');
const james = document.createTextNode('james');
p.appendChild(hi);
p.appendChild(james);
document.body.appendChild(p);
```

然后，我们可以通过编写以下内容来组合这两个节点:

```
document.querySelector('p').normalize();
```

我们得到了:

```
hijames
```

在屏幕上。

# 拆分文本节点

我们可以用`splitText`拆分一个文本节点。

例如，如果我们有:

```
<div>
  hi james
</div>
```

我们可以写:

```
console.log(document.querySelector('div').firstChild.splitText(4).data)
```

然后我们记录`'james'`,因为我们从索引 4 开始，并且在字符串前有空格。

![](img/9811bf8eeb2bae84b39de657cc6926ac.png)

照片由[英达努尔](https://unsplash.com/@indaheart?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们可以通过设置`data`属性来设置文本节点内容。

`innerText`和`textContent`在很多方面都不一样。

同样，我们可以分别用`normalize`和`splitText`组合和拆分文本节点。

我们也可以用`createTextNode`创建文本节点，并用`appendChild`添加它们。