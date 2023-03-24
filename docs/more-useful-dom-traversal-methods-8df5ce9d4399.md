# 更有用的 DOM 遍历方法

> 原文：<https://levelup.gitconnected.com/more-useful-dom-traversal-methods-8df5ce9d4399>

![](img/e2fc2686a823725aba44a0771e260cc5.png)

Beau Ulrich 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

客户端 JavaScript 的主要用途是动态操作网页。我们可以用 DOM 节点对象可用的 DOM 遍历方法和属性来做到这一点。

对于任何给定的元素来说，添加或更改子元素和兄弟元素都很容易，因为 DOM 节点对象中内置了属性来实现这一点。下面是 DOM node 对象获取父、子和兄弟节点或元素的方法。

下面是更有用的 DOM 遍历方法。

# hasChildNodes

`hasChildNodes`方法返回一个布尔值，表明它所调用的节点是否有子节点。节点包括所有类型的节点，如元素、文本和注释节点

例如，给定以下 HTML:

```
<div></div>
```

那么我们可以在它上面调用`hasChildNodes`如下:

```
const div = document.querySelector('div');
console.log(div.hasChildNodes())
```

我们应该得到`false`,因为 div 没有子节点。

另一方面，如果我们有:

```
<div>
  foo
</div>
```

然后`hasChildNodes`返回`true`，因为 div 中有一个文本节点。

# 插入之前

`insertBefore`方法让我们在该方法调用的节点之前添加一个节点。它需要两个参数。第一个是要插入的新节点。第二个是它所插入的新节点之前的节点，称为引用节点。

如果引用节点是`null`，那么它将被插入到子节点列表的末尾。

它返回 add 子节点，除非新节点是一个`DocumentFragement`。在这种情况下，只返回`DocumentFragement`。

例如，给定以下 HTML:

```
<div id='foo'>
  foo
  <div id='bar'>
    bar
  </div>
</div>
```

我们可以在 ID 为 foo 的 div 元素内的 ID 为 bar 的 div 之后添加一个新的 div 元素，方法是:

```
const foo = document.querySelector('#foo');const newDiv = document.createElement('div');
newDiv.textContent = 'new';foo.insertBefore(newDiv, null);
```

HTML 结构看起来像这样:

```
<div id="foo">
  foo
  <div id="bar">
    bar
  </div>
  <div>new</div>
</div>
```

在`insertBefore`被调用之后。

如果我们传入第二个参数，那么第一个参数中的元素将被插入到第二个参数之前。

例如，给定以下 HTML:

```
<div id='foo'>
  foo
  <div id='bar'>
    bar
  </div>
</div>
```

然后，我们可以通过编写以下内容，在具有相同级别 ID 栏的元素之前插入一个元素:

```
const foo = document.querySelector('#foo');
const bar = document.querySelector('#bar');const newDiv = document.createElement('div');
newDiv.textContent = 'new';foo.insertBefore(newDiv, bar);
```

然后我们在调用`insertBefore`后得到下面的 HTML:

```
<div id="foo">
  foo
  <div>new</div>
  <div id="bar">
    bar
  </div>
</div>
```

# **isEqualNode**

`isEqualNode`方法检查两个节点是否相等。当它们具有相同的类型时，它们是相等的，定义了像 ID、子元素的数量、相同的属性等特征。用于比较的数据点因节点类型而异。

它有一个参数，就是我们要比较的节点。

例如，给定以下 HTML:

```
<div id='foo'>
  foo
</div>
<div id='bar'>
  bar
</div>
```

然后我们可以编写下面的 JavaScript 来检查 ID 为 foo 的 div 和 ID 为 bar 的 div 是否相同:

```
const foo = document.querySelector('#foo');
const bar = document.querySelector('#bar');console.log(foo.isEqualNode(bar));
```

`console.log`应该记录`false`，因为它们没有相同的 ID 或内容。

另一方面，如果我们有下面的 HTML:

```
<div class='foo'>
  foo
</div>
<div class='foo'>
  foo
</div>
```

然后我们可以通过书写来比较它们:

```
const foo1 = document.querySelector('.foo:nth-child(1)');
const foo2 = document.querySelector('.foo:nth-child(2)');console.log(foo1.isEqualNode(foo2));
```

并且`console.log`应该记录`true`，因为它们有相同的类值和内容。

![](img/44dfa39aa7ffb9c4563d81df17d7d80b.png)

罗纳德·吉耶曾在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# **isSameNode**

`isSameNode`方法检查两个节点对象是否引用同一个节点。

它接受一个参数，即要测试的另一个节点，并返回一个布尔值，表明它们是否相同。

例如，给定以下 HTML:

```
<div>
  foo
</div>
```

我们可以这样称呼`isSameNode`:

```
const div = document.querySelector('div');console.log(div.isSameNode(div));
```

另一方面，如果我们有:

```
<div>
  foo
</div>
<div>
  foo
</div>
```

那么下面对`isSameNode`的调用将返回`false`:

```
const div1 = document.querySelector('div:nth-child(1)');
const div2 = document.querySelector('div:nth-child(2)');console.log(div1.isSameNode(div2));
```

这是因为尽管它们看起来一样，但它们并不引用同一个节点。

# 使标准化

`normalize`方法通过删除相邻的文本节点来清理节点的子树。

例如，如果我们有以下 HTML:

```
<div>
  foo
</div>
```

然后在调用`normalize`之前，我们得到在添加 2 个文本节点作为 div 的子节点之后，div 元素中有 3 个子节点。在我们调用它之后，它将子节点的数量减少到 1。

我们在下面的代码中调用 as:

```
const div = document.querySelector('div');
div.appendChild(document.createTextNode("foo "));
div.appendChild(document.createTextNode("bar"));console.log(div.childNodes.length);
div.normalize();
console.log(div.childNodes.length);
```

第一个`console.log`应该记录 3，第二个应该记录 1。

# 移除孩子

要从给定的节点中移除子节点，我们可以使用`removeChild`方法从 DOM 树中移除子节点。

它接受给定节点的子元素作为参数，然后返回从 DOM 树中移除的节点。

例如，假设我们有以下 HTML:

```
<div id='foo'>
  foo
  <div id='bar'>
    bar
  </div>
</div>
```

我们可以通过编写以下代码来删除带有 ID 栏的 div:

```
const foo = document.querySelector('#foo');
const bar = document.querySelector('#bar');foo.removeChild(bar);
```

然后，我们应该不再看到浏览器窗口中的“酒吧”。

它只适用于子节点。例如，如果我们有:

```
<div id='foo'>
  foo
</div>
<div id='bar'>
  bar
</div>
```

并运行相同的代码，那么我们将得到错误“unccatd DOM exception:未能在“Node”上执行“removeChild”:要删除的节点不是此节点的子节点。

如果节点不存在，它也会抛出一个错误。

# 替换孩子

`replaceChild`方法用参数中给出的第二个替换当前的。

例如，如果我们有以下 HTML:

```
<div id='foo'>
  foo
  <div id='bar'>
    bar
  </div>
</div>
```

然后，我们可以创建一个新元素来替换带有 ID 栏的 div:

```
const foo = document.querySelector('#foo');
const bar = document.querySelector('#bar');const baz = document.createElement('div');
baz.textContent = 'baz';
foo.appendChild(baz);foo.replaceChild(baz, bar);
```

第一个参数包含新元素，第二个参数包含原始元素。

它也适用于使用已经存在的元素替换另一个已经存在的元素。

例如，如果我们有下面的 HTML:

```
<div id='foo'>
  foo
  <div id='bar'>
    bar
  </div>
  <div id='baz'>
    baz
  </div>
</div>
```

然后我们可以写:

```
const foo = document.querySelector('#foo');
const bar = document.querySelector('#bar');
const baz = document.querySelector('#baz');foo.replaceChild(baz, bar);
```

用 ID 为 baz 的 div 替换 ID 为 bar 的 div。

DOM 遍历和操作方法非常有用。我们可以使用它们来检查它们是否有相同的内容或者引用相同的元素。此外，还有替换节点和清理无关文本节点的方法。还有一些插入和删除节点的简便方法。