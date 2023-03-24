# 有用的 DOM 遍历方法

> 原文：<https://levelup.gitconnected.com/useful-dom-traversal-methods-d2b55cf8e25c>

![](img/03c35276d727d903e23e68d6239cc7fb.png)

照片由[伦纳德·冯·比布拉](https://unsplash.com/@leonardvonbibra?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

客户端 JavaScript 的主要用途是动态操作网页。我们可以用 DOM 节点对象可用的 DOM 遍历方法和属性来做到这一点。

对于任何给定的节点，添加或更改子元素和兄弟元素都很容易，因为 DOM 节点对象中内置了执行这些操作的属性。下面是 DOM Node 对象获取父节点、子节点和兄弟节点或元素的方法。

# appendChild

`appendChild`方法让我们将一个子节点附加到一个给定的 HTML 元素上，作为当前节点的最后一个子节点。如果参数引用了 DOM 树上的现有节点，那么该节点将从其当前位置分离并附加到其新位置。

它接受一个参数，这是一个 DOM 节点对象。

例如，给定以下 HTML 中的两个现有节点:

```
<div id='foo'>
  foo
</div>
<div id='bar'>
  bar
</div>
```

我们可以将带有 ID 栏的元素作为子元素附加到带有 ID 栏的元素，如下所示:

```
const foo = document.querySelector('#foo');
const bar = document.querySelector('#bar');foo.appendChild(bar);
```

一旦我们运行它，我们应该得到如下结构:

```
<div id="foo">
  foo
  <div id="bar">
    bar
  </div>
</div>
```

我们还可以用它来创建一个动态创建的元素。例如，如果我们有以下 HTML:

```
<div id='foo'>
  foo
</div>
```

然后，我们可以编写以下代码，将一个新的`p`元素附加到 ID 为 foo 的 div:

```
const foo = document.querySelector('#foo');
const bar = document.createElement('p');
bar.textContent = 'bar';foo.appendChild(bar);
```

在上面的代码中，我们使用了`createElement`来创建一个新的`p`元素。然后我们设置`textContent`属性，在`p`元素中添加文本。最后，我们可以用`bar`作为参数在`foo`上`appendChild`附加`bar`作为`foo`的子节点。

# 克隆节点

`cloneNode`方法克隆一个节点对象，也可以克隆它的所有内容。默认情况下，它不会克隆节点的所有内容。

它有一个参数，这是一个可选参数，表示它是否是一个深度克隆，这意味着所有东西都将被克隆。`true`表示进行深度克隆，否则为`false`。

例如，给定以下 HTML:

```
<div>
  foo
</div>
```

我们可以编写以下 JavaScript 代码来克隆 div，然后将其作为最后一个子元素附加到`body`元素:

```
const fooDiv = document.querySelector('div');
const fooClone = fooDiv.cloneNode(true);
document.body.appendChild(fooClone);
```

我们将`true`传递给`cloneNode`方法来克隆一切。然后我们调用`document.body`上的`appendChild`,将克隆的对象作为参数传入，将其添加为`body`的子对象。

# 比较文档位置

`compareDocumentPosition`方法比较任意文档中给定节点与另一个节点的位置。它接受一个 DOM 节点对象作为它的参数。

它返回具有下列可能值的位掩码

*   `DOCUMENT_POSITION_DISCONNECTED` — 1
*   `DOCUMENT_POSITION_PRECEDING` — 2
*   `DOCUMENT_POSITION_FOLLOWING` — 4
*   `DOCUMENT_POSITION_CONTAINS` — 8
*   `DOCUMENT_POSITION_CONTAINED_BY` — 16
*   `DOCUMENT_POSITION_IMPLEMENTATION_SPECIFIC` — 32

例如，给定以下 HTML:

```
<div id='foo'>
  foo
</div>
<div id='bar'>
  bar
</div>
```

我们可以编写以下 JavaScript 来比较 ID 为 foo 的 div 和 ID 为 bar 的 div 的位置:

```
const foo = document.querySelector('#foo');
const bar = document.querySelector('#bar');const relativePos = foo.compareDocumentPosition(bar);
console.log(relativePos);
```

上面的代码应该为`relativePos`获得 us 4，这意味着 ID 为 bar 的元素跟随 ID 为 foo 的元素。

![](img/0a79f9172faddcb1944888d6f1d69b27.png)

由[汉娜·塔斯克](https://unsplash.com/@hannahtasker?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 包含

方法检查一个 DOM 节点是否在给定的节点中。它有一个参数，这是一个节点对象，我们要检查它是否在调用这个方法的对象中。

如果参数中的节点在被调用的节点内，则返回`true`，否则返回`false`。

例如，给定以下 HTML:

```
<div id='foo'>
  foo
</div>
<div id='bar'>
  bar
</div>
```

然后我们可以编写下面的 JavaScript 来检查 ID 为 bar 的 div 是否在 ID 为 foo 的 div 内部:

```
const foo = document.querySelector('#foo');
const bar = document.querySelector('#bar');const fooContainsBar = foo.contains(bar);
console.log(fooContainsBar);
```

当然，`fooContainsBar`应该是`false`，因为 ID 为 foo 的 div 不在 ID 为 bar 的 div 内。

另一方面，如果我们用下面的 HTML 代替:

```
<div id='foo'>
  foo
  <div id='bar'>
    bar
  </div></div>
```

然后使用与第一个例子相同的 JavaScript 代码，`fooContainsBar`应该是`true`，因为 ID 为 foo 的 div 在 ID 为 bar 的 div 内部。

# getRootNode

`getRootNode`方法返回节点对象的根，如果影子根可用的话，还可以包含影子根。

它为具有以下属性的对象提供一个可选参数:

*   `composed` —一个布尔值，表示应该包括阴影根。默认为`false`

它返回的节点要么返回给定节点的根元素，要么返回影子 DOM 中元素的影子根。

例如，如果我们有以下 HTML:

```
<div id='foo'>
  foo
</div>
```

那么我们可以如下调用`getRootNode`方法:

```
const foo = document.querySelector('#foo');const root = foo.getRootNode();
console.log(root);
```

我们应该将 HTML 文档作为根节点，因为它不在影子 DOM 中。

该根将是 Web 组件的影子根。例如，如果我们有以下 web 组件:

```
customElements.define('custom-p',
  class extends HTMLElement {
    constructor() {
      super(); const pElem = document.createElement('p');
      pElem.id = 'p-element';
      pElem.textContent = 'shadow p' const shadowRoot = this.attachShadow({
        mode: 'open'
      });
      shadowRoot.appendChild(pElem);
    }
  }
);
```

我们在 HTML 代码中添加了 Web 组件:

```
<custom-p></custom-p>
```

然后，我们可以通过编写以下代码获得 ID 为`p-element`的元素的根节点:

```
const pElement = document.querySelector('custom-p').shadowRoot.querySelector('#p-element');
const rootNode = pElement.getRootNode();
console.log(rootNode);
```

首先，我们获得自定义元素，然后我们获得`shadowRoot`属性，该属性允许我们访问我们的`custom-p` web 组件的影子 DOM。然后我们就可以得到 ID 为`p-element`的`p`元素，带有阴影根。

之后，我们通过调用代表 ID 为`p-element`的`p`元素的`pElement`上的`getRootNode`来获得它的根节点。

`console.log`应该能让我们得到 Web 组件的影子根。

# 结论

使用这些 DOM 遍历方法，我们可以在简单的情况下随意设置节点。此外，还有一些方法来检查一个元素是否是另一个元素的子元素，以及获取给定元素的根节点。