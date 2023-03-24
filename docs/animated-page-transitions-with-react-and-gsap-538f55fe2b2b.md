# 使用 React 和 GSAP 制作动画页面过渡

> 原文：<https://levelup.gitconnected.com/animated-page-transitions-with-react-and-gsap-538f55fe2b2b>

![](img/554a8c166b28e6682ad75e26d04e39f1.png)

克里斯·里德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 你能做什么

# CodeSandbox

# 属国

为了能够进行如上所示的一些转换，您只需要安装两个额外的依赖项:

1.  `react-router-dom`:用于网站不同页面之间的路由。在我们的例子中，这个依赖关系负责我们的*主页*和*关于*页面之间的路由。
2.  `gsap`:这是制作网站动画的最好、最强大的工具之一。在我们的例子中，我们将只探索它的一小部分，这将有助于我们在页面转换之间制作动画。

```
To install these two dependencies execute the following code in your command line:yarn add react-router-dom gsapornpm install react-router-dom gsap
```

# 过渡

## 第一:什么

创建过渡的第一步是决定在进入或退出页面时，当前页面中有哪些元素需要动画。在我们的例子中，除了导航菜单之外，我们动画显示了页面的所有组件。但是，您可以决定在转场中包含或排除任何想要的元素。

这一步很重要，因为当 React 在虚拟 DOM 中创建和呈现组件时，GSAP 需要处理真实 DOM 中存在的组件。为了解决这个问题，我们需要使用 **refs** ，它提供了一种从 React 访问 DOM 节点的方法。

让我们看看我们是如何为我们的主页做到这一点的，使用一个组件作为例子——包含*欢迎*消息的标题:

```
<div ref={div => (this.header = div)} className="home-header"> <p>Welcome!</p></div>
```

具体来说，片段`ref={div => (this.header = div)}`是允许我们访问这个 DOM 节点的一段代码(当我们用 GSAP 制作这个节点的动画时，这是必要的)。我们在这里所做的基本上是将这个 div(最初位于虚拟 DOM 中)链接到一个 GSAP 可以定位的“实际”DOM 节点。我们可以通过调用`this.header`来访问这个 DOM 节点，这就是我们对节点的命名方式。

在我们的例子中，我们对主页中的 3 个组件都这样做。它们被命名为`this.header`、`this.content`和`this.footer`。看看您能否在下面的代码中找到它们，并理解发生了什么:

```
<div ref={div => (this.header = div)} className="home-header"> <p>Welcome!</p></div><div ref={div => (this.content = div)} className="home-content"> <p> Lorem Ipsum is simply dummy text of the printing and typesettingindustry. Lorem Ipsum has been the industry's standard dummy textever since the 1500s, when an unknown printer took a galley oftype and scrambled it to make a type specimen book. It hassurvived not only five centuries, but also the leap intoelectronic typesetting, remaining essentially unchanged. It waspopularised in the 1960s with the release of Letraset sheetscontaining Lorem Ipsum passages, and more recently with desktoppublishing software like Aldus PageMaker including versions ofLorem Ipsum. </p></div><div ref={div => (this.footer = div)} className="home-footer"> <p>January, 2020</p></div>
```

我们对*关于*页面做同样的操作。请检查 CodeSandbox 以进一步探索如何将**引用** 应用于该页面。

**那么；该如何**

创建过渡的下一步是决定如何制作不同组件的动画。这一步是选择你的效果并微调它们。例如，您可以决定让某个元素跳动一秒钟，而另一个元素逐渐出现在屏幕上。这就是这一步所要做的——选择和微调你的效果。这就是 GSAP 变得非常方便的地方！

让我们来探索一下如何在我们的主页上做到这一点。

你需要做的第一件事是创建一个 **GSAP 时间线最大值**，正如其名称所示，这是一个动画时间线，让你决定你希望你的动画发生的时间和方式。在我们的主页中，我们在下面这段代码中创建了时间轴，**，它需要在你的类**的构造函数方法中:

```
this.timeline = new TimelineMax({ paused: true });
```

就这样，你已经创建了你的时间线！现在，我们需要使用它。这里，我们将把上一步中创建的**引用**与我们新创建的**时间线**结合起来。我们使用以下代码来实现这一点:

```
componentDidMount() { this.timeline .from(this.header, 0.5, { display: "none", autoAlpha: 0, delay: 0.25, ease: Power1.easeIn }) .from(this.content, 0.4, { autoAlpha: 0, y: 25, ease: Power1.easeInOut }) .from(this.footer, 0.4, { autoAlpha: 0, y: 25, ease: Power1.easeInOut }); this.timeline.play();}
```

那么，我们在这里做什么？

首先，我们抓取我们的时间线，并告诉它我们希望在屏幕上显示的第一个组件是由`this.header`引用的组件，我们希望在动画开始发生之前不显示任何内容`display: "none"`，我们希望在动画开始之前有 0.25 秒的延迟，我们希望效果是一个缓和(特别是 Power1 easeIn) `ease: Power1.easeIn`，最后但同样重要的是，我们希望动画持续 0.5 秒(这是在`this.header`之后的数字)。

接下来，我们链接下面要显示的组件:`this.content`。当前一个动画结束时，时间轴才会开始这个动画(这可以根据您的需要定制，但是在我们的例子中，它是这样工作的)。`autoAlpha: 0`给出一个不透明度从 0 到 1 的动画，最后`y: 25`给出动画开始的位置。

最后，我们需要真正播放时间线，这样一切都开始发生:`this.timeline.play()`。如果我们错过了这最后一步，什么都不会发生。

我强烈建议你使用这些变量，看看动画是如何表现的。此外，我鼓励你去探索 [GSAP](https://greensock.com/docs/v2/Easing) 提供的惊人的缓解效果库。

## 最后一步:何时

在创建转换之前，我们需要考虑的最后一件事是我们希望它们在什么时候发生。在我们的示例中，我们希望它们在我们进入页面时发生(以动画形式显示页面中不同组件的显示方式)以及每当我们退出页面时发生(以动画形式显示组件如何从屏幕上撤回)。在这个例子中，退出一个页面的唯一方法是点击另一个页面的链接。

进入情况是相当容易的。因为 React 已经为此提供了一个名为 ComponentDidMount 的方法。这就是为什么我们在 ComponentDidMount 方法中创建和播放时间轴。每当这一页被安装，我们的时间线将播放。简单。

然而，现有的情况并不简单，因为 React 没有提供一种干净的方法来处理它。所以我们基本上破解了它。

首先是考虑退出过渡应该何时发生。正如我们之前所说的，对于我们的例子，每当我们导航到一个不同于我们当前所在的页面时，就会发生这种情况。然后，如果我们现在在家，每当我们点击导航菜单中的 About 链接(在我们的例子中是一个按钮)时，退出转换就会发生。在代码中，我们的主页是这样的:

```
<header className="navigation">
 <button className="nav-item"> <p>Home</p> </button> <button className="nav-item" onClick={e => this.changePage(e, "/about")} > <p>About</p> </button> </header>
```

这个标题组件代表我们的导航菜单，它有两个按钮组件:Home 和 About。因为这是我们主页的代码，所以当我们点击 Home 按钮时，什么都不会发生。然而，每当我们单击“关于”按钮时，应该会发生两件事:1)退出过渡应该开始，2)在退出过渡完成后，它应该会将我们转到“关于”页面。这两件事发生在 changePage 函数中，每当点击 About 按钮(链接)时就会调用这个函数。以下是 changePage 函数的代码:

```
changePage = (e, destination) => { e.preventDefault(); this.timeline.reverse(); const timelineDuration = this.timeline.duration() * 1000; setTimeout(() => { this.props.history.push(destination); }, timelineDuration);};
```

如前所述，这里发生了两件事:

1.  退出动画开始播放。在我们的例子中，我们决定反转时间轴:`this.timeline.reverse()`，但是您可以使用不同的时间轴轻松地制作自己的退出动画。
2.  退出动画完成后，由常量`timelineDuration`捕捉，我们使用`react-router-dom`历史对象从当前页面跳转到 about 页面:`this.props.history.push(destination)` —在本例中，destination 是“/about”，对应我们 about 页面的路线。

这里不探讨路由，因为它超出了本文的范围。但是，请在下面的评论区留下关于该部分的任何问题。

感谢您的阅读！我希望这有用！