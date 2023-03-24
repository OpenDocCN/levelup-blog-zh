# 《盖茨比》中使用门户的可及情态动词

> 原文：<https://levelup.gitconnected.com/accessible-modals-in-gatsby-using-portals-1e80e33a8a2>

## 和附加反作用钩

向你的网站或应用程序添加一个模态(对话框)是一个常见的场景。我们如何在 React 中做到这一点？具体在《盖茨比》里？在 React version 16 之前，开发人员不得不稍作改动来创建这个简单的元素。这是因为没有直接的方法在根/父元素旁边创建 DOM 元素，这可能会导致定位问题(z-index，overflow: hidden)。React v16 引入了一个名为 Portals 的新特性来实现这一点。

![](img/a82e34eda7d6f7e7605a8d27b820017d.png)

[Zoltan·塔斯](https://unsplash.com/@zoltantasi?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# React 门户

在 Gatsby 的例子中，在实现 React 门户后，DOM 看起来像这样。

```
<html>
  <body>
    <div id="___gatsby"></div>
    <div id="portal"></div>
  </body>
</html>
```

*#___gatsby* 是父元素， *#portal* 是一个新的兄弟节点，我们可以在其中注入我们的模态。

这就是我们创建门户的方式:

```
ReactDOM.createPortal(child, container)
```

> *第一个参数(* `*child*` *)是任何* [*renderable React 子*](https://reactjs.org/docs/react-component.html#render) *)，如元素、字符串或片段。第二个参数(* `*container*` *)是一个 DOM 元素。*

因此，在 HTML 中我们可以添加一个新元素#portal，就像上面一样，访问它

```
var container = document.getElementById('portal')
```

并将其作为第二个参数传递给 *createPortal()* 。

**重要提示**:

> 尽管门户可以在 DOM 树中的任何位置，但它在其他方面的行为都像普通的 React 子节点。无论子节点是否是门户，上下文之类的特性都完全相同，因为门户仍然存在于 React 树*中，而与* DOM 树*中的位置无关。*

在《盖茨比》中我们如何做到？

# [盖茨比插件门户](https://www.gatsbyjs.com/plugins/gatsby-plugin-portal/?=gatsby-plugin-portal)

如果我们想在 Gatsby 中使用门户，我们可以使用一个插件来处理一些令人不安的挑战。其中两个是:

*   **index.html 在《盖茨比》中并不存在(只在《营造》之后)**
*   **在构建期间未定义文档对象。**

我们可以通过运行以下命令来安装插件:

```
npm install --save gatsby-plugin-portal
```

然后在 gatsby-config.js 中我们添加:

```
module.exports = {
  plugins: [`gatsby-plugin-portal`]
}
```

有两个选项[你可以指定，创建元素的键和 id，默认设置为“门户”。](https://gist.github.com/miresk/abe67ef6ab1f8e76675d0a1fe44170ce)

插件使用`*onRenderBody*` API 为我们添加了 div 组件，但是我们仍然需要覆盖第二点。是时候创建 portal.js 文件了，它会处理这些问题。

**portal.js**

对它创建的文档模型有一些检查。

现在让我们创建一个模态组件。

# 模态和附加挂钩

如何创建模态取决于项目和您的需求，但是我想使用一些 React 挂钩和如下有趣的技术与您一起构建一个可访问的模态组件:

*   `[useState](https://reactjs.org/docs/hooks-reference.html#usestate)`
*   `[useRef](https://reactjs.org/docs/hooks-reference.html#useref)`
*   `[useImperativeHandle](https://reactjs.org/docs/hooks-reference.html#useimperativehandle)`
*   转发参考

啊！我们需要所有这些来创建一个简单的模态窗口吗？

我明白你的意思，但别担心，这只是几行代码。让我先给你看完整的代码。

**modal.js**

模态组件已经准备好了，所以我们可以在其他组件(例如 about.js)中导入它，并像这样使用它:

**about.js**

```
import React, {useRef} from 'react';
**import Modal from './modal';**export default function About() {
    **const modalRef1 = useRef();**

    return (
        <section>
            <button className="btn" onClick={**() => modalRef1.current.openModal()**}>Open modal</button> **<Modal ref={modalRef1}>
                <h3>Modal title 1</h3>
            </Modal>**
        </section>
    )
}
```

现在让我们浏览一下上面的代码。

## useState()挂钩

```
*const* [display, setDisplay] = useState(false)
```

默认状态为 false，这意味着模式是关闭的。我们有两个显示/隐藏模态的函数，我们相应地调用 *setDisplay()* 。True 或 false 值存储在 *display* 变量中，我们在呈现我们的模态(门户)之前检查该变量。

## [useRef](https://reactjs.org/docs/hooks-reference.html#useref) 和 [forwardRef](https://reactjs.org/docs/forwarding-refs.html)

> ***Ref forwarding****是一种自动将*[*Ref*](https://reactjs.org/docs/refs-and-the-dom.html)*通过一个组件传递给它的一个子组件的技术。*
> 
> **useRef()** *返回一个可变的 Ref 对象，其* **。当前** *属性被初始化。*

1.  我们在 About 组件中创建了一个变量 *modalRef1* ，在这里我们想要显示模态，我们将 *useRef()* 赋给这个变量。
    `*const* ***modalRef1 = useRef()****;*`
2.  我们通过将`ref`指定为 JSX 属性，将其传递给`modal`。
    `<Modal **ref={modalRef1}**>` **故意为*。当前的*属性(来自 useRef)将指向这个元素。**
3.  React 将`ref`作为第二个参数传递给`forwardRef`中的`(props, ref) => ...`函数。
    `const Modal = **forwardRef((props, ref)** => { // modal.js`(在 modal.js 中)

## [useImperativeHandle()](https://reactjs.org/docs/hooks-reference.html#useimperativehandle) 钩子

> `*useImperativeHandle*` *定制使用* `*ref*` *时暴露给父组件的实例值。*

在 modal.js 中我们可以使用我们在 *forwardRef 中传递的 Ref((props，****ref****)*中的 *useImperativeHandle()* 钩子。语法如下所示:

```
useImperativeHandle(ref, createHandle, [deps])
```

在 *createHandle* 的位置，我们返回一个具有两个属性的对象: *openModal* 和 *closeModal* ，这两个属性都是函数。

```
useImperativeHandle(
    ref,
    () => {
      return {
        openModal: () => handleOpen(),
        closeModal: () => handleClose(),
      }
    }
  )
```

这意味着我们正在定制 ref 的*当前*属性。在 about.js 中，我们使用我们的<模态>组件，并且 ref 指向它。Ref 被转发，modal.js 接收它并在返回 *openModal()* 和 *closeModal()* 函数的 *useImperativeHandle()* 钩子中使用它。因此，我们可以调用这些函数，例如当我们单击一个按钮时:

```
<button onClick={() => **modalRef1.current.openModal()**}>Open modal</button>
```

迪伦·科勒在他的文章中是这样说的:

> `*useImperativeHandle*` *为双向数据和逻辑流提供 redux 和 props 之间的中间地带。*

我认为这在某些情况下非常有用。而且记住要和`[forwardRef](https://reactjs.org/docs/react-api.html#reactforwardref).`一起用

看看用户 daryanka 制作的这个沙箱[](https://codesandbox.io/s/affectionate-fermat-vrc76?file=/src/Modal.js)****来看看 *useImperativeHandle()* 的运行情况。****

# ****可接受性****

****作为 web 开发人员，我们应该记住的一件事是可访问性。这个主题本身就值得写一篇文章，所以我将快速列出一些关于情态动词的重要事项。****

## ****角色和属性****

****模态应该包含以下属性。****

```
**<div role="dialog"
     aria-modal="true"
     aria-labelledby="dialog1_label">**
```

****例如，在 Gatsby 中，如果我们想将门户仅用于模态，我们可以更新 portal.js 并将这些行添加到构造函数中，在那里我们创建 div。****

```
**this.el.setAttribute("role", "dialog");
this.el.setAttribute("aria-modal", "true");**
```

## ****“退出”时关闭模式****

****其中一种方法是像这样使用 useEffect() hook。****

```
**useEffect(() => {
    const close = e => {
        if (e.keyCode === 27) {
            handleClose();
        }
    }
    window.addEventListener('keydown', close)
    return () => window.removeEventListener('keydown', close)
  }, []);**
```

****除了退出按钮，我们应该考虑使用 Tab 和 Shift + Tab 在屏幕上移动。我们把重点放在哪里很重要，但这也取决于你如何组织你的内容。也就是说，我将在这里留下一些与可访问性相关的链接:****

*   ****[盖茨比——让你的网站变得可访问](https://www.gatsbyjs.com/docs/conceptual/making-your-site-accessible/)****
*   ****[WAI-ARIA 练习对话模式](https://www.w3.org/TR/wai-aria-practices-1.1/examples/dialog-modal/dialog.html)****
*   ****反应— [程序化管理焦点](https://reactjs.org/docs/accessibility.html#programmatically-managing-focus)****
*   ****[反应-咏叹调-调式](https://github.com/davidtheclark/react-aria-modal)****

******其他参考文献:******

*   ****[React 门户](https://reactjs.org/docs/portals.html)****
*   ****[盖茨比插件门户](https://www.gatsbyjs.com/plugins/gatsby-plugin-portal/?=gatsby-plugin-portal)****
*   ****[Youtube 视频](https://www.youtube.com/watch?v=SmMZqh1xdB4&feature=emb_err_woyt) —使用 React 挂钩、引用和创建门户来反应模态组件****
*   ****[反应更好的模态](https://www.telerik.com/blogs/better-modals-in-react)****

****谢谢大家！****