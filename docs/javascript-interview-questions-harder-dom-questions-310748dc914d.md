# JavaScript 面试问题—更难的 DOM 问题

> 原文：<https://levelup.gitconnected.com/javascript-interview-questions-harder-dom-questions-310748dc914d>

![](img/855a0007ed233cb9f18173db88213ef7.png)

由[托德·夸肯布什](https://unsplash.com/@toddquackenbush?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

为了得到一份前端开发人员的工作，我们需要搞定编码面试。

在本文中，我们将研究一些关于 DOM 操作的更难的问题。

# 如何检查一个元素是否是父元素的后代？

我们可以使用 DOM 元素的`parentNode`属性来检查一个元素是否是另一个元素的子元素。

例如，我们编写以下函数检查一个节点是否是一个子节点的父节点:

```
const isDescendant = (parent, child) => {
  while (child.parentNode) {
    if (child.parentNode == parent)
      return true;
    else
      child = child.parentNode;
  }
  return false;
}
```

在`isDescendant`函数中，我们用`parentNode`属性获取`child`的父节点，直到到达顶层。

在每个父节点中，我们检查`parentNode`属性是否引用了与`parent`相同的节点。

如果是这样，我们返回`true`。否则，如果我们发现`parentNode`与`parent`是同一个节点，我们将遍历祖父级、曾祖父级并一直到顶层，然后返回`true`。

否则，如果没有一个与`parent`相同，我们返回`false`。

例如，如果我们有以下 HTML:

```
<div>
  <p>
    <span></span>
  </p>
</div><div id='foo'></div>
```

那么如果我们有下面的代码:

```
const div = document.querySelector('div');
const p = document.querySelector('p');
const span = document.querySelector('span');
const foo = document.querySelector('#foo');console.log(isDescendant(div, p));
console.log(isDescendant(div, span));
console.log(isDescendant(foo, span));
```

前两个应该记录`true`，最后一个应该记录`false`，因为第一个 div 是 p 的父类和 span 的祖父类。

ID 为 foo 的 div 不是任何其他节点的父节点。

# innerHTML 和 appendChild 有什么区别？

`innerHTML`删除元素的所有当前子节点。然后 JavaScript 解释器解析该字符串，然后将解析后的字符串作为子元素分配给该元素。

`appendChild`顾名思义，将子节点附加到它所调用的父节点。

例如，如果我们有以下 HTML:

```
<div>
  <p>foo</p>
</div>
```

那么如果我们写:

```
const div = document.querySelector('div');
div.innerHTML = '<p>bar</p>';
```

然后我们只在屏幕上看到‘bar ’,因为我们设置了`innerHTML`属性来覆盖那里的内容。

另一方面，如果我们如下使用`appendChild`:

```
const div = document.querySelector('div');const p = document.createElement("p");
const bar = document.createTextNode("bar");
p.appendChild(bar);div.appendChild(p);
```

然后我们添加带有文本节点“bar”的 p 元素，并调用`appendChild`将`p`追加到`div`中，我们看到:

```
foobar
```

因为我们在 div 的子元素列表的末尾添加了一个新的子元素。

# 什么是 createDocumentFragment，为什么您可能会使用它？

`document.createDocumentFragment`让我们创建一个新的`DocumentFragment`，可以在其中添加 DOM 节点来构建一个屏幕外 DOM 树。

例如，如果我们有以下 HTML:

```
<ul></ul>
```

然后我们可以使用如下的`createDocumentFragment`来创建一个屏幕外的片段，然后将整个东西插入到 DOM 中:

```
const element = document.querySelector('ul');
const fragment = document.createDocumentFragment();
const fruits = ['apple', 'orange', 'grape'];fruits.forEach((fruit) => {
  const li = document.createElement('li');
  li.textContent = fruit;
  fragment.appendChild(li);
});element.appendChild(fragment);
```

使用`createDocumentFragment`，我们首先将所有节点添加到片段中，然后使用`appendChild`将整个内容添加到我们的`ul`元素中。

是 DOM 或 DOM 子树的轻量级或最小部分。这对于多次操作 DOM 的一部分很有用。

在内存中进行管理，这样 CPU 就不会被昂贵的操作所累。

![](img/d533e438b0d7664696289e4c10668a40.png)

照片由 [Cierra Woodard](https://unsplash.com/@cierrawoodard?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 什么是回流？什么导致了回流？我们如何减少回流？

重排是指我们根据屏幕大小或位置的变化来改变元素的位置。

如果我们改变屏幕的宽度，比如当我们旋转手机屏幕时，那么所有的东西都必须被移动以显示在屏幕属性中。

回流是昂贵的，因为所有的东西都要移动，尤其是像手机这样的小设备。

重排是由改变布局、调整窗口大小、改变任何元素的尺寸、以任何方式改变字体、移动 DOM 元素、添加或移除样式表以及任何其他类似的改变引起的。

为了避免重排，我们避免设置多个内嵌样式，将动画应用于固定或绝对位置的元素，并避免使用表格进行布局。

# 结论

我们可以使用节点的`parentNode`属性来获取节点的父节点。

设置元素的`innerHTML`属性会覆盖所有现有的子元素，并用我们设置的字符串中的新元素替换它们。

`appendChild`在子树的末尾附加一个新的子元素。

`DocumentFragments`是轻量级的屏幕外元素，包含 DOM 元素。它在内存中管理，可以像任何其他元素一样添加到屏幕上。

重排是根据屏幕上的一些其他变化来改变元素的位置。这是一个应该避免的昂贵的手术。