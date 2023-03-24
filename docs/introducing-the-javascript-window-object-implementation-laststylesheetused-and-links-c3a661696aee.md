# 探索 JavaScript 窗口对象:document.implementation 和 document.links

> 原文：<https://levelup.gitconnected.com/introducing-the-javascript-window-object-implementation-laststylesheetused-and-links-c3a661696aee>

![](img/87236ccf680fc08d7d6edf2848c1c20e.png)

[皮特·鲁德沃尔](https://unsplash.com/@petterrudwall?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

`window`对象是一个全局对象，具有与当前 DOM 文档相关的属性。

在本文中，我们将查看`window.document`对象的属性，包括`implementation`和`links`属性。

# windw . document . implement

`document.implementation`属性返回一个`DOMImplementation`对象，该对象具有允许我们创建文档对象或 XMLDocument 对象的属性，并且还有一个创建 DocumentType 对象的方法。它还有一个`hasFeature`方法，返回一个特性是否被支持。然而`hasFeature`方法并不可靠，因为除了与 SVG 相关的特性之外，它几乎对所有的东西都返回`true`。

要创建一个 XMLDocument 对象，我们可以使用`document.implementation.createDocument`方法。它需要三个参数。第一个是名称空间 URI，它是包含 XML 文档的名称空间 URI 的字符串。第二个是具有限定名的字符串，限定名是一个可选的前缀和一个冒号加要创建的文档的本地根名称。第三个参数是可选的文档类型参数，它是包含 doctype 的节点，默认为`null`。例如，我们可以在下面的代码中使用它:

```
const doc = document.implementation.createDocument ('[http://www.w3.org/1999/xhtml'](http://www.w3.org/1999/xhtml'), 'html', null);
const body = document.createElementNS('[http://www.w3.org/1999/xhtml'](http://www.w3.org/1999/xhtml'), 'body');
body.setAttribute('id', 'hello');
body.innerHTML = 'hello';
doc.documentElement.appendChild(body);
console.log(body);
```

然后在`console.log`的最后一行，我们得到:

```
<body ae kf" href="http://www.w3.org/1999/xhtml" rel="noopener ugc nofollow" target="_blank">http://www.w3.org/1999/xhtml" id="hello">hello</body>
```

我们可以将上一个示例中创建的文档对象放入 IFrame 中，首先添加一个`iframe`元素，如下所示:

```
<iframe id='frame'></iframe>
```

然后，在我们的脚本中，我们可以通过添加以下内容来扩展上面的示例:

```
const doc = document.implementation.createDocument('[http://www.w3.org/1999/xhtml'](http://www.w3.org/1999/xhtml'), 'html', null);
const body = document.createElementNS('[http://www.w3.org/1999/xhtml'](http://www.w3.org/1999/xhtml'), 'body');
body.setAttribute('id', 'hello');
body.innerHTML = 'hello';
doc.documentElement.appendChild(body);const frame = document.getElementById("frame");
const destDocument = frame.contentDocument;
const srcNode = doc.documentElement;
const newNode = destDocument.importNode(srcNode, true);destDocument.replaceChild(newNode, destDocument.documentElement);
```

在上面的代码中。我们将 doctype 的名称空间、限定标记和`null`传递到第二行的`createDocument`方法中。

然后我们在第三行创建一个`p`元素，将内容设置为“hello”到`p`标签，然后我们将它附加到新创建的文档的`body`中，然后我们获取从`createHTMLDocument`方法创建的`doc`对象的`documentElement`，然后用`importNode`方法复制节点。

最后，在最后一行，我们用从`importNode`方法获得的文档节点替换了 iframe 的内容。

在运行了所有这些之后，我们应该在 IFrame 中得到单词“hello”。

`createDocumentType`方法让我们创建一个`DocumentType`对象。这可以作为第三个参数传递给`createDocument`，也可以通过`insertBefore()`或`replaceChild()`之类的方法放入文档。它需要三个参数。

第一个参数是限定名，类似于`svg:svg`。第二个是公共 ID，它是一个包含公共标识符的字符串，第三个是系统 ID，它是一个包含系统标识符的字符串。例如，如果我们有以下代码:

```
const doctype = document.implementation.createDocumentType('svg:svg', '-//W3C//DTD SVG 1.1//EN', '[http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd'](http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd'));const doc = document.implementation.createDocument('[http://www.w3.org/2000/svg'](http://www.w3.org/2000/svg'), 'svg:svg', doctype);console.log(doc.doctype);
console.log(doc.doctype.publicId);
console.log(doc.doctype.systemId);
```

然后我们回来了:

```
<!doctype svg:svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "[http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd](http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd)">-//W3C//DTD SVG 1.1//EN[http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd](http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd)
```

第一个是我们创建的 doctype。最后两个日志输出是我们传入的内容。

使用`createHTMLDocument`方法，我们可以创建一个 HTML 文档对象。它需要一个参数，即`title`，除了 IE 之外，它是可选的。它是一个字符串，代表我们给文档的标题。我们可以使用它来创建一个新的文档对象，然后通过首先在 HTML 文件中添加一个`iframe`元素将它放入 IFrame 中，如下面的代码所示:

```
<iframe id='frame'></iframe>
```

然后我们可以使用下面的代码中的`createHTMLDocument`方法创建 HTML 文档:

```
const frame = document.getElementById("frame");
const doc = document.implementation.createHTMLDocument("Hello");
const p = doc.createElement("p");
p.innerHTML = "hello";
doc.body.appendChild(p);
const destDocument = frame.contentDocument;
const srcNode = doc.documentElement;
const newNode = destDocument.importNode(srcNode, true);
destDocument.replaceChild(newNode, destDocument.documentElement);
```

在上面的代码中。我们将标题传递给第二行的`createHTMLDocument`方法。

然后我们在第三行创建一个`p`元素，将内容设置为`p`标签的“hello ”,然后我们将它附加到新创建文档的`body`,然后我们获取从`createHTMLDocument`方法创建的`doc`对象的`documentElement`,然后用`importNode`方法复制节点。

最后，在最后一行，我们用从`importNode`方法获得的文档节点替换了 iframe 的内容。

在所有这些都运行之后，我们应该在 IFrame 中得到单词“hello ”,当我们检查 IFrame 时，我们应该得到`title`标记应该有“Hello”。

`hasFeature`方法被否决了，它对于检查特性不是很有用。它接受两个参数，一个是要检查的特性的字符串，另一个是特定特性的版本。例如，如果我们有:

```
console.log(document.implementation.hasFeature('HTML', '5'));
```

它记录了`true`，因为这是该方法为大多数特性返回的内容。

![](img/ee11cd38248a70c3589659f541a93805.png)

由[迈克·阿隆佐](https://unsplash.com/@mikezo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# windw.document.links

`links`属性是一个只读属性，它包含一个文档对象的所有`area`和`a`元素，并设置了`href`属性的值。它返回一个 HTMLCollection，这是一个类似数组的对象，我们可以用`for...of`循环遍历它，或者用 spread 操作符将其转换为一个数组。例如，如果我们的 HTML 中有以下链接:

```
<a href='[http://google.com'](http://google.com')>Google</a>
<a href='[http://yahoo.com'](http://yahoo.com')>Yahoo</a>
<a href='[http://medium.com'](http://medium.com')>Medium</a>
```

然后我们可以像下面的代码一样用`for...of`循环遍历它们:

```
for (const link of document.links) {
  console.log(link);
}
```

我们还可以使用 HTMLCollection 对象的`item`方法通过索引获取每个链接。为此，我们可以编写以下代码:

```
for (let i = 0; i < document.links.length; i++) {
  console.log(document.links.item(i));
}
```

两个循环应该以相同的方式输出链接。我们还可以通过属性链接直接获取链接的属性，如下面的代码所示:

```
for (const link of document.links) {
  console.log(link.href);
}
```

然后我们从`console.log`语句中得到以下内容:

```
[http://google.com/](http://google.com/)
[http://yahoo.com/](http://yahoo.com/)
[http://medium.com/](http://medium.com/)
```

## 结论

对于`document`对象，我们有一些方便的属性，让我们通过使用它的方便属性来获取文档中的元素。`document.implementation`属性对于动态创建新文档并附加到其他元素(如`iframe`)非常方便。

这可以通过`createDocument`和`createHTMLDocument`方法完成。要从文档中获取所有的`a`和`area` 元素，并设置`href`属性的值，我们可以使用`links`属性。

我们可以用`for...of`循环遍历`document.links`条目。此外，HTMLCollection 对象附带了一个`item`方法，我们可以通过索引获得想要的元素。

此外，我们可以通过使用属性名称作为元素的属性来获取代码中的属性值，从而获取 HTMLCollection 元素的属性。