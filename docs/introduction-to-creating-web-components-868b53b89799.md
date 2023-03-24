# 创建 Web 组件简介

> 原文：<https://levelup.gitconnected.com/introduction-to-creating-web-components-868b53b89799>

![](img/bc6c47dc29204009977f8f2a996469b0.png)

罗宾·格劳泽在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

随着 web 应用变得越来越复杂，我们需要某种方式将代码分成易于管理的块。要做到这一点，我们可以使用 Web 组件来创建可以在多个地方使用的可重用组件。

Web 组件也与其他代码片段隔离开来，因此很难从其他代码片段中意外修改它们并创建冲突的代码。

在本文中，我们将看看 Web 组件的不同部分，以及如何创建基本部分。

# Web 组件的组成部分

Web 组件有 3 个主要部分。它们一起封装了可以随时重用的功能，而不用担心代码冲突。

*   自定义元素——一组 JavaScript APIs，允许我们定义自定义元素及其行为，我们可以根据需要在 UI 中使用它们
*   shadow DOM —一组 JavaScript APIs，用于附加元素的封装 shadow DOM 树。它与主文档 DOM 分开呈现。这保持了元素特性的私密性，因此可以编写它们的脚本，而不用担心与文档的其他部分冲突
*   HTML 模板——`template`或`slot`元素使我们能够编写不在呈现的页面上显示的标记模板。它们可以作为定制元素结构的基础被多次重用。

# 创建 Web 组件

为了创建 Web 组件，我们执行以下步骤:

1.  创建一个类或函数来指定 web 组件功能。
2.  使用`CustomElementRegistry.define()`方法注册我们的新定制元素，传递要定义的元素名和指定功能的类或函数，以及可选地传递它从哪个元素继承而来
3.  使用`Element.attachSHadow()`方法将阴影 DOM 附加到定制元素。添加子元素、事件侦听器等。使用普通的 DOM 方法添加到影子 DOM
4.  使用`template`和`slot`标签定义 HTML 模板。我们使用常规的 DOM 方法克隆模板，并将其附加到我们的影子 DOM。
5.  像使用其他常规 HTML 元素一样，在页面上的任何地方使用自定义元素

![](img/ef5ed25d9aee21d0577b800a92d67888.png)

[女仆米林基](https://unsplash.com/@lensilium?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 基本示例

`CustomElementRegistry`保存了一个已定义的定制元素列表。这个对象让我们在页面上注册新的定制元素，并返回关于注册了哪些定制元素的信息。

为了在页面上注册一个新的定制元素，我们使用了`CustomElementRegistry.define()`方法。它采用以下参数:

*   表示元素名称的字符串。要求在其中使用虚线。它们不能是单个单词。
*   定义元素行为的类对象
*   包含一个`extends`属性的可选参数，该属性指定我们的元素继承的内置元素(如果有的话)

我们可以如下定义自定义元素:

```
class WordCount extends HTMLElement {
  constructor() {
    super();
    const shadow = this.attachShadow({
      mode: 'open'
    });
    const span = document.createElement('span');
    span.textContent = this.getAttribute('text').split(' ').length;
    const style = document.createElement('style');
    style.textContent = 'span { color: red }';
    shadow.appendChild(style);
    shadow.appendChild(span);
  }
}customElements.define('word-count', WordCount);
```

在上面的代码中，我们通过调用`attachShadow`将一个影子 DOM 附加到我们的文档。`mode`设置为`'open'`意味着可以从根之外的 JavaScript 访问影子根。也可以是`'closed'`表示相反的意思。

然后我们创建一个`span`元素，在这里，我们将文本内容设置为`text`属性的`length`，只要有空格，我们就将其中的单词拆分出来。

接下来，我们创建了一个`style`元素，然后将内容设置为`color: red`。

最后，我们将它们都附加到影子 DOM 根。

注意，在我们的类中，我们扩展了`HTMLElement`。我们必须这样做来定义一个自定义元素。

我们可以如下使用我们的新元素:

```
<word-count text='Hello world.'></word-count>
```

有两种类型的自定义元素。它们是:

*   自主定制元素——它们是独立的，不从标准 HTML 元素继承。我们可以通过直接引用名称来创建它们。比如我们可以写`<word-count>`，或者`document.createElement("word-count")`。
*   定制的内置元素—这些元素继承自基本的 HTML 元素。我们可以扩展一个内置的 HTML 元素来创建这些类型的元素。我们可以通过编写`<p is="word-count">`或`document.createElement("p", { is: "word-count" })`来使用它们。

我们之前创建的定制元素是自治的定制元素。我们可以通过编写以下内容来创建定制元素:

```
class WordCount extends HTMLParagraphElement {
  constructor() {
    super();
    const shadow = this.attachShadow({
      mode: 'open'
    });
    const span = document.createElement('span');
    span.textContent = this.getAttribute('text').split(' ').length;
    const style = document.createElement('style');
    style.textContent = 'span { color: red }';
    shadow.appendChild(style);
    shadow.appendChild(span);
  }
}customElements.define('word-count', WordCount, {
  extends: 'p'
});
```

然后在我们的页面上写下:

```
<p is='word-count' text='Hello world.'></p>
```

正如我们所看到的，自主的定制元素和定制的内置元素没有什么不同。唯一的区别是我们扩展了`HTMLParagraphElement`而不是`HTMLElement`。

然后我们使用了`is`属性来引用定制元素，而不是在定制的内置元素中使用元素名称。

![](img/9dc8a1ebb3de1b5e221d5a20e67f575e.png)

Alex bl Jan 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 内部与外部风格

我们也可以像下面这样引用外部样式，而不是像上面那样使用内部样式。例如，我们可以写:

```
const linkElem = document.createElement('link');
linkElem.setAttribute('rel', 'stylesheet');
linkElem.setAttribute('href', 'style.css');
shadow.appendChild(linkElem);
```

这引用了来自`style.css`的样式。我们创建了一个`link`元素，就像我们在 HTML 和普通 DOM 操作中所做的一样。

创建 Web 组件很简单。我们只需为该功能定义一个类或函数，然后通过使用`customElements.define`方法将它放入自定义元素注册表中。

我们可以扩展现有的元素，比如`p`元素，或者从头开始创建。此外，我们可以通过创建一个`link`元素并引用外部文件来添加内部样式或引用外部样式。