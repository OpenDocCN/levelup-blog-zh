# 语义标记很可能不是你想的那样！

> 原文：<https://levelup.gitconnected.com/semantic-markup-probably-doesnt-mean-what-you-think-9e44d2439b18>

在我作为一名自由的可访问性和效率顾问从一家公司到另一家公司的旅程中，我经常与那些对语义标记是什么和不是什么有一些真正奇怪的概念的 web 开发人员发生冲突。从那些说“那不就是为了 SEO 吗？”从“这真的没关系”，到“这只是给编码人员看的”，有很多虚假信息、误解和简单的谎言。

尽可能简单明了地说:

> 语义标记只不过是“正确使用 HTML！”的一种病态的委婉说法

语义标记是 HTML 从一开始就存在的原因。这不仅仅是为了 SEO，因为它在 JumpStation — *第一个“真正的”基于 HTML 的搜索引擎* —两年前就有了语义，这在 Feltcher 的眼中是一个亮点。

语义也不意味着代码是人类可读的，如果是，我们会在页面中显示标记。有些人似乎认为 HTML 是让程序员互相理解的，这不是标签的目的。

标签——任何原始标签或任何当前有效的标签——也不“意味着”它们的默认外观。**如果你根据你想要的东西来选择你的 HTML 标签，那你就大错特错了！**

让我们再说一遍:

> 如果你是根据你想要的东西的样子来选择你的 HTML 标签，那你就大错特错了！

# 为什么我们会有这种“委婉语”？

**为了不冒犯那些在使用 HTML 3.2 风格的“表示性标记”方面把脑袋塞进 1997 年直肠的人**。这是一种说“你用错了 HTML”的方式，而不是真的当着他们的面说…这就是 web 开发的一个很好的问题，就是这种不愿意把屎叫做屎的娇媚。

当 HTML 问世时，蒂姆·伯纳斯·李正试图解决这样一个问题，即按照标准写作惯例编写的专业文档不能在线发送到所有不同的设备。有几十种不兼容的文件格式，它们都是基于表示概念，而不是语法和结构概念。考虑到每台计算机都有不同的大小、分辨率和文本显示能力，它们中的大多数甚至在布局上都是设备特定的。*他们中的许多人甚至没有程序员可访问栅格的“图形”。*

通过采用通用 SGML 语言并预定义标签列表以从结构、语法和句法的角度具有特定的含义，可以将正确编写的结构良好的文档传输到各种设备。然后，它落在设备上——或者更具体地说，设备上称为“用户代理”的软件——在自己的限制范围内翻译结构和语法意义。

![](img/d89d1533e0310601838ccaa3497265b8.png)

蒂姆·伯纳斯·李在欧洲粒子物理研究所，1989 年。这是 HTML 开始的地方。为了好玩，我在车库里有一台同样的显示器，连接着一台 Sun Blade 2000。

不管它是一台老式的雏菊轮电传打字机，还是 Linus Torvalds 使用的 21x22 显示器，是使用 TTY 的人，还是打印出来的人，还是 TBL 有幸在 CERN 访问的下一个工作站上的高端 1152x864 显示器。无论用户的计算机或其他访问设备多么有限或先进，同一文档都可以传达其完整的含义和结构。

这是一个相当聪明的想法，布局语言不是基于你想要的东西，而是基于它们在一个遵循专业写作规范的写得很好的文档中的语法和结构。

为什么如此可悲的是，大多数人选择他们的标签都是基于默认的外观，完全没有抓住重点！

# 那么什么是“恰当的语义”

正如我刚才所说的，每个标签——或者至少是重要的标签——都有基于正确语法的结构意义或目的。你根据标签的意思来选择标签，或者根据语法原因来选择标签在正确的写作中的用法。

让我们从结构开始，从那里开始工作。首先是编号标题；这些标签**不是指不同粗细和大小的字体！**我们来浏览一下。

**<h1>***(单数)* head **ing** *(单数)*描述当前文档所属的站点或文档/页面集合。*WhatWG 试图通过将我们重置为 H1 深度的部分标签来搞砸这一点，但随着时间的推移，越来越多的开发者告诉我们放弃这一点。*

**< h2 >** 标志着页面*的一个主要子部分的开始(使得 HTML 5 < section >标签多余且无意义)*。如果你前面没有< h1 >，你就不应该有< h2 >。页面上的第一个< h2 >应该标志着主要内容的开始。*(使 HTML 5 < main >标签变得多余且无意义)。*

**< h3 >** 由前一个 H2 开始的分段的开始。如果你没有 H2，你也不应该有 H3。

**< h4 >** 标题为由前一 H3 开始的分段的开始。如果你没有 H3，你也不应该有 H4。

想猜猜 H5 和 H6 是什么意思吗？

**< hr >** 主题/章节中的段落级别变化，其中标题文本是不需要的或不必要的。基本上，没有文字的 H2。**它并不意味着“在屏幕上画一条线”**，那只是屏幕媒体目标的默认外观！

以上标签组合在一起就是为什么如果你看到一个以 H5 开头的文档，你看到的是无知。如果你看到一个文档，它的网站标志不是一个 H1，里面有文字，但是页面标题/主要标题是一个 H1，你会看到无知。这就是为什么如果你看到一份文件把 H3 放在 H2 前面，它们是同一个“标题”,你看到的是无知、无能和不称职。web 开发中讨厌的 3i！“查看源”任何用 Bootstrap 为“眼罐 haz teh intarwebs”的主要示例构建的内容

它们的存在是为了给所有用户代理创建一个可导航的结构，而不仅仅是使用 web 浏览器的神奇的完美视觉。

**< p >** 一个合乎语法的段落。如一个或多个句子的完整思想。某人的地址**不是段子**。一个<标签> + <输入> **不是一个段落**。一个随意的句子片段或者“这恰好是文字”**都不是段落。**一个单独的< img >或< video >标签本身**不是一个翻转段落！！！**

*说真的，/不及格/初三英语。或者至少三年级英语在 70 年代是什么。那是什么，现在大学二年级英语专业？*

**< ul >** 或 **< ol >** 要点列表，分别无序和有序。这些应该是简短的语法要点或选择。这是语法要点，而不是“万岁，万万岁，万万岁”。再说一遍那只是默认的样子，**不是他们的意思！**

**<表>** 同样处理。对于单元格在两个坐标轴上都有特定关系的结构表。它不是为“眼睛 wunts teh 列。”

还有新闻快报的人，进入的标签不仅仅是和

| 。每次我看到一些一无所知的傻瓜这样做: |

```
<table>
  <tr class="title">
    <td colspan="4"><b>Shopping Cart</b></td>
  </tr><tr>
    <td class="heading"><b>#</b></td>
    <td class="heading"><b>Item</b></td>
    <td class="heading"><b>Unit Price</b></td>
    <td class="heading"><b>Total</b></td>
  </tr><tr class="data">
    <td>2</td>
    <td>Console</td>
    <td>$499.99</td>
    <td>$999.98</td>
  </tr><tr class="data">
    <td>2</td>
    <td>Controllers</td>
    <td>39.99</td>
    <td>78.98</td>
  </tr><tr class="total">
    <td colspan="3"><b>Grand Total</b></td>
    <td><b>1079.96</b></td>
  </tr>
</table>
```

我必须抑制住用湿鳟鱼反手打某人的冲动。为什么？因为正确的标记应该是:

```
<table>
  <caption>Shopping Cart</caption>
  <thead>
    <tr>
      <th scope="col">#</th>
      <th scope="col">Item</th>
      <th scope="col">Unit Price</th>
      <th scope="col">Total</th>
    </tr>
  </thead><tbody>
    <tr>
      <td>2</td>
      <th scope="row">Console</th>
      <td>$499.99</td>
      <td>$999.98</td>
    </tr><tr>
      <td>2</td>
      <th scope="row">Controllers</th>
      <td>39.99</td>
      <td>78.98</td>
    </tr>
  </tbody><tfoot>
    <tr>
      <th colspan="3" scope="row">Grand Total</th>
      <td>1079.96</td>
    </tr>
  </tfoot>
</table>
```

**< th >** 表示我们的标题， **scope=""** 表示标题标记事物的方向。 **<标题>** 作为表格的标题和/或描述其内容。 **< thead >** 标记表头， **< tfoot >** 标记表尾， **< tbody >** 标记实际数据单元格所在的表体。

并且**对于你可能想要的任何样式的数据来说，这已经足够了。你不需要像那些 BEM 傻瓜，或者彻头彻尾的 HTML/CSS“框架”白痴那样的类来破坏它。**

# *、**、、*和**，一连串的悲哀******

这些标签在这篇文章中有它们自己的主要部分，因为我见过的 100% A 级农场新鲜肥料的绝对量与它们有关。人们不停地胡说八道他们是什么，他们是干什么的，他们做什么。

**不！<b>T1**I>不“弃用”****

**不！**你不应该“停用 **< b >** 和 **< i >** 而只使用 **<强>** 和 **< em >**

**没有！< b >** 和 **< i >** 不是“严格意义上的表象”

HTML 2/更早版本中的所有原始标签都具有独立于其默认外观的语义，这实际上包括 **< b >** old 和 **< i >** talic。这让许多人感到困惑，但这一切都要追溯到专业写作规范。

让我们来看看这些:

**< b >** 是为了在专业写作时文本*会被*加粗。这并不是说文本必须是粗体，而是说它应该是粗体。这方面的一个例子是法律文件中的实体。你没有“更加强调”这个名字，你只是在标记它是一个名字。

**< i >** 是为了当你的文本*因为类似的原因而成为*斜体的时候。这方面的一个很好的例子是一本书的书名，你不能引用。

```
I read <i>Moby Dick</i> last night. As the protagonist in <cite>Moby Dick</cite> said, <q>Call me Ishmeal.</q>
```

看到了吗？

**<引用>** 是在你引用一个来源的时候用的。它也默认为斜体，但它有不同的含义。正如:

**< em >** 是强调和

**<强>** 是为了“更强调”

因此，我的一个朋友在 15 年前想出了一个例子:

```
*<i>GURPS,</i> <b>Steve Jackson Games'</b> flagship role-playing game, was first released in 1985\. Several licensed adaptations of other companies' games exist for the system, such as <i>GURPS Bunnies and Burrows.</i> However, <b>SJ Games</b> has no connection with <b>Wizards of the Coast</b>, producers of the <i>Dungeons and Dragons</i> RPG. <em>No <i>GURPS</i> content is open-source.</em> <strong>Do not plagiarize <b>SJ Games</b> work!</strong>*
```

是的，**中的**完全有效，甚至可以是语法或结构正确的！****

所有这些标签都有特定的用途，所以要正确使用它们！

# 但这看起来是我想要的，这有什么关系？

因为在视觉用户代理上，HTML 对于视力良好的用户来说不仅仅是看起来像什么。它是为屏幕阅读器(大声朗读页面的软件)，盲文阅读器，甚至不能显示图形绘制文本的设备…记住，搜索引擎没有眼球！

这些“媒体目标”中没有一个会给出一条飞来飞去的紫色鱼关于你的糟糕的布局、字体选择等等。

哦，但是对你来说看起来很好……**对你来说很好！这不是关于你，而是关于访问你网站的人。这就是所谓的可访问性，通过正确使用 HTML，你可以快速、简单、容易地获得它。你忽略了正确的语义，你只是让页面在某个地方、某个时间更难被某人使用！**

大多数开发人员在这个问题上的故意无知和完全无能确实令人震惊。看看那些由 bootstrap 开发者们吐槽的[无能的白痴垃圾标记](https://getbootstrap.com/docs/5.0/examples/album/)。

```
<header>
  <div class="collapse bg-dark" id="navbarHeader">
    <div class="container">
      <div class="row">
        <div class="col-sm-8 col-md-7 py-4">
          <h4 class="text-white">About</h4>
          <p class="text-muted">Add some information about the album below, the author, or any other background context. Make it a few sentences long so folks can pick up some informative tidbits. Then, link them off to some social networking sites or contact information.</p>
        </div>
        <div class="col-sm-4 offset-md-1 py-4">
          <h4 class="text-white">Contact</h4>
          <ul class="list-unstyled">
            <li><a href="#" class="text-white">Follow on Twitter</a></li>
            <li><a href="#" class="text-white">Like on Facebook</a></li>
            <li><a href="#" class="text-white">Email me</a></li>
          </ul>
        </div>
      </div>
    </div>
  </div>
  <div class="navbar navbar-dark bg-dark shadow-sm">
    <div class="container">
      <a href="#" class="navbar-brand d-flex align-items-center">
        <svg ae nk" href="http://www.w3.org/2000/svg" rel="noopener ugc nofollow" target="_blank">http://www.w3.org/2000/svg" width="20" height="20" fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" aria-hidden="true" class="me-2" viewBox="0 0 24 24"><path d="M23 19a2 2 0 0 1-2 2H3a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h4l2-3h6l2 3h4a2 2 0 0 1 2 2z"/><circle cx="12" cy="13" r="4"/></svg>
        <strong>Album</strong>
      </a>
      <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarHeader" aria-controls="navbarHeader" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
    </div>
  </div>
</header>
```

你怎么能以一个 H4 作为文档的开头呢？这个标签的意思是“它前面的 H3 的一个小节的开始”。这些一无所知的人选择他们的标签是基于外表而不是意义；这意味着他们没有资格编写一行该死的 HTML 代码，更没有胆量告诉别人如何去做。无止境的无意义的 DIV，无止境的无意义的类，内联在标记中的静态图像错过了缓存的机会，如果这将被模板化为多个页面，围绕不是文本的东西会/应该被强调，多余的咏叹调，没有 CSS 优雅的退化，这些白痴使用 JavaScript 做 CSS 的工作的数据属性…这是开发人员无能的一场灾难，使用适当的语义，更应该是:

```
<header>
  <div>
    <h1>Album</h1>
    <label for="toggle_navSections"></label>
  </div>
  <input type="checkbox" id="toggle_navSections" class="toggleNext" hidden>
  <div>
    <section>
      <h2>About</h2>
      <p>
        Add some information about the album below, the author, or any other background context. Make it a few sentences long so folks can pick up some informative tidbits. Then, link them off to some social networking sites or contact information.
      </p>
    </section><section>
      <h2>Contact</h2>
      <ul>
        <li><a href="#">Follow on Twitter</a></li>
        <li><a href="#">Like on Facebook</a></li>
        <li><a href="#">Email me</a></li>
      </ul>
    </section>
  </div>
</header>
```

你不喜欢那样子，那是 CSS 的工作。如果他们打算转而使用 flex，他们至少应该学会如何使用 ORDER。

H1 是描述网站的标题，H2 标志着子部分的开始，如果你打算使用 HTML 5，使用血腥的部分标签…但最糟糕的是:

# 表象课程让 22 年的进步付之东流！

CSS 从 HTML 中分离出来的一个重要原因是，这样你就可以针对特定的媒体设备。屏幕、语音/听觉、印刷等。[如果你正在使用像“文本-白色”、“背景-深色”、“列表-无样式”这样的类，或者像“col-sm-4 offset-md-1 py-4”这样的晦涩难懂的表达方式，你还不如回到使用<字体>、<居中>的时候，那些用于布局 y '的表格显然都错过了！](https://medium.com/swlh/html-css-frameworks-monuments-to-ignorance-incompetence-and-ineptitude-4c1db2571de9) **向 1997 年的前沿心态和方法论问好。**

HTML 是用来描述事物是什么，或者它们为什么会因为语法或结构的原因而接受样式。这不仅扩展到您的标签选择，还扩展到您如何选择您的类和 ID。好的，你在标记中说文本-白色…如果你要多皮肤，另一个模板将有蓝色的文本，你真的想改变 HTML 来改变布局/风格吗？白色文字对印刷真的有意义吗？我很确定语音/听觉目标会给你的文本一个飞行的颜色。

这就是“表现与内容分离”的含义。*我很惊讶有这么多的 derps 认为那是服务器端的概念。* CSS 用于描述特定媒体目标的内容。HTML 是用来从语法和结构上描述事物的，创建元素和顺序的关系，这样每个人都可以访问你的内容。

# 你一直说“媒体目标”，那是什么？

CSS 的整个概念之一是能够为特定设备设计 HTML 样式。为此， **<样式>** 和 **<链接>** 标签都有一个可用的`media=””`属性。它的存在是为了说明你的风格是为了什么。屏幕、印刷品、听觉、语音、投影…

实际上，你的风格应该是特定于媒体目标的，因此如果你看到一个没有媒体属性的<link>或

这也是为什么在大多数情况下应该避免使用 style= " "属性，因为它无法说明你的风格是针对什么媒体的。如果不是因为一些极端的例子——标签云、HTML/CSS 图表——我会说样式属性应该完全从 HTML 中移除！

这又是 CSS 存在的原因，不仅仅是说屏幕的东西“看起来像什么”，而是说屏幕、打印等的多种外观。这包括多屏幕出现的可能性。风格，布局，都应该是一个单一的统一标记的变量。通过这种方式来改变事物的外观，您不必钻研 HTML、服务器端模板代码或任何与标记无关的东西。

这就是为什么把表示——不管是标签还是类——放到 HTML 中，明确地说外观不仅是错误的，而且是愚蠢的。

同样，如果你看到有人说要使用像“text-white”或“w3-red”或“box-shadow”这样的类，为什么要跑…跑得远，跑得快，但最重要的是？**快跑！以这种方式编写 HTML 的人根本不需要编写一行 HTML 代码，更不用说告诉你如何去做了！**

# 但是有无障碍需求的用户真的那么重要吗？

我经常听到这种抱怨，通常是来自同一类人，他们说轮椅坡道很碍眼，把车停在残疾人车位上是因为“为什么他们应该得到特殊待遇”，以及我们这些天听到的所有其他自恋的反社会者。“我，我，我，他妈的任何不喜欢我的人”。

坦白说，这是一个彻头彻尾的“商业计划”。

我们当中那些真正关心他人的人，我们当中那些意识到理想是接触尽可能多的人的人，以及我们当中那些在可及性咨询领域工作的人都非常清楚这有多重要。这就是为什么在一个只有 JavaScript 功能的网站上撒尿是愚蠢的。这就是为什么没有一个优雅的降级计划来关闭/阻止脚本，禁用/不适用 css，甚至阻止图像，是短视的，并且几乎是告诉大量潜在用户滚蛋的捷径。

甚至有关于这方面的法律，如美国的《美国残疾人法案》(ADA)和英国的《平等法案》(EQA)。如果你不能满足 WCAG 最低标准——语义标记是其中的一部分——如果你是一个企业或政府组织，你就违反了法律。

…正如我多次说过的，如果多米诺骨牌和碧昂斯在这种事情上可以在法庭上受到指责，那么当小企业仅仅因为使用了一些愚蠢的“框架”或“站点生成器”而忽略这些基本而简单的方法时，他们还有什么机会呢？

同样，大多数这样的“捷径”对网站所有者没有任何作用，只是让他们趴在树林里的一根木头上，让他们像猪一样尖叫。

# 结论

如果你通过了四年级英语考试，或者至少是 70 年代的四年级，考虑到推特一代的读写能力，我想现在是大学英语专业的二年级学生，这些概念应该很熟悉并且容易使用。

底线 HTML 是用来从语法和结构上说明事物是什么或者将会是什么，**它不是用来说明事物看起来是什么样子的！！！**