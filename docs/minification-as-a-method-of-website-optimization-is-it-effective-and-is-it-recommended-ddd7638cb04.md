# 缩小作为一种网站优化的方法，是否有效？而且推荐吗？

> 原文：<https://levelup.gitconnected.com/minification-as-a-method-of-website-optimization-is-it-effective-and-is-it-recommended-ddd7638cb04>

速度优化是每个开发人员在编写任何网站时都应该考虑的一个非常重要的方面，因为一个需要花费很长时间来加载和响应的网站通常会破坏用户的体验。随着每天网络上网页数量的增加，你应该希望你的网站脱颖而出，因为糟糕的用户体验往往会将用户从你的网站推向其他提供良好用户体验的网站。缩小(我们将在本文中讨论)是一种流行的速度优化方法，经常推荐给 web 开发人员。

![](img/07f635d9226f84e943c94a7e9c70af8f.png)

**本文涵盖**

什么是缩小？

缩小如何优化程序？

缩小的效果如何？

缩小的方法

缩小和压缩一样吗？

速度优化的缩小

*所以，*

**什么是缩小化？**

缩小意味着最小化，它是从源文件中删除不影响网站功能(或行为)的代码的过程，这通常是为了优化速度。我们所说的不影响网站功能的代码是指像空格、注释、分号等代码。添加到代码中以使其对人类来说更可读。这样的代码通常会被运行该代码的服务器忽略。

**例子**

未缩小的版本

```
@import url(‘https://fonts.googleapis.com/css2?family=Lato&family=Martel+Sans&family=Open+Sans:wght@300&display=swap');*{margin: 0px;font-family: ‘Lato’, sans-serif;padding: 0px;font-family: ‘Lato’, sans-serif;}a{text-decoration: none;}ul{list-style: none;}.showcase{width: 100%;height: 692.594px;background-image: url(https://assets.nflxext.com/ffe/siteui/vlv3/b321426e-35ae-4661-b899-d63bca17648a/044b9365-d6b8-4e45-98b0-3ace7d99ffd3/BD-en-20220926-popsignuptwoweeks-perspective_alpha_website_medium.jpg);background-position: center;background-size: cover;position: relative;z-index: 2;border-bottom: 8px solid #222;}.showcase:after{content: ‘’;position: absolute;width: 100%;height: 100%;left: 0;top: 0;background-color: rgba(0, 0, 0, .5);z-index: -1;}.menu{padding-top:28px ;margin: 0 56px;display: flex;justify-content: space-between;align-items: flex-start;}.menu img{width: 135px;height: 41px;}.menu a{background-color: #e50914;font-size: 16px;font-weight: 600;padding: 7px 17px;color: #fff;border-radius: 5px;}
```

和缩小版

```
@import url(‘https://fonts.googleapis.com/css2?family=Lato&family=Martel+Sans&family=Open+Sans:wght@300&display=swap');*{margin: 0px;font-family: ‘Lato’, sans-serif;padding: 0px;font-family: ‘Lato’, sans-serif;}a{text-decoration: none;}ul{list-style: none;}.showcase{width: 100%;height: 692.594px;background-image: url(https://assets.nflxext.com/ffe/siteui/vlv3/b321426e-35ae-4661-b899-d63bca17648a/044b9365-d6b8-4e45-98b0-3ace7d99ffd3/BD-en-20220926-popsignuptwoweeks-perspective_alpha_website_medium.jpg);background-position: center;background-size: cover;position: relative;z-index: 2;border-bottom: 8px solid #222;}.showcase:after{content: ‘’;position: absolute;width: 100%;height: 100%;left: 0;top: 0;background-color: rgba(0, 0, 0, .5);z-index: -1;}.menu{padding-top:28px ;margin: 0 56px;display: flex;justify-content: space-between;align-items: flex-start;}.menu img{width: 135px;height: 41px;}.menu a{background-color: #e50914;font-size: 16px;font-weight: 600;padding: 7px 17px;color: #fff;border-radius: 5px;}
```

上面的代码示例显示了 CSS 文件的缩小版本和未缩小版本。你可以看到，未缩小的版本比缩小的版本更具可读性和可理解性，为了速度优化牺牲了可读性。

**缩小如何优化程序？**

对此的简单回答是，缩小通过减少服务器在下载和加载代码时需要担心的代码数量来减小程序的文件大小，从而加快加载速度。由于服务器现在需要下载和运行的文件较少，文件较小的网站比文件较大的网站加载速度更快。

*现在，*

**缩小的效果如何？**

缩小可以将文件大小减少 50%。这可以转化为 1 到 3 秒的速度提升。虽然速度差异可能不是你把你的网站从慢速提升到极速所需要的全部，但是轻微的速度差异对访问者来说意味着很多，也增加了你在谷歌搜索中排名更高的机会。

谷歌的搜索团队宣布

> “速度会是一个 [***排名信号***](https://webmasters.googleblog.com/2010/04/using-site-speed-in-web-search-ranking.html) 对于 2010 年桌面搜索和截至 2018 年 7 月的 [***页面速度会是一个排名*** ***因子对于移动搜索***](https://webmasters.googleblog.com/2018/01/using-page-speed-in-mobile-search.html) 太”
> 
> [***—谷歌***](https://developers.google.com/web/updates/2018/07/search-ads-speed) ***。***

而且，2018 年谷歌的研究表明

> “我们还用大量的反弹和转换数据训练了一个深度神经网络——一个模仿人脑和神经系统的计算机系统。预测准确率为 90%的神经网络发现，随着页面加载时间从 1 秒增加到 10 秒，移动网站访问者反弹的概率增加了 123%。同样，当一个页面上的元素——文本、标题、图像——从 400 个增加到 6000 个时，转化的可能性会下降 95%”。
> 
> [***—谷歌/索斯塔研究，2017***](https://www.thinkwithgoogle.com/marketing-strategies/app-and-mobile/mobile-page-speed-new-industry-benchmarks/)

缩小你的代码也会带来一些好处，比如改善用户体验，减小文件大小，以及提高网站的性能。

**缩小的方法**

在线缩小工具

这个过程很简单。你所要做的就是访问一个在线缩小工具，插入你的代码，点击缩小，然后等待它为你完成整个缩小过程。虽然这听起来很容易，但对于大型项目来说并不方便，因为这个过程中涉及的复制和粘贴会令人厌倦和沮丧。想象一下，在一个大项目上工作，想用这种方法缩小它。你必须单独复制每个文件，在在线缩略器中插入并缩略它，然后复制并粘贴到你的代码编辑器中。你可以看到这个过程有多累。这就是为什么它主要只推荐给较小的项目。

一些伟大的在线缩小工具包括

[***HTML 缩小器***](https://kangax.github.io/html-minifier/) 为 HTML

[***清理 CSS***](https://www.cleancss.com/css-minify/) 获取 CSS

[***Toptal Javascript minifier***](https://www.toptal.com/developers/javascript-minifier)和 compressor for Javascript

所以我们来看看其他方法。

命令行工具

命令行工具是另一种可以用来精简代码的方法。与在线缩小工具不同，它们在本地运行，不需要互联网连接即可使用，而且与在线缩小工具相比，自定义其输出也更容易。

要使用命令行工具进行缩小，您必须将其安装在您的计算机上。

一些很棒的命令行缩小工具包括:

[***npm***](https://www.npmjs.com/package/css) 为 CSS。

[***uglifyjs***](https://www.npmjs.com/package/uglify-js)为 javascript。

WordPress 插件

WordPress 插件是缩小网站的另一种方式。如果你运行一个 WordPress 网站，那么没有比使用 WordPress 插件更好的方法来缩小你的网站，比如[***W3 Total Cache***](https://wordpress.org/plugins/w3-total-cache/)、 [***蜂鸟***](https://wordpress.org/plugins/hummingbird-performance/) 和 [***【自动优化***](https://wordpress.org/plugins/autoptimize/) ，它们可以让你做到这一点。有了这些插件，你只需点击几个按钮，就可以轻松优化你网站的文件、图片等。

加拿大

内容交付网络(CDN)是缩小网站的另一种方式。使用 CDN 会自动缩小存储在其服务器上的网站文件，而无需您做任何工作。

软件构建工具

如果使用软件构建工具像[](https://www.packer.io/)*，[***大口***](https://gulpjs.com/) ， [***蚂蚁***](https://ant.apache.org/) ，*[***maven***](https://maven.apache.org/)等。可以肯定的是，这些工具提供了缩小功能。您只需搜索工具的文档或集成库就能找到答案。**

****缩小和压缩一样吗？****

**我看到许多人困惑的一件事是“缩小和压缩是一样的吗？”。虽然缩小和压缩具有类似的功能，即“优化网站”，但它们的工作方式不同。压缩通过使用像 GZIP 这样的压缩算法将代码改写成二进制格式来优化网站，而缩小通过减少空白、注释等来达到同样的效果。在代码中。**

****速度优化缩小，最终想法****

**在处理速度优化时，有许多方法，缩小是我喜欢使用的方法之一，也推荐给其他人。让你的代码不被缩小会使它更容易被人阅读，但是运行代码的服务器并不在乎，他们只需要一个他们能处理的代码。所以我会建议你缩小你的网站，因为这样可以加快速度，而且不会影响代码的功能。**