# 增强你的网页设计技能:避免！“不惜一切代价重要

> 原文：<https://levelup.gitconnected.com/beef-up-your-web-design-skills-avoid-important-at-all-costs-cb5e10437e0f>

## 改为这样写你的 CSS。

![](img/a68ebd19e8191e7d0bad2caf3f1071a7.png)

由 [VectorMine](https://stock.adobe.com/contributor/201457013/vectormine?load_type=author&prev_url=detail)

当我看到分散在 CSS 代码库中的`!important`时，没有什么比这更让我激动的了。鲁莽地将`!important`添加到你的 CSS 中会使你的代码难以管理。

`!important` CSS 规则通常用于覆盖先前的样式规则。一旦添加了这个，如果您需要覆盖它，您将需要使用另一个`!important`。

想象你现在进来维护这个代码。你写了一些 CSS，却发现由于某处应用的`!important`规则，它不起作用了。您的下一步是:

*   移除`!important`并更新代码，这样没有它时一切看起来都像预期的那样
*   为你想做的事情添加另一个`!important`

频繁使用`!important`会导致混乱和复杂的 CSS 代码库。下面是您应该做的，以使下一个开发人员更容易维护代码。

# 增加特异性

你熟悉 CSS 特异性吗？当我第一次开始我的 web 开发者生涯时，我当然不是。我是自学的，后来偶然发现的。

> 特异性是浏览器决定哪些 CSS 属性值与元素最相关，因此将被应用的方法。特异性基于由不同种类的 [CSS 选择器](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference#selectors)组成的匹配规则。
> 
> 了解更多:[https://developer . Mozilla . org/en-US/docs/Web/CSS/specification](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity)

以下是从最高特异性到最低特异性的选择器类型列表:

1.  ID 选择器—(例如`#idname` *)* 。
2.  类选择器，属性选择器，伪类选择器:
    a)类选择器——(例如`.example`)。
    b)属性选择器—(例如`[type="radio"]`)。
    c)伪类选择器—(例如`:hover`)。
3.  类型选择器—(如`h1`)。

# 考虑原子 CSS

既然我已经提到了特殊性，值得注意的是，我们希望避免不断地创建我们总是需要覆盖的过高的特殊性。Thierry Koblentz 在他题为“[挑战 CSS 最佳实践](https://www.smashingmagazine.com/2013/10/challenging-css-best-practices-atomic-approach/)”的文章中首次创造了原子 CSS 这个术语它的工作原理是将类映射到一个单一的样式，这样每个类都作为一个构建块，而不是上下文 CSS。

这是以下两者的区别:

```
// specificity too high and hard to maintain
.button.red { 
    display: inline-block;
    height: 1.75rem;
    background-color: red
}.modal .button {
    position: absolute;
    bottom: 2rem;
}
```

**(HTML)**

```
<div class="modal">
  <button type="button" class="button red">Button</button>
</div>
```

和

```
.display-ib {
  display: inline-block;
}.height-2 {
  height: 2rem;
}.bg-red {
  background-color: red
}.absolute {
  position: absolute;
}.left-50 {
  left: 50%;
}.translateX-50 {
  transform: translateX(-50%);
}.bottom-2 {
  bottom: 2rem;
}
```

**(HTML)**

```
<div class="modal">
  <button type="button" class="display-ib height-2 bg-red absolute left-50 translateX-50 bottom-2">Button</button>
</div>
```

您可能会想，*“我为什么要创建那么多的类呢？?"我发现这些众多的单一功能类在大型项目中是有益的。*

## 原子 CSS 的优点

*   **最小化膨胀** —不太需要在样式表中添加更多样式。使用原子 CSS 类，您将能够构建整个组件，而无需为新组件添加新的 CSS 样式。
*   **代码重用** —你可以在许多组件中使用相同的 CSS 类。
*   **减少特异性冲突** —由于特异性冲突减少，对`!important`的需求减少，因为所有原子类都将使用低特异性。

## 原子 CSS 库

您可以从下面列出的免费库之一开始使用原子 CSS。

*   超光速粒子 CSS—[https://tachyons.io/](https://tachyons.io/)
*   顺风 CSS—[https://tailwindcss.com/](https://tailwindcss.com/)
*   https://bulma.io/布尔玛

# 考虑 BEM

BEM 代表块、元素、修饰符，是 CSS 类的命名约定。这些类通常看起来像:`.block__element--modifier`

**例如:**

```
/* the block component */
.modal {}/* the block component with a modifier that changes the style */
.modal--yellow {}/* the element that relies on the block */
.modal__button {}/* the element with a modifier */
.modal__button--red
```

**(HTML)**

```
<div class="modal modal--yellow">
  <button type="button" class="modal__button--red">Button</button>
</div>
```

## 支持 BEM

*   标准命名约定——将帮助其他开发者对 CSS 类应该如何命名有一个共同的理解，从而增加可维护性。一眼就能看出类名的含义以及哪些组件依赖于另一个组件。
*   **减少特异性冲突** —这是另一种旨在通过利用低特异性 CSS 类来减少特异性冲突的方法。

# 结论

在调用`!important`规则之前，请考虑本文中列出的选项。它*在 CSS 写作中有它的位置，比如在 [HTML 时事通讯](/the-trials-and-tribulations-of-html-newsletters-92bb5736ec8b)和覆盖内联样式中。然而，我写这篇文章是因为我看到它经常成为一种负担。在 CSS 代码库中过于频繁地使用`!important`会导致代码难以维护。*

*当您需要覆盖样式时，请考虑以下事项:*

*   *增加特异性—确定什么是合适的特异性，并使用它来覆盖必要的样式*
*   *注意不要创建特异性太高的 CSS 选择器。在你的 CSS 代码库中考虑以下方法:
    -原子 CSS
    - BEM*

*以后你会感谢自己的:)。*

# *进一步阅读*

1.  *[https://developer . Mozilla . org/en-US/docs/Web/CSS/specification](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity)*
2.  *[https://specifishity.com/](https://specifishity.com/)*
3.  *[https://blog.hubspot.com/website/css-important](https://blog.hubspot.com/website/css-important)*
4.  *[https://www . smashingmagazine . com/2013/10/challenged-CSS-best-practices-atomic-approach/](https://www.smashingmagazine.com/2013/10/challenging-css-best-practices-atomic-approach/)*
5.  *[https://www . site point . com/CSS-architecture-block-element-modifier-BEM-atomic-CSS/](https://www.sitepoint.com/css-architecture-block-element-modifier-bem-atomic-css/)*
6.  *[https://www.toptal.com/css/introduction-to-bem-methodology](https://www.toptal.com/css/introduction-to-bem-methodology)*