# 使用 CSS3 变量和最少的普通 JavaScript 进行日夜颜色切换

> 原文：<https://levelup.gitconnected.com/day-night-colour-switching-using-css3-variables-and-minimal-vanilla-javascript-a54fa36c550f>

![](img/f092bd633700c66c07850613d8ae85ae.png)

日日夜夜，在我的皮肤下…

界面设计的一个最新趋势是能够在亮主题和暗主题之间进行选择。这提高了许多用户的可用性和可访问性，特别是那些晚上在黑暗的房间里浏览网站的用户。给用户选择总是一个好主意。

我已经看到许多实现跨越了一些…有趣的障碍。我有更强烈的词来形容它，但我们姑且称之为“有趣”。JS 中的 CSS，动态加载一个完全独立的样式表，服务器端处理…但是事实证明，有了新的 CSS3 伪状态，比如:checked 和:target，人们用 JavaScript 做的很多事情甚至不再是 JavaScript 的工作了。[模态对话框](/modal-dialog-driven-websites-without-javascript-16e858615780)、[选项卡子部分](/tabbed-interfaces-without-javascript-661bab1eaec8)，甚至颜色切换几乎都可以完全由 CSS 处理，只需要很少的 JavaScript。

在颜色切换的情况下，我们真正需要 JavaScript 的唯一事情是记住/存储/恢复页面加载的状态。我们剩下的所有繁重工作都可以基于一个`INPUT`标签的状态在样式表中完成！

# 加价

在 HTML 方面，我们所需要的是一个“人造主体”——`DIV`，因为输入不能在`BODY`之前——以及一个隐藏的`INPUT`标签。“人造体”只是一个容器，我们把它当作体来对待，它通常是在模态驱动的布局中完成的，这样你就不会同时出现两个滚动条。在我们的情况下，它可以为页面的颜色和风格提供双重功能。

我们的`INPUT`和`DIV`:

```
<input
  type="checkbox"
  id="toggle_nightAndDay"
  class="toggle rememberState"
  hidden
  aria-hidden="true"
><div id="fauxBody">
  <!-- put your page content here -->
<!-- #fauxBody --></div>
```

都相对简单。这是一个复选框，ID 是一个`LABEL`指向所需要的，类“toggle”对于页面上所有隐藏的切换复选框是通用的，因为我们可能有其他的复选框，并且简单地将它们恢复为在我们的屏幕媒体样式表中可见。hidden 属性应该防止所有用户代理(浏览器是一个 UA，但 UA 并不总是浏览器)看到输入或与输入交互，但有些用户代理也需要 aria-hidden 属性来防止这种情况。我们可以通过将它们设置为 display:block，然后通过将其滑动到屏幕之外来隐藏它们，从而使它们在屏幕媒体样式表中再次可交互。

从那里，在我们的标记中的任何地方——比如说在主菜单中——我们可以创建一个元素，用一个`LABEL`来显示我们的实际切换状态。

```
<label for="toggle_nightAndDay"></label> 
```

HTML 最神奇的地方之一是，点击一个`LABEL`，即`FOR` 、`SELECT`或`TEXTAREA`的`ID`，在功能上与点击那个元素是一样的。因此，你可以把那个`LABEL`放在你的标记中的任何地方，点击它将选中/取消选中我们的复选框！

## 关于屏幕媒体的一个注记

你会看到我说了很多“屏幕媒体”，我指的是在你的样式表`LINK`上使用`media="screen"`，或者如果你是个白痴`STYLE`。例如:

```
<link
  rel="stylesheet"
  href="screen.css"
  media="screen,projection,tv"
>
```

如果你链接或使用的内联 CSS 只是为了你页面上的屏幕媒体而没有`media`属性，或者你发送的是`media="all"`，你就没有理解 CSS 的目的以及为什么它与 HTML 是分开的。你应该**而不是**将你的屏幕布局发送给印刷、听觉或其他任何东西。

较老的`projection`和`tv`媒体类型仍然被许多流通中的设备使用，以告诉它们我们知道我们在做什么，并且包含它们没有害处。在大多数情况下——比如在 Wii 或 DS 浏览器*(仍然基于 Opera 12)*或许多公共亭上——它对他们的行为非常类似于 viewport `META`告诉 mobile " *我们知道我们在做什么，用你愚蠢的专有废话把它剪掉！*

是的，它们在大约一年前的 HTML 5 中是“无效”的…这只是*如此可爱*在文档中没有版本控制，并且在那之前一直是有效的 HTML 5。有点像几乎同时交换 TBODY 和 TFOOT，同时使 TFOOT 优先的旧方法无效的心理困难。由于“活文档”的愚蠢，这是一个任意的、无意义的改变，没有跟踪它的方法。*以防你猜不出，不是粉丝。*

这只是让你的文档满足现代 HTML 5 验证毫无意义的众多因素之一。当“无效”的东西仅仅在一两年前在相同的 doctype 下是有效的，并且许多用户代理仍然需要它来正常工作时，他们可以去吮吸所说的验证警告、错误等等。

无论如何，如果你使用没有媒体属性的`LINK`或`STYLE`或者设置为“all ”,你就做错了。就像你 100%的时间在标记中看到`<style>`，99%的时间看到`style=””`，你看到的是 web 开发中那些讨厌的“3i”:无知、无能和不称职。

# CSS:

当你可以在样式表中搜索/替换时，CSS 变量总是让我觉得毫无意义。在以前的实现中，使用了大量浪费时间的无知垃圾，它们是 CSS 的“预处理器”，提供了很少甚至没有合法的好处。坦率地说，如果它们真的服务于一个目的，那就是要么让没有资格使用 CSS 的人尝试和修改值，要么纵容那些似乎使用了两倍到十倍所需 CSS 的人，因为他们从未掌握分组选择器。

更糟糕的是，在预处理器中，它们甚至不是变量，而是声明。更类似于常量或 define，tou 不能在 JavaScript 或样式表中动态改变它们。

原生 CSS 变量改变了这一切。当您通过使用 JS 添加更多 CSS 或者在基于选择器的现有样式表中更改它们时，整个布局都会发生变化……您可以通过伪类、伪状态和其他类似的选择器、组合符和方法来分配它们；例如:输入的 checked 属性！

在大多数关于原生 CSS 变量的教程和讨论中，你会看到很多人说你应该把它赋给`:root`伪元素。许多人甚至说，它应该只分配给所说的元素，这是一个赤裸裸的谎言。您可以将变量分配给任何选择器，它们将只应用于选择器内部。使用`:root`的原因是`<html>`是现代浏览器中`<body>`下面的一个渲染元素，你可能想给这些变量分配一个更高的级别。因为我们需要使用一个人造身体，所以没有理由这样做，所以我们可以在身体级别分配它们，然后用选择器/组合器将它们分配给我们的`div#fauxBody`

因此，对于我们正常的风格变量:

```
body { --hue:220;

  --body-background:hsl(var(--hue), 100%, 95%);
  --body-color:hsl(var(--hue), 100%, 10%);

  --header-background:hsl(var(--hue), 100%, 90%);
  --header-border-color:#0007;

  --footer-background:var(--header-background);
  --footer-border-color:var(--header-border-color);
  --footer-a-color:hsla(var(--hue), 100%,  15%, 0.8);
  --footer-a-hover-color:#000E;

  /* etc, etc, etc */}
```

我们分配给身体，然后我们可以使用这个选择器来分配我们的“暗”皮肤，当复选框被…选中时。

```
#toggle_nightAndDay:checked ~ * { --hue:240;

  --body-background:hsl(var(--hue), 100%, 5%);
  --body-color:hsl(var(--hue), 100%, 90%);

  --header-background:hsl(var(--hue), 100%, 20%);
  --header-border-color:#FFF4;

  --footer-background:var(--header-background);
  --footer-border-color:var(--header-border-color);
  --footer-a-color:hsla(var(--hue), 100%,  90%, 0.8);
  --footer-a-hover-color:#FFFE; /* etc, etc, etc */}
```

通用兄弟组合符“~”和所有元素选择器“*”的使用不仅影响到我们的`#fauxBody`，还影响到它的任何兄弟，如`.modal`。

*很多人说你应该“永远不要”使用“所有元素选择器”,因为它莫名其妙地“慢”了。结合一般的兄弟姐妹，我们限制它的应用，消除这种担忧。*

我接受了把所有事情都说成是“坏”的想法，但最近对我的重新评估越来越显示这种说法是赤裸裸的谎言。我可能会在某个时候写一篇文章来测试这个问题 [*，就像我对虚构的和无意义的类与选择器和组合子的“渲染时间”争论所做的那样。说真的，这个行业似乎充斥着人们编造一些事情来证明他们自己的个人喜好的做事方式。*](https://medium.com/me/stats/post/827ff7d15203)

还要注意我是如何分配`--hue`的。这样，我们可以快速轻松地改变整个布局的颜色，而不需要使用其他值。为了展示这一点，我将“白天”版本设置为“青色-蓝色”，但在“夜晚”，我选择了“真正的蓝色”。当创建布局时，其他人会重新着色，使用 HSL 和变量可以使生活变得容易得多。你想要红色的吗？设定`--hue=0;`你要绿色？`--hue=120;`

当需要在页面中分配这些颜色时，我们就像处理任何 CSS 变量一样，例如:

```
.modal,
#fauxBody {
 background:var(--body-background);
 color:var(--body-color);
}
```

对于`LABEL`,如果它在比如说，我们的`#mainMenu`在一个`LI.nightAndDay`里面，我们可以把它隔离出来，让生成的内容说“白天”或“晚上”,这样:

```
#mainMenu .nightAndDay label:before {
  content:"Day Mode";
}#toggle_nightAndDay:checked ~ #fauxBody #mainMenu .nightAndDay label:before {
  content:"Night Mode";
}
```

你可以把生成的内容换成任何你能想到的 CSS 能做到的效果和内容。因为我们通过生成的内容来输入文本，所以它对于非屏幕媒体设备来说是不存在的，只是进一步隔离了可能混淆或负面影响语音、盲文、印刷、搜索引擎等的内容。

让白天和黑夜的过渡看起来更好的最后一个调整:

```
*,
*:after,
*:before {
  transition:all 0.3s;
}
```

它也使用“全部”选择器。哦好吧。我发现一个小的——也是违反直觉的——怪癖是“*”并不针对生成的内容。哪一个…不是“全部”是吗？因此有了:after 和:before。请注意，由于嵌套问题，Blink*(chrome-like)*中的一些元素会以奇怪的级联方式过渡。仍然比两个极端之间的刺目的“闪光”更好看。

这是我们 90%以上的功能，只剩下…

# JavaScript

在脚本方面，我们需要做的就是存储输入的状态。这就是为什么我在`INPUT`中添加了“rememberState”类，因为它让我们可以轻松地在页面上挂接任何这样的元素。我们所要做的就是用 querySelectorAll 获取所有这样的输入，遍历它们以添加一个 onchange 事件侦听器来存储更改时的状态，并检查状态是否已存储。

```
(function() { for (var input of document.querySelectorAll("input.rememberState")) {
    input.addEventListener("change", inputRemember, false);
    if (input.id) input.checked = localStorage.getItem(
      "remember_" + input.id
    ) ? "selected" : "";
  }

  function inputRemember(e) { 
    localStorage.setItem("remember_" + e.currentTarget.id, (
      e.currentTarget.checked ? "selected" : ""
    ));
  } // inputRemember

})();
```

我选择“本地存储”而不是 cookies，因为它不会增加文件传输的开销。如果需要，您可以使用 cookie，然后让您的服务器端代码检测 cookie 是否已设置，如果是，则提供已经选中的复选框。这将删除代码的`if (input.id)`部分。尽管如此，这是一个不错的解决方案。

…因为我们按标签定位，所以此代码可以自动用于记住任何带有“rememberState”的复选框或单选按钮，而不仅仅是此代码库中的那个。

我把它放在一个隔离作用域的生命中，因为模块是“危险的”,并且不在脚本中应该声明它的地方提供隔离。我不喜欢违反关注点分离的事情。

# Internet Explorer 怎么样？

什么？！？哦，那个。😫

我最近转换到让网页“优雅地降级”到 IE 浏览器，只是简单地阻止了我在该浏览器中的所有 CSS 和脚本。他们得到的是没有样式的普通语义标记。我再也无法证明竭尽全力让已经过时十年的浏览器“正常运行”是有道理的。如果我们使用正确的语义，对于屏幕阅读器、盲文阅读器和搜索引擎来说足够好的东西对于没有人应该再使用的浏览器来说也足够好***！***

***可爱的把戏？X-UA。将其设置为 IE9，条件注释又神奇地起作用了。***

```
***<!-- don't even bother sending CSS to legacy IE -->
<meta
 http-equiv="X-UA-Compatible"
 content="IE=9"
>
<!--[if !IE]>-->
 <link
  rel="stylesheet"
  href="screen.css"
  media="screen,projection,tv"
 >
 <link
  rel="stylesheet"
  href="print.css"
  media="print"
 >
<!--<![endif]-->***
```

***嘣，你不再向 IE 的任何版本发送你的样式表了。我还建议加一条警告信息。***

```
***<!--[if IE]>
  <h2 style="color:red; display:block;" hidden aria-hidden="true">
    Error, Outdated Browser Detected
  </h2>
  <p>
    <strong style="color:red; display:block;" hidden aria-hidden="true">
      You are recieving a vanilla version of this page because your browser is a decade or more out of date. For full / proper appearance, please revisit in a modern browser.
    </strong>
  </p>
<![endif]-->***
```

***通知 IE 用户正在发生的事情。在一个评论中，由于有了`display:block;`，非可视 UA 将忽略这个消息(对 IE 中的 JAWS 用户来说很好),同时仍然显示在屏幕上***

***知道我是怎么说的吗，99%以上的时候你看到的`style=”something”`都是垃圾代码？向那 1%问好。***

***对我们的 JavaScript 也可以这样做。***

```
***<!--[if !IE]>-->
  <script src="nightAndDay.js"></script>
<!--<![endif]-->***
```

***标记中有额外的代码很糟糕，但总的来说还不到 1k，所以有什么坏处呢？只是不要做得太过火。***

# ***所以让我们来看看它的行动吧！***

***我把我的模态对话框驱动的网站演示，并把它变成了这个演示。你可以在这里看到现场直播:***

 ***[## 日日夜夜

cutcodedown.com](https://cutcodedown.com/for_others/medium_articles/nightAndDay/nightAndDay.html)*** 

***您可以通过点击主菜单右上角的“白天模式”或“夜晚模式”在两种模式之间切换。一定要检查一下子菜单、搜索和登录的虚拟模态对话框，它们也有自己的颜色设置……就像小屏幕菜单的“汉堡包”版本一样。***

***和我所有的例子一样，这个目录:
[https://cut code down . com/for _ others/medium _ articles/night and day/](https://cutcodedown.com/for_others/medium_articles/nightAndDay/nightAndDay.html)
是完全开放的，可以很容易地访问到这些有趣的内容。***

***我还为你们[放了一个整个项目](https://cutcodedown.com/for_others/medium_articles/nightAndDay/nightAndDay.rar)的. rar 文件，还有一个[。对于那些害怕“查看源代码”的人来说。](https://cutcodedown.com/for_others/medium_articles/nightAndDay/nightAndDay.html.txt)***

***我将[变量 CSS](https://cutcodedown.com/for_others/medium_articles/nightAndDay/nightAndDay.vars.screen.css) 与[布局样式表](https://cutcodedown.com/for_others/medium_articles/nightAndDay/nightAndDay.layout.screen.css)分开，这样你们更容易理解。在实际部署中，我会将这些合并到一个文件中，以节省一次握手，降低传输开销，而无需使用 HTTP2“推送”。***

# ***利弊***

***按照传统，我将首先讨论这种方法的问题。***

## ***不足之处***

1.  ***在任何版本的 IE 都不行。相应地计划。有一个不使用 CSS 变量的变体，可以在 IE9/更高版本中使用，但它涉及到为每个有颜色的元素做复杂的“通用兄弟”声明。实施起来很麻烦。***
2.  ***当设置了黑暗模式的 localstorage 值时，在缓存清空加载时，灯光主题会有短暂的闪烁，因为脚本偶尔会在 FCP 之后加载/运行——“第一次上下文绘制”。如果这对您来说是一个担忧或问题，我建议使用 cookie 而不是 localstorage，这样服务器可以提前将`INPUT`设置为“checked”。***
3.  ***你不能设计真正的页面`BODY`，取而代之的是必须依赖一个“人造体”`DIV`元素。这意味着一些试图跟踪窗口滚动的现成脚本不起作用，必须重写来跟踪`#fauxBody`的滚动位置。***
4.  ***标记中额外的 DIV 和代码，尤其是在添加 IE 回退时，会给 DOM 和带宽增加一些不必要的开销。实际超额加价仍低于 1000 英镑，这些担忧很容易被忽视。特别是如果您首先利用语义标记、选择器和组合子来保持 HTML 的小。***

## ***优势***

1.  ***CSS 完成了几乎所有的工作，使得过渡和加载更加平滑，并且更容易改变颜色。***
2.  ***这个只有 431 字节的脚本几乎没有增加额外的开销，并且可以重复用于您希望它记住的其他复选框或单选按钮。它还可以很容易地扩展，以记住其他表单元素的实际值。***
3.  ***因为它将一个`#fauxBody`作为我们`INPUT`的兄弟，所以它与`:target`驱动的模态对话框兼容。***
4.  ***通过使用`hidden`属性和`aria-hidden="true"`，我们能够使我们的控制元素对非视觉和/或非屏幕媒体用户界面的导航或页面阅读没有任何影响。它们对那些设备和用户来说根本不存在，因为它们实际上是不相关的。***
5.  ***由于`label`内部的状态可以像我们的变量一样被控制，我们可以在上面设置任何种类或样式的生成内容。这可能是一个“太阳”和“月亮”的图形，动画，或者任何你能用 CSS 做的事情。***
6.  ***因为使用`FOR`属性指向我们的`INPUT`的任何**`LABEL`都可以用来切换它，所以您实际上可以将标签放在您想要的代码中的任何地方。在菜单中，分开在页眉、页脚、内容中间。你甚至可以实现多个触发器元素！*****

# *****更多可能性*****

*****使用复选框或单选按钮`INPUT`来控制布局不仅可以用于颜色。你可以让用户选择一个字体集，改变填充甚至布局，等等。越想越觉得它是一种极其强大和有用的技术。*****

*****所有这一切都不会损害可访问性或可用性，也不必编写千字节(甚至兆字节)的无意义的 JavaScript，这些 JavaScript 通常与这种功能相关联。*****

# *****结论*****

*****随着 CSS 的不断改进和强大，许多人们倾向于使用 JavaScript 作为终极解决方案的事情越来越与 JavaScript 无关。对于大多数视觉和布局效果来说，JS 最好应该作为一种增强和权宜之计，用于 CSS 本身不能做的事情，而不是每一点功能的首选工具。*****

*****这进一步增强了我们保持关注点分离的能力，特别是内容(HTML)、表示(CSS)和行为(JS)。许多人会认为这是一种行为，但他说“行为”是一个风格控制的问题，在理想世界中，风格和布局与 JavaScript 无关。太多时候，人们沉迷于 JavaScript 而没有考虑“如果脚本被阻止，或者与 UA 无关，会发生什么？”。*****

*****同样，许多人应用他们的类、id 和 CSS，丝毫不关心非屏幕媒体用户。有一些技术——比如这个——可以给你所有视觉上的视觉控制，而不牺牲那些坐在那里使用屏幕媒体设备的人的可访问性需求。*****

*****这种技术功能强大，相对容易实现，最终给 CSS 变量提供了有用的东西。*****