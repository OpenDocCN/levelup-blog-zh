# 如何编写永久有效的代码🔊

> 原文：<https://levelup.gitconnected.com/how-to-write-code-that-will-last-forever-f8c4b1c0c867>

## 这并不是因为每个人都不敢碰它

![](img/bde22f25b41be64acfcce865f5dd4ddf.png)

澳大利亚昆士兰州努沙国家公园[海豚角了望台](https://goo.gl/maps/ztGpcX46zYk)岩石海岸的一张照片。水是如此透明，以至于底部的岩石和沙子都清晰可见。像这样的公园有为公众精心设计的战略性了望台，因此它们的美丽可以持续很长时间。

听听音频版！

伟大的设计师不会创造出完美的杰作。他们建立了坚实的基础来支持一个环境，在这个环境中，建造一个杰作是不可避免的。

你还记得电影[太空堵塞](https://en.wikipedia.org/wiki/Space_Jam)吗？

电影的[网站创建于 1996 年。22 年后的今天，它看起来和过去一模一样。](https://www.warnerbros.com/archive/spacejam/movie/jam.htm)

这怎么可能呢？

尽管 HTML 格式随着时间的推移发生了变化，浏览器仍然知道如何支持旧的元素，比如`center`和`frame`标签。这使得 Space Jam 网站几十年来保持向后兼容。

默认情况下浏览器向后兼容的原因是它们的代码主要是为了解释 HTML 而编写的。他们以一种格式编写代码，这种格式可以表示页面的状态，而浏览器不需要知道页面是如何构造的。

他们只需要编写一次理解这种格式的代码，别无其他。

这里有一个例子:

[代表虚拟浏览器内部的代码](https://gist.github.com/FagnerMartinsBrack/3ff6fcba23abd62f4763fb0fb6b3dfc9)。有一个“页面内容”变量代表页面的根。该变量调用一个方法来检索每个“子元素”对于每个“子”元素，它运行一个名为“渲染元素和子元素”的函数该函数的代码检查元素是“脚本”、“容器”、“文本节点”还是“输入”如果它是一个“容器”，它会再次递归遍历所有元素。对于所有其他类型，它只是相应地呈现它们。

浏览器针对 HTML 编码是有意义的。如果他们要为网站的特定结构编写代码，他们必须编写如下内容:

[代表虚拟浏览器内部的代码](https://gist.github.com/FagnerMartinsBrack/eb3b13dacf72dbe227211b299ad83889)。它获取“容器”类型的第一个元素，然后获取假定存在于“容器”内部的“输入”类型的第一个元素。它包含在一个条件中，只有当网站是“google.com”时才运行

上面的代码假设页面上存在一个“容器”。它还假设“容器”有一个“输入”子元素。如果网站改变结构删除容器，每个浏览器都必须更新。

元素类型“容器”、“脚本”、“文本节点”和“输入”并不完全代表浏览器如何在内部定义元素。真正的浏览器需要担心[性能](https://medium.com/@fagnerbrack/html-and-state-a-challenging-way-to-look-at-web-performance-e3084fcff70d)和支持不同[种类的元素](https://dev.w3.org/html5/html-author/#elements)。

这里的要点是表明你可以**写一次代码**，它可以**解析任何东西**，而不需要知道另一个系统是如何构造的。想法是**建立能力**，而不仅仅是写一些漂亮的代码来解决眼前的问题。

> 您可以编写一次可以解析任何内容的代码，只要您针对功能进行编码。

在“现代”前端开发中，从服务器返回一个 JSON 是很常见的做法，该 JSON 的结构被绑定到它运行的确切网站:

[可运行代码](https://jsfiddle.net/fagnerbrack/zd690oe6/)表示两个服务器端响应，解析为 JSON 并呈现在客户端 HTML 模板中。第一个响应包含一个名为“user”的属性“user”属性包含一个对象文字，该对象文字包含一个名为“name”的属性“name”属性的值是一个名为“Mary”的字符串第二个响应只包含一个名为“用户名”的属性“用户名”属性的值也是名为“Mary”的字符串客户端 HTML 模板呈现“user”对象文字模型中的“name”属性。如果您更改来自服务器的响应，客户端将呈现字符串“未定义”

在之前的[帖子](https://medium.com/@fagnerbrack/front-end-separation-and-the-irrational-love-for-curly-braces-b6472f48bde7)中，我已经展示了这是如何在网站上被编码并被贴上“单页应用”的标签的。

问题是如果服务器响应的结构改变了，客户端代码就会中断。

那是极其脆弱的。

如果你开始应用构建网站功能的原则，用 JavaScript 和 JSON 重新创建 HTML，你最终会重新发明 HTML。

如果是这样的话，那么从服务器返回 HTML 就可以了！

> 酷，HTML 对浏览器来说很棒。然而，如果你从你的服务器发送 HTML，你实际上是在编写只适用于浏览器的代码！如果你不得不支持像移动客户端这样的东西，这似乎毫无意义，除非你想把你的网站放在一个“网络视图”中

没错。

HTML 是浏览器能够有效解析的唯一格式。这是一种在“交换”、“触摸”或“摇动”等控件出现之前就已经创建的格式。为了在浏览器中访问这些控件，您必须编写[自定义 JavaScript](https://developer.mozilla.org/en-US/docs/Web/API/Touch_events#Setting_up_the_event_handlers) 。

“网络视图”的概念在 [Android](https://developer.android.com/reference/android/webkit/WebView.html) 和 [iOS](https://developer.apple.com/documentation/uikit/uiwebview) 上都存在。它允许你将一个网站加载到一个本地移动应用程序中。创建一个在浏览器中运行的网站并把相同的代码库加载到“web 视图”中是非常容易的如果你这样做，你就可以避免为你的产品编写另一个前端。

> 仅仅因为它很容易，并不意味着它是理想的。

如果你有一些手机应用的经验，你会知道“网络视图”不会没有折衷。

它没有伸缩性。

随着更多的代码和功能被添加到“网络”中，“移动”版本将会变得更慢随着更多的代码和功能添加到“移动”中，“网络”版本将变得更慢

随着时间的推移，针对“移动”和“网络”的产品会有所不同在某种程度上，随着[复杂性开始增加](https://en.wikipedia.org/wiki/Software_entropy)，每个人都会意识到“移动”和“网络”之间的工作流程是完全不同的。

它们基本上是[解耦](https://medium.com/@fagnerbrack/why-do-you-need-to-know-package-coupling-fundamentals-8e0fa8e33e20)和[内聚](https://medium.com/@fagnerbrack/why-do-you-need-to-know-package-cohesion-fundamentals-8a3510cba2c1)的。

> 你可以将网站的相同代码库应用到移动“网络视图”中然而，这并不具有可扩展性。

那么还有什么选择呢？

你可以构建原生的移动应用程序——不需要 HTML——使用与使网络保持强大和向后兼容的相同的原则。

想象一下，一方面你有一个服务器为一个 HTTP 请求返回不同的标记语言，让我们称之为**DML**—**D**different**M**arkup**L**language。该标记语言符合为移动设备优化的特定格式。它允许程序编写代码来理解这种格式，就像浏览器编写代码来解释 HTML 一样。

另一边，你有客户。这是一款类似“浏览器”的原生移动应用然而，它不理解 HTML，也没有“web 视图”它被优化来解析 **DML** 并从服务器构建整个应用程序。

在这个设置中，你可以改变所有的用户界面，包括交互，而不需要改变你的应用程序中的任何一行代码。

这就像“HTTP 上的本地应用程序”

那不是很好吗？

> 一个可以改变服务器和更新移动应用的系统将会改变游戏规则。

好消息是它已经存在了。

作者称之为“ [jasonette](https://jasonette.com/) ”:

jasonette 作者的一段视频，介绍了其背后的核心思想。可以在服务器上编写用于创建移动应用程序的代码。

Jasonette 就是一个很好的例子。

你没有[必须以特定的顺序部署多个项目](https://medium.com/@fagnerbrack/front-end-separation-and-the-irrational-love-for-curly-braces-b6472f48bde7)的问题。您只需要在后端部署更改，它就会一直工作。

你永远不需要专门为一个移动应用程序写代码，你有一个可以理解格式的客户端和一个可以写入格式的服务器。

想象一本书。

这本书是由一个人脑写的，他用书面语言向另一个人脑传达信息。人类的大脑可以被认为是生物[图灵机](https://en.wikipedia.org/wiki/Turing_machine)。阅读的能力已经被编程到人脑中，这样它就能有效地理解别人试图传递的信息。

在这种情况下，作者的人脑代表服务器。读者的人脑代表浏览器。书就是网站。这本书的内容是 HTML 消息。

理解书的内容的能力是与生俱来的。对于一个人来说，被专门编码来阅读一本书是没有意义的。

由于进化造就了我们，书的内容可以改变，但人类不必改变。它唯一需要的是用特定语言阅读的能力。它只需要知道**通信格式**。

这就是浏览器对于 HTML 的意义:一个能理解一种**通信格式**的人工大脑。

> 只要服务器和客户端符合通用的消息格式，浏览器可以像阅读书籍一样高效地阅读网站。

有时候伟大的想法隐藏在众目睽睽之下。

如果您按照消息的格式而不是结构编码，您可以构建一个不需要多个有序部署的伟大系统。

就像你现在正在阅读的文本一样，计算机也可以理解一种通用语言，并有效地传递信息。

有了这个[基本](https://hackernoon.com/the-doctor-and-the-scalpel-78656f508c9a)原则，你今天就能创造出可能成为明天下一个 Space Jam 网站的东西。

你只需写一次……**就能永远使用的代码**。

感谢阅读。如果你有一些反馈，请在[推特](https://twitter.com/FagnerBrack)、[脸书](https://www.facebook.com/fagner.brack)或 [Github](http://github.com/FagnerMartinsBrack) 上联系我。

感谢[迈克·阿蒙森](https://twitter.com/mamund)对这篇文章的深刻见解。