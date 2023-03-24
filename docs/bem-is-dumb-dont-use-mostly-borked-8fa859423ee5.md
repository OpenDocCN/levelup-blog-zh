# 本是哑巴。不要使用大部分 Borked

> 原文：<https://levelup.gitconnected.com/bem-is-dumb-dont-use-mostly-borked-8fa859423ee5>

![](img/c09e6491336f603192d5c6e05296de76.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [La-Rel Easter](https://unsplash.com/@lastnameeaster?utm_source=medium&utm_medium=referral) 拍摄的照片

我想喜欢 BEM——Block，Element，Modifier——真的喜欢！任何时候有人提出一个统一一致的命名方案，我通常都全力以赴…但是…当涉及到它是如何工作的，它做了什么的时候，如果不是全部的话，也是大部分的挫败了 HTML 存在的原因和 CSS 从它分离出来的原因。

它散发着人们太愚蠢而无法理解选择器、组合子和适当语义的简单性，试图到处抛弃类来修复他们自己的无能；当你真的需要使用组合子和适当的标记“如此困难”时，也许是时候放弃了，走开，去卖汉堡包谋生。

这真的是在用类给每个该死的标签做标记吗？这才是真正的烦恼。[在关于“课程如何更快”的**露骨谎言**和“特殊性对普通人来说太难了”的颇具侮辱性的观点](/css-selectors-combinators-are-slow-classes-are-fast-theyre-lying-to-you-827ff7d15203)之间，BEM 建立在网络开发的那些讨厌的“3i”的基础上:无知、无能和无能。

你知道什么不会更快吗？使用 2 到 10 倍的 HTML 来完成这项工作。你知道什么不利于可及性吗？没有使用正确的语义。你知道是什么让 CSS 难以维护吗？没有利用你的语义来做真正的工作！

我的意思是，从官方网站上取下第一个示例代码[:](http://getbem.com/introduction/)

```
<button class="button">
	Normal button
</button>
<button class="button button--state-success">
	Success button
</button>
<button class="button button--state-danger">
	Danger button
</button>
```

真的，纽扣就是纽扣？世卫组织知道！？！

在他们的 CSS 中:

```
.button {
	display: inline-block;
	border-radius: 3px;
	padding: 7px 12px;
	border: 1px solid #D5D5D5;
	background-image: linear-gradient(#EEE, #DDD);
	font: 700 13px/18px Helvetica, arial;
}
.button--state-success {
	color: #FFF;
	background: #569E3D linear-gradient(#79D858, #569E3D) repeat-x;
	border-color: #4A993E;
}
.button--state-danger {
	color: #900;
}
```

是”。按钮"真的神奇地比"按钮"好吗？或者”。与“button.success”相比,“button-state-success”不知何故是一个奇迹创造者？这完全是代码膨胀的废话，甚至不能理解 HTML/CSS 应该如何操作。

虽然我们有足够的证据**这些小丑没有资格写一行 CSS** 在他们的 CSS 中包含所有那些 PX 度量声明。*告诉具有可访问性的用户需要“走开，去耕耘你自己”的方式。说真的，当你在 PX 声明的代码库中的任何地方看到字体大小时，这是一个已知的事实，即制作这个 CSS 的人没有资格编写任何东西！*

我们可以在其他例子中看到他们对最基本的 HTML 一无所知的进一步证据:

```
<form class="form form--theme-xmas form--simple">
  <input class="form__input" type="text" />
  <input
    class="form__submit form__submit--disabled"
    type="submit" />
</form>
```

哇，表格就是表格。谁知道呢？更不用说一个“简单”的形式，如何描述是什么…当然，它更像是一个简单的形式，给定他们的字段集在哪里？给输入加个标签怎么样，这样非可视化用户界面上的用户就能知道这到底是干什么的了。Type="submit "，这是什么 1997？使用按钮。

愚蠢的不完整标记，为他们当前的“功能”*(缺乏可访问性)*

```
<form class=”xmasSomething”>
  <input type="text" name="someData">
  <button disabled>submit</button>
</form>
```

但在实践中，修复缺失的应该是:

```
<form class="xmasSomething" method="post" action="#">
  <fieldset>
    <label>
      Describe this input
      <input type="text" name="someData">
    </label>
    <button disabled>submit</button>
  </fieldset>
</form>
```

假设一个人真的关心可用性和可访问性，并且不打算用[愚蠢地使用占位符作为标签](https://lmgtfy.app/?q=Placeholder+is+not+a+label)。

土地的缘故，愚蠢运行如此之深，他们有一个“禁用”类，而不是实际上禁用血腥的输入！好像“`form button[disabled]`”比“`.form__submit — disabled`”难那么多。这就是它变得多么愚蠢。

一切都散发着 3i 的味道。从“我们不建议重置”告诉你基本上手动设置每个该死的元素的填充/边距，到用你不需要的无尽的无意义的垃圾类来破坏标记，到通过挖掘大量的类来使维护变得更加困难，这些类产生了比它修复的更多的特性地狱，一旦你开始在单个元素上丢弃多个类就证明了这一点。

问题，那些是英制还是公制吨？

看到这些令人麻木的垃圾:

```
<div class="cardWrapper">
 <h2 class="cardWrapper__title">Card Description</h2>
  <div class="card">
    <img class="card__img" src="..." alt="...">
    <h3 class="card__subtitle">...</h3>
    <p class="card__description">...</p>
    <div class="card__rating">...</div>
  </div>
  <div class="card">
    <img class="card__img" src="..." alt="...">
    <h3 class="card__subtitle">...</h3>
    <p class="card__description">...</p>
    <div class="card__rating">...</div>
  </div>
  <div class="card">
    <img class="card__img" src="..." alt="...">
    <h3 class="card__subtitle">...</h3>
    <p class="card__description">...</p>
    <div class="card__rating">...</div>
  </div>
</div>
```

做…的工作:

```
<div class="cards">
 <h2>Card Description</h2>
  <section>
    <img src="..." alt="...">
    <h3>...</h3>
    <p>...</p>
    <div class="rating">...</div>
  </section>
  <section>
    <img src="..." alt="...">
    <h3>...</h3>
    <p>...</p>
    <div class="rating">...</div>
  </section>
  <section>
    <img src="..." alt="...">
    <h4>...</h4>
    <p>...</p>
    <div class="rating">...</div>
  </section>
</div>
```

只有需要类的东西才是语义中立的 DIV。

因为，如果“`.cards h3`”比“`.card-subtitle`”难那么多，请帮我们大家一个忙，把****从键盘上挪开，去学一些不太注重细节的东西，比如麦克拉伦！

你使用 BEM 所做的只是让代码变得更大更难维护，这是为了什么？“哇哇，两个笨蛋选一个组合！？!"

再说一次，像这样的废话——就像 HTML/CSS 框架的垃圾一样——就是为什么人们接受最简单的任务，却以完成工作所需的 2 到 10 倍的代码而告终，这些代码错综复杂，开发缓慢，难以维护。

经过 40 年的编程，其中一半是网络技术？愚蠢是如此痛苦地大声，它伤害了我的耳朵。