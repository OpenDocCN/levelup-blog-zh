# 变异观测器 Web API

> 原文：<https://levelup.gitconnected.com/the-mutationobserver-web-api-cf469261eb36>

对 MutationObserver Web API 的介绍，它提供了监视对文档所做的更改的能力。

![](img/a2e82ba7147a3ba50da648393cbc6a18.png)

来源论坛 [resetera](https://www.resetera.com/threads/magneto-or-professor-x-who-do-you-side-with-more.291911/)

我最近在 [MutationObserver Web API](https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver) 的帮助下开发了多个跨项目的特性。令我有点惊讶的是，我注意到一些同事从未使用过它，甚至以前从未听说过它。这就是为什么我有了这篇博文的想法。

# 介绍

`[MutationObserver](https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver)`接口提供了观察对`[DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model)`树所做的更改的能力(来源 [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver) )。

这是一个 web 特性，在所有浏览器中都有实现(是的，根据[可以使用](https://caniuse.com/?search=mutationobserver)的说法，甚至是 Internet Explorer v11)，它允许我们检测文档和网页的变化。

# 换句话说

我不喜欢《最后一战》这部电影，但是，你还记得罗格注射疫苗来消除她的能力吗？没有任何其他信息，我们仍然不知道治疗是否有效。要解决问题(3)，我们必须试试运气并取得联系，但不知道会有什么结果。另一方面，由于他的心灵转化能力，X 教授将能够检测到变异，并知道它是否成功。

我们的网页遵循同样的想法。

当我们对 DOM (1)进行修改时，比如修改一个标签或者一个属性，不管有没有框架，它都会被浏览器(2)解释和呈现。即使操作真的很快，如果我们查询(3)随后修改所涉及的 DOM 元素，我们也不能 100%确定修改已经被应用了。幸运的是，多亏了`MutationObserver`，我们可以检测到这种突变，从而知道它何时以及是否有效地发生了。

# 走查

要初始化一个`MutationObserver`，你应该调用它的`constructor`一个`callback`函数，当 DOM 发生变化时调用这个函数。

```
const observer = new MutationObserver(callback);
```

回调函数获取一个已经应用的单个 DOM 突变的数组作为参数。

为了观察目标节点并开始通过回调接收通知，您可以调用函数`observe()`。

```
observer.observe(targetNode, config);
```

作为第二个参数，应该传递一个配置。它定义了我们要观察的突变类型。这些都记录在优秀的 [MDN Web 文档](https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver/observe)中。对于我来说，我经常使用`attributes`来观察对`style`和`class`的修改，或者像前面的例子一样，使用`childlist`来观察对一个元素的子元素的修改。

为了阻止`MutationObserver`接收进一步的通知，除非`observe()`再次被调用，应使用函数`disconnect()`。只要在实例上调用它，就可以在回调或任何地方调用它。

```
observer.disconnect();
```

最后但同样重要的是，它公开了一个函数`takeRecords()`，可以查询该函数来删除所有未决的通知。

# 具体例子

我在对 [DeckDeckGo](https://deckdeckgo.com) 的 WYSIWYG 内联编辑器进行一些改进，我必须对用户通过输入字段输入的选择应用一种颜色，同时保留范围，以便每次用户输入一种新颜色时，它都会应用到相同的选定文本🤪。

总结如下:

```
class Cmp {

  private range = window.getSelection()?.getRangeAt(0);

  applyColor() {
    const selection = window.getSelection();

    selection?.removeAllRanges();
    selection?.addRange(this.range);

    const color = document.querySelector('input').value;

    document.execCommand('foreColor', false, color);

    this.range = selection?.getRangeAt(0);
  }
}
```

它应该工作了吧？嗯，不，它没有或至少没有完全😉。

事实上，获得并应用颜色到选区确实如预期的那样工作了，但是，后来我无法保存范围，`this.range`没有像我预期的那样被重新分配。

幸运的是，我能够用`MutationObserver`解决这个问题。

```
class Cmp {

  private range = window.getSelection()?.getRangeAt(0);

  applyColor() {
    const selection = window.getSelection();

    selection?.removeAllRanges();
    selection?.addRange(this.range);

    const color = document.querySelector('input').value; // A. Create an observer
    const observer = new MutationObserver(_mutations => {
        // D. Disconnect it when triggered as I only needed it once
        observer.disconnect();

        // E. Save the range as previously implemented
        this.range = selection?.getRangeAt(0);
    });

    // B. Get the DOM element to observe
    const anchorNode = selection?.anchorNode;

    // C. Observe 👀
    observer.observe(anchorNode, {childList: true});

    document.execCommand('foreColor', false, color);
  }
}
```

首先，我创建了一个新的`MutationObserver`。我定义了必须观察哪个节点元素(在我的例子中是父元素)( B ),并配置了观察器(C ),以便在 DOM 发生变化时通过其回调函数接收通知。在回调中，我首先断开(d)它，因为只有一个事件对我的用例感兴趣，最后(e)能够像预期的那样保存范围🥳.

# 更进一步

如果你喜欢这个关于`MutationObserver`的介绍，我可以建议你更进一步，去看看[尺寸观测器](https://developer.mozilla.org/en-US/docs/Web/API/ResizeObserver)和[交叉观测器](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserver)。

例如，第一个可以用于检测可编辑字段大小的变化，第二个用于延迟加载内容。

# 摘要

您可能不会每天都使用观察器，但是在检测应用于 DOM 的更改时，它们非常有用。此外，用这些来开发特性也很有趣🤙。

到无限和更远的地方！

大卫

你可以通过 [Twitter](https://twitter.com/daviddalbusco) 或我的[网站](https://daviddalbusco.com/)联系我。

尝试使用 [DeckDeckGo](https://deckdeckgo.com/) 制作您的下一张幻灯片。

[![](img/c10750c018f0c5d1ca890b1188fc0b2b.png)](https://deckdeckgo.com)