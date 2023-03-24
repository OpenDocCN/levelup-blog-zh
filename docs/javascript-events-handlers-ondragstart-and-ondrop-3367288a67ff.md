# JavaScript 事件处理程序:ondragstart 和 ondrop

> 原文：<https://levelup.gitconnected.com/javascript-events-handlers-ondragstart-and-ondrop-3367288a67ff>

## 了解 JavaScript 中拖放事件的工作原理

![](img/53f263f24af0917cf3d2b5712f327ea5.png)

格伦·汉森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 JavaScript 中，事件是应用程序中发生的动作。它们是由各种事情触发的，比如输入、提交表单、调整大小等元素变化，或者应用程序运行时发生的错误等。

我们可以为事件分配事件处理程序，这样当事件被触发时，我们就可以执行一个动作。发生在 DOM 元素上的事件可以通过为相应事件的 DOM 对象的属性分配一个事件处理程序来处理。在本文中，我们将看看`ondragstart`和`ondrop`事件处理程序。

# ondragstart 事件

HTML 元素的`ondragstart`属性让我们为当用户开始拖动元素或文本选择时触发的`dragstart`事件分配一个事件处理程序。例如，如果我们想跟踪一个元素何时开始被拖动，何时被放下，我们可以编写下面的 HTML 代码:

```
<p id='drag-start-tracker'>
</p><div id='drag-box' draggable="true">
</div><div id='drop-zone'>
</div>
```

在上面的代码中，我们有一个`p`元素来显示什么时候被拖动，以及被拖动元素的 ID。我们有一个 ID 为`drag-box`的元素正在被拖动。在它下面，我们有一个 ID 为`drop-`的`div`区域，它将接受任何被拖放到其中的元素。然后，我们可以添加以下 CSS 来样式化我们在上面添加的 HTML 元素:

```
#drag-box {
  width: 100px;
  height: 100px;
  background-color: red;
}#drop-zone {
  width: 200px;
  height: 200px;
  background-color: purple
}
```

我们可以看到`drag-box`是红色的`drop-zone`是紫色的`drop-zone`比`drag-box`大。接下来，我们可以将自己的事件处理函数分配给代表`drag-box`元素的 DOM 对象的`ondragstart`属性，以便在它被拖动时对其进行跟踪，并更新我们的`drag-start-tracker` `p`元素，以显示`drag-box`正在被拖动，代码如下:

```
const dragBox = document.getElementById('drag-box');
const dropZone = document.getElementById('drop-zone');
const dragStartTracker = document.getElementById('drag-start-tracker');dragBox.ondragstart = (e) => {
  dragStartTracker.innerHTML = `Element with ID ${e.target.id} is being dragged.`;
};dragBox.ondragend = (e) => {
  dragStartTracker.innerHTML = '';
  dropZone.appendChild(e.srcElement);
};
```

在上面的代码中，我们可以看到在拖动`drag-box`元素时,“带有 ID 的元素拖动框正在被拖动”,当我们停止拖动框并将其放入`drop-zone`元素时，我们应该会看到文本消失了，而`drag-box`元素停留在`drop-zone`元素内。它的工作原理是，当我们第一次开始拖动`drag-box`元素时，`dragstart`事件将被触发，而`dragBox.ondragstart`将被传入的`DragEvent`对象调用，该对象具有`e.target.id`属性，我们引用该属性来获取`drag-box`元素的 ID。

当我们在`drag-box`位于`drop-zone`上方时释放鼠标按钮，我们分配给`ondragend`事件处理程序的事件处理程序被调用，因为`dragend`事件是通过释放鼠标按钮触发的，结束了`drag-box`的拖动。

在函数内部，我们有一个在函数被调用时传入的`DragEvent`对象，我们可以使用`srcElement`来获取被拖动元素的 DOM 元素对象，所以我们可以使用`dropZone`对象的`appendChild`方法来追加`drag-box`元素，这是我们通过将`srcElement`属性添加到`drop-zone`元素中所得到的。

![](img/43b917a4377c4b96bed9f1b46f0e8637.png)

照片由 [Levi XU](https://unsplash.com/@xusanfeng?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# ondrop 事件

我们可以设置 DOM 元素的`ondrop`属性来处理该元素的`drop`事件。当一个元素或文本选择被放入一个有效的放置目标时，触发`drop`事件。例如，我们可以用它来制作一个可以拖到两个不同盒子的`div`元素，并使用`ondrop`事件处理程序来处理拖放操作。首先，我们添加以下 HTML 代码来制作可拖动的`div`元素和 2 个`div`元素，我们可以将可拖动的`div`元素放到以下代码中:

```
<div id='drag-box' draggable="true">
</div><div id='drop-zones'>
  <div id='drop-zone'>
  </div><div id='drop-zone-2'>
  </div>
</div>
```

然后我们可以添加一些 CSS 代码，用下面的代码来样式化`div`元素:

```
#drag-box {
  width: 100px;
  height: 100px;
  background-color: red;
}#drop-zone {
  width: 200px;
  height: 200px;
  background-color: purple
}#drop-zone-2 {
  width: 200px;
  height: 200px;
  background-color: green
}#drop-zones {
  display: flex;
}
```

在上面的代码中，我们通过添加一个 ID 为`drop-zones`的`div`来包含其中的 2 个`div`元素，从而使`drop-zone` `div`元素并排。我们使用`display: flex` CSS 代码并排显示`drop-zone`和`drop-zone-2`元素。然后我们改变每个 div 的背景颜色，这样我们就可以区分它们。接下来，我们添加 JavaScript 代码来处理`drag-box` `div`元素的拖放，并使用我们用以下代码定义的 with `ondrop`事件处理程序将其放入`drop-zone`元素中:

```
const dragBox = document.getElementById('drag-box');dragBox.ondragstart = (e) => {
  e
    .dataTransfer
    .setData('text/plain', event.target.id);
};document.ondragover = (e) => {
  e.preventDefault();
};document.ondrop = (e) => {
  const id = e
    .dataTransfer
    .getData('text');
  e.srcElement.appendChild(document.getElementById(id));
}
```

上面的代码通过处理`ondragstart`事件处理程序来获取被拖动元素的 ID。当用户开始拖动`drag-box` `div`时，调用`ondragstart`处理程序。在我们定义的`ondragstart`事件处理函数中，我们调用了`e.dataTransfer.setData`方法来设置`DataTransfer`对象的`'text'`属性，稍后当我们将`drag-box`放入`drop-zone`或`drop-zone-2` `div`元素中时，我们需要用到它。非常重要的是，我们拥有:

```
document.ondragover = (e) => {
  e.preventDefault();
};
```

这阻止了`ondragover`事件处理程序处理事件，因为一旦它被那个事件处理程序处理，那么`drop`事件就不会被触发，我们的`ondrop`事件处理程序也不会运行。这样一来，我们可以定义我们的`ondrop`事件处理程序，然后将它分配给`document.ondrop`属性来处理`document`的`drop`事件。

事件处理函数有一个`e`参数，它是一个`DragEvent`对象，它有一些有用的属性，我们可以用它们来处理`drag-box` 元素的删除。在该事件处理程序中，我们通过调用带有`'text'`字符串的`e.dataTransfer.getData`方法来获取我们正在拖动的元素的 ID。

然后我们可以使用`e`对象的`srcElement`属性来获取我们的`drag-box` `div`正在被删除的 DOM 元素，并使用`document.getElementById(id)`参数对其调用`appendChild`，其中`id`应该是`'drag-box'`，因为这是从`e.dataTransfer.getData(‘text’);`返回的内容，因为我们在`dragBox.ondragstart`事件处理程序中设置了`drag-box`元素的 ID。

# 包裹

`ondragstart`和`ondrop`属性对于在我们的网页中制作拖放功能非常有用。属性让我们为`dragstart`事件分配一个事件处理程序，当用户开始拖动一个元素或文本选择时就会触发这个事件。我们可以设置 DOM 元素的`ondrop`属性来处理该元素的`drop`事件。当一个元素或文本选择被放入一个有效的放置目标时，触发`drop`事件。

注意，我们已经为`document`的`ondragover`属性设置了一个事件处理函数，并在函数内部调用`e.preventDefault()`来阻止`ondragover`事件处理函数处理`dragover`事件，从而阻止`drop`事件的触发。这样一来，我们可以为`ondrop`属性分配一个事件处理程序，将 draggable 元素作为子元素添加到 drop target 元素中。