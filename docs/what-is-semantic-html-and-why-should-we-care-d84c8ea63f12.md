# 什么是语义 HTML，我们为什么要关心？

> 原文：<https://levelup.gitconnected.com/what-is-semantic-html-and-why-should-we-care-d84c8ea63f12>

![](img/d0c4b831a11062327c4d55027169d4b4.png)

瓦列里·塞索耶夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

HTML 标签告诉浏览器如何构建和显示网页内容。语义 HTML 是一种功能，我们可以用更具描述性的方式编写 HTML 代码。它让人们理解这一行或一大块代码的作用，而不是它们应该如何构造或看起来像什么。在语义 HTML 出现之前，我们经常会看到一堆 div 标签、p 标签和 span 标签，这些标签对它们的内容一无所知，我们通常使用“id”来说明它们的用途(或注释)。然而，我们可以用一种更加不言自明的方式做到这一点，而不需要用 id 或类来描述它们的角色。

以下是语义 HTML 之前的代码:

```
<div id="header">
     <div id="nav-bar">
        <a href="index.html">Home</a>
        <a href="about.html">About Us</a>
     </div>
</div>
<div id="main">
     This is the section where main content goes.
     <div class="content">
          <div>This is for section 1 content.</div>
     </div>
     <div class="content">
          <div>This is for section 2 content.</div>
     </div>
     <div class="content">
          <div>This is for section 3 content.</div>
     </div>
</div>
<div id="footer">
     <div>This is for footer.</div>
</div>
```

这是语义 HTML 的应用。

```
<header>
     <nav>
          <a href="index.html">Home</a>
          <a href="about.html">About Us</a>
     </nav>
</header>
<main>
     This is the section where main content goes.
     <section>
          <h2>Section 1</h2>
          <div>This is for section 1 content.</div>
     </section>
     <section>
          <h2>Section 2</h2>
          <div>This is for section 2 content.</div>
           <article>
                <figure>
                     <img src="/whatever.png" />
                     <figcaption>
                          This is the image caption.
                     </figcaption>
                </figure>
                <p>Article content goes here.</p>
           </article>
     </section>
     <section>
          <h2>Section 3</h2>
          <div>This is for section 3 content.</div>
     </section>
</main>
<footer>
     <div>This is for footer.</div>
</footer>
```

这些是 HTML5 中引入的语义 HTML 标签的一些例子，你可以在你的代码中使用:

**<表头>**

*   包含网页和导航菜单的介绍性项目。它与

    # 相似但不同，因为用于表示节，

    # 用于放置该节的标题。

**<页脚>**

*   包含页脚信息，例如版权、隐私政策、联系人等。

**<导航>**

*   包含导航链接

**<主>**

*   表示主要内容区域，这应该只在页面中使用一次。([一个文档不能有多个未指定隐藏属性的主元素。](https://html.spec.whatwg.org/multipage/grouping-content.html#the-main-element))

**<章节>**

*   表示一个独立的通用部分，每个部分都应该有一个标题。

**<图>** & **<图>**

*   这个独立的元素可以用来对带有标题的图像进行分组

[**大约有 100 个…**](https://developer.mozilla.org/en-US/docs/Web/HTML/Element)

# 以下是使用语义 HTML 的一些好处:

**1)可读性**
<页眉>，<页脚>，<导航> …它们的作用很大程度上类似于一个< div >，但是，这将使我们更清楚地了解这些块中的内容。第二个代码块比上面的两个代码块更容易阅读和理解。作为开发人员，我们经常需要看别人的代码。因此，语义 HTML 肯定会改善代码的阅读体验。

这些关键词会提高你的内容的搜索排名，因为它们提供了更丰富的信息，而不是告诉搜索引擎什么都没有。

**3)可访问性**
语义 HTML 将提高您的网页的可访问性，这将为通过[屏幕阅读器](https://axesslab.com/what-is-a-screen-reader/)访问这些页面或依靠纯键盘导航(使用 tab 键从一个部分跳到另一个部分)的受众提供更精确的上下文。例如，你可能想要使用<按钮>，而不是仅仅使用按钮的<和>标签。对于其他信息，您也可以用按钮的值将“角色”属性分配给< a >。这也将被屏幕阅读器识别。

我希望这能让你深入了解什么是语义 HTML，以及这将如何提高我们的代码质量。干杯。