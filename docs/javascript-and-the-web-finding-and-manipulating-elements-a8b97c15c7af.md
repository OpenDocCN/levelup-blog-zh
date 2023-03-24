# JavaScript 和网络——寻找和操作元素

> 原文：<https://levelup.gitconnected.com/javascript-and-the-web-finding-and-manipulating-elements-a8b97c15c7af>

![](img/87a073a2ddd516d887ea154264c18d0d.png)

娜塔莉·布雷兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

JavaScript 是世界上最流行的编程语言之一。为了有效地使用它，我们必须了解它的基本知识。

在本文中，我们将看看如何在我们的 web 应用程序中使用 JavaScript。

我们看看如何用 JavaScript 查找和操作元素。

# 查找元素

除了能够在 DOM 节点中导航之外，我们还可以直接在页面中找到元素，

这通常比遍历具有节点属性的元素更有用。

节点属性只允许我们遍历位置上彼此相关的元素。

例如，我们可以使用`getElementsByTagName`方法通过标签名获取元素。

我们可以通过写下以下内容来获得第一个`body`中的`a`元素:

```
const link = document.body.getElementsByTagName("a")[0];
```

我们在`document.body`上调用了`getElementsByTagName`，所以它只会找到`body`标签中的`a`元素。

它返回一个 NodeList，所以我们使用索引 0 来获取第一个。

同样，我们可以使用`getElementById`通过 ID 获取元素。

例如，如果我们有下面的 HTML:

```
<p id='foo'>
  foo
</p>
```

我们可以写:

```
const foo = document.body.getElementById('foo');
```

ID 是指元素的`id`属性的值。

因此，将返回`p`元素。

# 更改文档

一旦我们用那些方法得到了我们想要的元素，我们就可以改变元素。

我们可以调用`remove`方法从当前父节点中移除一个元素。

调用`appendChild`来添加一个元素作为当前元素的最后一个子元素。

`insertBefore`将第一个参数中的节点插入第二个参数中的节点之前。

例如，如果我们有以下 HTML:

```
<p>
  a
</p>
<p>
  b
</p>
<p>
  c
</p>
```

然后我们可以在第一个`p`元素之前插入第三个`p`元素，方法是:

```
const paragraphs = document.body.getElementsByTagName("p");
document.body.insertBefore(paragraphs[2], paragraphs[0]);
```

我们通过带有`getElementsByTagName`的标签名称来获取`p`元素。

然后我们用第 3 个`p`元素和第 1 个元素调用`insertBefore`。

`replaceChild`用于将一个子节点替换为另一个子节点。

它以一个新节点作为第一个参数，并以第二个参数替换该节点。

`replacechild`和`insertBefore`都期望一个新节点作为它们的第一个参数。

# 创建节点

我们可以创建一个文本节点，用`document.createTextNode`方法创建一个文本节点。

例如，我们可以写:

```
const paragraphs = document.body.getElementsByTagName("p");
for (let i = paragraphs.length - 1; i >= 0; i--) {
  const text = document.createTextNode('foo')
  const p = paragraphs[i];
  p.parentNode.replaceChild(text, p);
}
```

我们向后遍历了`p`节点，并用`foo`替换它们。

这必须向后进行，因为节点会随着文档的变化而更新。

如果我们从头开始，删除第一个图像会导致列表丢失元素。

该长度将减少 1。

我们可以使用`document.createElement`方法来创建新的节点。

它接受一个标记名，并返回一个给定类型的新的空节点。

例如，我们可以写:

```
const a = document.createElement('a')
a.innerText = 'example';
a.href = 'http://example.com';document.body.appendChild(a)
```

我们创建一个新的`a`元素，链接文本‘example’和`href`属性设置为`http://example.com`。

然后我们把它贴在身体上。

# 属性

像`href`这样的一些属性可以通过元素的 DOM 对象上的同名属性来访问。

这是大多数标准属性的情况。

HTML 允许我们设置任何想要的属性，所以我们可以为数据存储自定义属性。

我们可以使用`getAttribute`和`setAttribute`方法来存储属性值。

例如，我们可以写:

```
const para  = document.body.getElementsByTagName("p")[0];
para.setAttribute('data-foo', 'bar');
```

我们将 p 元素的`data-foo`属性设置为`bar`。

然后，我们可以通过写下以下内容来获得属性:

```
console.log(para.getAttribute('data-foo'));
```

我们得到了那个值。

我们只需给属性加上前缀`data`来表示我们用属性存储数据。

![](img/5af90fa09d159b95e59632256db48183.png)

[Olia Gozha](https://unsplash.com/@olia?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们可以用`getElementsByTagName`和`getElementById`找到元素。

`getAttributes`让我们获取任何属性，而`setAttribute`让我们设置任何属性。

我们可以使用`insertBefore`、`appendChild`和更多来在任何我们想要的地方附加节点。

元素组作为节点列表而不是数组返回。