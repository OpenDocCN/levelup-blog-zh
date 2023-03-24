# 如何从一个对 JavaScript 一无所知或反应迟钝的人开始构建一个拥有成千上万用户的 Web 应用程序

> 原文：<https://levelup.gitconnected.com/how-to-go-from-no-knowledge-of-javascript-or-react-to-building-a-web-app-with-thousands-of-users-7d644fe2eccc>

## (并获得报酬学习如何做)

![](img/f96ad0c6d4430109c6085d7f8078e86e.png)

照片由[劳塔罗·安德烈亚尼](https://unsplash.com/es/@lautaroandreani?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在最近完成计算机科学学位的第二年后，我认为自己是一个相当流利的 Java 和 Python 程序员，在大学之前有一些小项目的 HTML 和 CSS 经验。但我不知道 JavaScript 或任何 web 框架，如 React、Angular 或 Vue。

因此，当我决定在这个夏天尝试 web 开发时，这是一个严峻的挑战，需要在我开始之前克服。

如果你和我一样，有一定的编码经验，想进入 web 开发，但是不知道具体的必要技术，这篇文章就是为你准备的。请继续阅读，了解我在短短几周内掌握堆栈的步骤，构建我的第一个 web 应用程序，向数千用户发布它，并从头到尾获得报酬。

![](img/4852ad868a1e2d5553f5b261d3f5d0bd.png)

照片由 [Cytonn 摄影](https://unsplash.com/es/@cytonn_photography?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 找到项目和客户

学习新技术如何获得报酬？找到需要构建 web 应用的人，并向他们提供您的服务。这一举两得，因为你也得到一个可以集中精力的项目，这在学习的时候会非常有动力。

就我而言，我很幸运。在南非，一个非盈利的自然保护基金会通过一个相互联系找到了我，问我是否可以为他们做点什么，因为他们知道我以前为他们地区的公司做过一些网页设计(Wordpress 宣传册网站的东西，没什么特别的)。但是他们要求的更复杂，需要定制的解决方案。Wordpress 不打算砍掉它。我一直想学习一些网页开发技术，我知道这是一个很好的机会。

所以我引用了他们的话，解释了为什么他们的主网站 Wix 不够强大，并告诉他们需要几个月的时间来构建。有一些自由职业者的经验，我知道我可以引用多少来在对我这个学生来说是很多钱和削弱任何专业网站开发公司或研究生开发者之间取得平衡。

如果你想了解更多关于我创业的经验，你可以点击这里:

[](https://medium.com/swlh/how-i-started-a-successful-web-design-business-at-18-years-old-7b1a67c41333) [## 我如何在 18 岁开始一个成功的网页设计生意

### 一个你可以复制的计划，以发现机会并实现你的想法

medium.com](https://medium.com/swlh/how-i-started-a-successful-web-design-business-at-18-years-old-7b1a67c41333) 

至关重要的是，我没有透露我不知道任何技术，甚至不知道我将用来建立他们的网站的编程语言，或者我引用他们的那部分时间将用于学习所有这些。免责声明:我不推荐这种方法，除非你对自己的交付能力非常有信心。作为开发人员，我们不断地学习新的东西，可以在创造东西的同时学习，所以我不认为这种方法是不诚实的**，除非**你觉得你可能无法足够快地学习来构建稳定和高质量的东西，并按时交付。

![](img/ee279df7e86cedeaa69d50f1c824355a.png)

照片由[克劳迪奥·施瓦茨](https://unsplash.com/@purzlbaum?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 关注前端

当你第一次涉足 web 开发领域时，将东西推向市场的最快方法——以及学习初级开发人员最常追求的技能——是完全专注于前端。学习 JavaScript、HTML、CSS 和你选择的框架/库，一旦你有信心并想进一步发展你的技能，就离开后台。在本文的后面，我将具体讨论如何通过使用后端即服务(Baas)来实现这一点。

## HTML、CSS 和 JavaScript

如果你在高中或大学没有学过这些，有很多很棒的在线课程可以免费上。我用的都是 Codecademy 上的免费课程。如果你已经知道如何编程，这些是理想的。他们也有关于 HTML、CSS 和 JS 框架的类似课程。

[](https://www.codecademy.com/learn/introduction-to-javascript) [## JavaScript 教程:免费学习 JavaScript | Codecademy

### 学习 Javascript 和 JavaScript 数组，构建适合每种设备的交互式网站和页面。添加动态…

www.codecademy.com](https://www.codecademy.com/learn/introduction-to-javascript) 

## 选择一个框架

正如敏锐的读者现在已经注意到的那样，我选择了 react js——是的，我知道它在技术上是一个库，而不是一个框架。为什么？和其他人一样:这是最受欢迎的。为什么最受欢迎？因为每个人都选择它。为什么大家都选择它？你明白我的意思了…正反馈循环。

反正这篇文章不是说选哪个。每件事都有利弊，我知道不管我说什么，你都会花几个小时去研究它。我选了一个我知道在我明年夏天实习的一家公司广泛使用的，因为我觉得它会给我一个好的开始。我也是用 Codecademy 学的:

[](https://www.codecademy.com/learn/react-101) [## ReactJS 教程第一部分:免费学习 ReactJS

### 利用像 JSX 这样的 ReactJS 组件，通过这个流行的 JavaScript 库构建强大的交互式应用程序。

www.codecademy.com](https://www.codecademy.com/learn/react-101) 

除此之外，我还看了几个 YouTube 视频，觉得非常有用，尤其是这个:

加入[引导程序](https://getbootstrap.com/docs/3.4/css/)来使 CSS *稍微*不那么令人头疼，你就万事俱备了！

# 欺骗后端

我没有用 Python 或 Java 开发服务器，也没有像 Flask 或 Spring 那样构建需要学习更多框架的 REST API，而是选择将我所有的后端需求委托出去。我确实想学习后端开发，但因为我有一个客户，这种努力是时间紧迫的，我不想让我的大脑超载。后端即服务(Backend-as-a-Service)是一种云服务模型，允许开发人员专注于他们应用程序的前端，并有效地外包其他一切。

我选择的一项服务是谷歌自己的 [Firebase](https://firebase.google.com) 。Firebase 提供从数据库、机器学习能力、性能和发布监控，甚至托管的一切。这些特性中的大多数都可以在他们的免费层上获得，用于有限数量的 API 调用、上传、下载等。

我把我客户的应用程序完全托管在 Firebase 上，而且是免费的。他们已经有了一个域名，所以我只是得到了他们 cPanel 的详细信息，并建立了一个子域——也是免费的——把它链接到 Firebase 主机上，我们就成功了！

同样，这篇文章并不是在赞美 Firebase，但它非常简洁。如果你打算使用他们的现收现付的 Blaze 计划，确保你彻底测试了你的应用程序调用了多少 API，你不想像这些在 72 小时内在 Firebase 上花了 3 万美元的家伙一样结束。但是不要担心，高效的设计可以避免这种情况。

另一个很酷的开源选项是 [Supabase](https://supabase.com) ，它采用订阅模式，而不是现收现付。

![](img/b3beb079b079129bed2f1597525819d7.png)

[猎人哈利](https://unsplash.com/@hnhmarketing?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 构建您的应用程序

所以，你知道一点 HTML，CSS 和 JavaScript，你已经选择并学习了前端框架的基础知识，你已经有了一个为你提供的后端。是时候进入有趣的部分了！

我建议从观看 YouTube 视频开始，视频中的人们在你的框架中构建你想要的特定特性。例如，如果你知道你需要注册/认证/登录功能，看着一些人创建这些功能，然后复制/改编/合并你所看到的，很快你就能够自己实现这些功能，而不需要参考。

# 总结

Web 开发技能将永远处于高需求状态，构建应用程序和网站只是一种乐趣。这是我学习的过程，希望你也能让它为你工作！从我们的生活中抽出时间来学习一项新技能是令人畏惧的，也是一项严肃的承诺。在学习和开发这个应用程序的时候，我正在做一份全职的暑期实习，所以我最后只能在晚上做。这就是为什么我主张如果可以的话，找到一种可以获得报酬的方法，这是一个巨大的动力，让我觉得这更值得我花时间。这也有助于我满足于知道我的创作实际上会被现实生活中的人们所使用。如果不是因为这个双重打击，我可能仍然没有时间，但我很高兴我做到了。

[**订阅**](https://medium.com/subscribe/@adenhaus) 📚为了不错过我的新文章，如果你还不是一个中等会员， [**加入**](https://medium.com/@adenhaus/membership) 🚀去读我所有的，还有成千上万的其他故事！

## 参考

*   **代码学院**https://www.codecademy.com/learn
*   **自举**https://getbootstrap.com
*   **什么是 BaaS？**[https://www . cloud flare . com/en-GB/learning/server less/glossary/back end-as-a-service-baas/](https://www.cloudflare.com/en-gb/learning/serverless/glossary/backend-as-a-service-baas/)
*   **https://firebase.google.com**火焰山
*   **Supabase**https://supabase.com

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)
*   🚀👉 [**软件工程师的顶级工作**](https://jobs.levelup.dev/)