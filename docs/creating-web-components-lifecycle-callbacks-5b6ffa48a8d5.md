# 创建 Web 组件—生命周期回调

> 原文：<https://levelup.gitconnected.com/creating-web-components-lifecycle-callbacks-5b6ffa48a8d5>

![](img/abc918f41a0d86212d68b6954bde204b.png)

由[Magnus eng](https://unsplash.com/@genuinemex?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

随着 web 应用变得越来越复杂，我们需要某种方式将代码分成易于管理的块。为此，我们可以使用 Web 组件来创建可重用的 UI 块，以便在多个地方使用。

在本文中，我们将看看 Web 组件的生命周期挂钩以及我们如何使用它们。

# 生命周期挂钩

Web 组件有自己的生命周期。Web 组件的生命周期中会发生以下事件:

*   元素插入到 DOM 中
*   当触发 UI 事件时更新
*   从 DOM 中删除的元素

Web 组件具有生命周期挂钩，是捕获这些生命周期事件并让我们相应地处理它们的回调函数。

他们让我们处理这些事件，而不需要创建我们自己的系统来这样做。大多数 JavaScript 框架提供相同的功能，但是 Web 组件是一个标准，所以我们不需要加载额外的代码就可以使用它们。

web 组件中有以下生命周期挂钩:

*   `constructor()`
*   `connectedCallback()`
*   `disconnectedCallback()`
*   `attributeChangedCallback(name, oldValue, newValue)`
*   `adoptedCallback()`

## 构造函数()

创建 Web 组件时会调用`constructor()`。它在我们创建影子 DOM 时被调用，用于设置监听器和初始化组件的状态。

然而，不建议我们在这里运行渲染和获取资源之类的操作。`connectedCallback`更适合这类任务。

对于 ES6 类来说，定义一个构造函数是可选的，但是如果没有定义，就会创建一个空的构造函数。

当创建构造函数时，我们必须调用`super()`来调用 Web 组件类扩展的类。

我们可以在那里使用`return`语句，但不能在那里使用`document.write()`或`document.open()`。

此外，我们不能在构造函数方法中获得属性或子元素。

## connectedCallback()

`connectedCallback()`当一个元素被添加到 DOM 时，方法被调用。我们可以确定当这个方法被调用时，这个元素对 DOM 是可用的。

这意味着我们可以安全地设置属性、获取资源、运行设置代码或呈现模板。

## disconnectedCallback()

当从 DOM 中移除元素时调用这个函数。因此，这是添加清理逻辑和释放资源的理想场所。我们还可以使用这个回调来:

*   通知应用程序的另一部分，该元素已从 DOM 中移除
*   释放不会被自动垃圾收集的资源，比如取消订阅 DOM 事件，停止间隔计时器，或者取消注册所有已注册的回调

当用户关闭标签页时，这个钩子永远不会被调用，在它的生命周期中它可以被触发多次。

## attributeChangedCallback(attrName，oldVal，newVal)

我们可以像传递任何其他属性一样，将带有值的属性传递给 Web 组件:

```
<custom-element 
  foo="foo" 
  bar="bar" 
  baz="baz">
</custom-element>
```

在这个回调中，我们可以获得在代码中分配的属性值。

我们可以添加一个`static get observedAttributes()`钩子来定义我们观察的属性值。例如，我们可以写:

```
class CustomElement extends HTMLElement {
  constructor() {
    super();
    const shadow = this.attachShadow({
      mode: 'open'
    });
  } static get observedAttributes() {
    return ['foo', 'bar', 'baz'];
  } attributeChangedCallback(name, oldValue, newValue) {
    console.log(`${name}'s value has been changed from ${oldValue} to ${newValue}`);
  }
}customElements.define('custom-element', CustomElement);
```

那么我们应该从`console.log`中得到以下内容:

```
foo's value has been changed from null to foo
bar's value has been changed from null to bar
baz's value has been changed from null to baz
```

假设我们有下面的 HTML:

```
<custom-element foo="foo" bar="bar" baz="baz">
</custom-element>
```

我们得到这个是因为我们给 HTML 中的`foo`、`bar`和`baz`属性分配了相同名称的值。

## 已采用回调()

当我们用传入的元素调用`document.adoptNode`时，就会调用`adoptedCallback`。它只发生在我们处理 iframes 的时候。

方法用于将一个节点从一个文档转移到另一个文档。iframe 有另一个文档，所以可以用 iframe 的 document 对象调用它。

![](img/54114adc09dc6404919c9c647688041a.png)

Matthew Cabret 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 例子

我们可以用它来创建一个带有如下闪烁文本的元素:

```
class BlinkElement extends HTMLElement {
  constructor() {
    super();
  } connectedCallback() {
    const shadow = this.attachShadow({
      mode: 'open'
    });
    this.span = document.createElement('span');
    this.span.textContent = this.getAttribute('text');
    const style = document.createElement('style');
    style.textContent = 'span { color: black }';
    this.intervalTimer = setInterval(() => {
      let styleText = this.style.textContent;
      if (style.textContent.includes('red')) {
        style.textContent = 'span { color: black }';
      } else {
        style.textContent = 'span { color: red }';
      }}, 1000)
    shadow.appendChild(style);
    shadow.appendChild(this.span);
  } disconnectedCallback() {
    clearInterval(this.intervalTimer);
  } static get observedAttributes() {
    return ['text'];
  } attributeChangedCallback(name, oldValue, newValue) {
    if (name === 'text') {
      if (this.span) {
        this.span.textContent = newValue;
      } }
  }
}customElements.define('blink-element', BlinkElement);
```

在`connectedCallback`中，我们有初始化元素的所有代码。它包括为我们传递给属性`text`的文本添加一个`span`作为它的值，以及闪烁文本的样式。样式从创建`style`元素开始，然后我们添加了`setInterval`代码，通过将`span`的颜色从红色变为黑色来闪烁文本，反之亦然。

然后，我们将节点附加到我们在开始时创建的影子 DOM。

我们有`observedAttributes`静态方法来设置要监视的属性。这被`attributeChangedCallback`用来监视文本属性的值，并通过获取`newValue`来相应地设置它。

要查看`attributeChangedCallback`的运行情况，我们可以动态创建元素，并按如下方式设置其属性:

```
const blink2 = document.createElement('blink-element');
document.body.appendChild(blink2);
blink2.setAttribute('text', 'bar');
blink2.setAttribute('text', 'baz');
```

然后我们应该`bar`和`baz`，如果我们在`attributeChangedCallback`钩子中记录`newValue`。

最后，我们有`disconnectedCallback`，它在组件被移除时运行。例如，当我们用下面的代码删除一个带有`removeChild`的元素时:

```
const blink2 = document.createElement('blink-element');
document.body.appendChild(blink2);
blink2.setAttribute('text', 'bar');
document.body.removeChild(blink2);
```

生命周期挂钩允许我们处理影子 DOM 内部和外部的各种 DOM 事件。Web 组件有用于初始化、移除和属性更改的钩子。我们可以选择观察哪些属性的变化。

在另一个文档中也有采用元素的挂钩。