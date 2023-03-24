# 用 HTML 和 CSS 创建图像的艺术(用 python 欺骗了一点)

> 原文：<https://levelup.gitconnected.com/the-art-of-image-creation-with-html-and-css-cheating-a-little-bit-with-python-33a89dfe2fb6>

![](img/c7172c6bb8897d3f408b1e82b3f9b57b.png)

Elena Mozhvilo 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

你准备好让你的图像创作技巧更上一层楼了吗？只需 HTML 和 CSS，您就可以释放您的艺术潜能，创建令人惊叹的图像，让您的朋友印象深刻。在这篇文章中，我将向您展示一个简单而强大的技术来创建美丽的图像。当然，在这种情况下 Python 也会帮助我们。

但首先，让我自我介绍一下。我的名字是 StackZero，我对网络安全领域充满热情。如果你和我一样感兴趣，请在这里和我的博客上关注我的工作。如果你还不是网络安全的粉丝，我希望用我的见解和专业知识改变你的想法。

现在，让我们深入探索 HTML 和 CSS 图像创建的精彩世界吧！在我的指导下，你很快就能创造出令人惊叹的图像，并且乐在其中。所以，拿起你最喜欢的饮料，戴上你的创意帽子，让我们开始吧！

# 这个想法

![](img/4d644dc1e6862fb3e2005027b8389999.png)

照片由 [Johannes Plenio](https://unsplash.com/@jplenio?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这个想法很简单，一幅图像只是一个由像素组成的 2D 矩阵！更接近这个结构的 HTML 标签是一个表格。那么如果我们创建一个由 1px 个单元格组成的表格呢？每一个细胞都涂上相同颜色的图像，我们想要复制。

即使它在概念上非常简单，手动也是不可行的！试着以一个低质量的图像为例，比如 800x600，你必须手动给 480000 像素上色！一年都不够！因此，我将向您展示一个很好的 Python 脚本，它将为您完成繁重的工作，因此您只需展示您的 HTML 工作。

准备好开始编码了吗？让我们开始使用 HTML、CSS 和 Python 创建一些漂亮的图片吧！

![](img/f7084c48c842f1d95f1ef059ad0622bd.png)

[布拉登·科拉姆](https://unsplash.com/@bradencollum?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 代码

我假设您的机器上已经安装了 python 3。如果你还没有安装它，现在就运行[这个页面](https://www.python.org/about/gettingstarted/)并修复它！

现在我想向您展示代码，然后对其进行评论，但在继续安装[枕头库](https://pillow.readthedocs.io/en/stable/)之前:

```
pip install Pillow
```

这是代码，你也可以在我的 [GitHub 库](https://github.com/StackZeroSec/image_to_html)中找到。

```
 from PIL import Image

if __name__ == "__main__":
    image_path="to_convert.jpg"
    image = Image.open(image_path)

    w, h = image.size

    out_html = "<html><head></head><body><table cellpadding=0 cellspacing=0>"
    for j in range(h):
        out_html+="<tr>"
        for i in range(w):
            pixel = image.getpixel((i, j))
            out_html+=f'<td style="background: rgb({pixel[0]}, {pixel[1]}, {pixel[2]}); height: 1px; width:1px;"></td>\n'
        out_html+="</tr>"

    out_html += "</table ></body></html>"
    with open("out_html.html", "w") as f:
        f.write(out_html)
```

我完全知道这不是你能看到的最好的代码，但是我希望它非常清楚！

下面是代码工作原理的简要说明:

1.  代码做的第一件事是从 Pillow 库中导入图像模块。该模块提供读取和操作图像数据的功能。
2.  在 main 函数中，代码指定了将要转换的图像文件的路径。然后，它使用 Image.open()函数打开图像并读取其像素数据。
3.  接下来，代码使用 size 属性获取图像的宽度和高度。这些值用于确定 HTML 表格中的行数和列数。
4.  然后，该代码创建一个空的 HTML 表，其中 cellpadding 和 cellspacing 设置为 0。这是通过将包含表格开始和结束标记的字符串连接到 out_html 变量来实现的。
5.  在下一步中，代码使用嵌套的 for 循环遍历图像中的像素。对于每个像素，代码使用 Image.getpixel()方法获取像素的颜色。然后，它向 out_html 字符串添加一个新的表格单元格，将单元格的背景颜色设置为像素的颜色。
6.  在处理完所有像素之后。该代码关闭该表，并使用内置的 open()和 write()函数将 out_html 字符串写入一个 html 文件。这就完成了转换过程，并生成了一个可以在 web 浏览器中查看的 HTML 文件。

# 结论

感谢您阅读我的内容。如果你喜欢它，请考虑跟随我，因为它真的会帮助我。我努力在我的博客上创造高质量的内容，所以如果你还没有，请检查一下。如果您有任何问题或想法，请随时联系我。我总是乐意讨论并尽我所能提供帮助。感谢您将我视为满足您需求的资源。

*如果你喜欢我的内容，请考虑在 Medium 上关注我，了解我的最新文章。如果你还不是中级会员，可以考虑使用我的推荐链接来注册——这不会额外增加你的费用，但对我来说意义重大。谢谢大家的支持！*

[](https://medium.com/@stackzero/membership) [## 用我的推荐链接加入媒体- StackZero

### 我们的最新报道(以及数以千计的其他报道)一经发布，您就可以立即获得。成为会员后，您将获得所有权限…

medium.com](https://medium.com/@stackzero/membership) 

# 您可能喜欢的其他文章:

[](/hard-to-find-answer-create-an-ai-expert-who-answers-for-you-e24c1656c42f) [## 很难找到答案？打造一个为你解答的 AI 专家

### 了解如何构建您的助手，在没有任何人工智能知识的情况下回答您的问题。

levelup.gitconnected.com](/hard-to-find-answer-create-an-ai-expert-who-answers-for-you-e24c1656c42f) [](/6-platforms-that-will-fuel-your-coding-motivation-404e77321f30) [## 5 个能激发你学习动力的平台！

### 一个最佳平台的简短列表，在那里你可以一边玩一边练习编程。

levelup.gitconnected.com](/6-platforms-that-will-fuel-your-coding-motivation-404e77321f30) [](https://medium.com/codex/create-chuck-norris-jokes-application-in-7-minutes-712887cb0956) [## 如何在 7 分钟内创建 Chuck Norris 笑话应用程序！

### 查克·诺里斯将不会出现在《敢死队 3》中，因为史泰龙再也不想感受那种恐惧了。

medium.com](https://medium.com/codex/create-chuck-norris-jokes-application-in-7-minutes-712887cb0956) [](https://medium.com/nerd-for-tech/introducing-the-simple-way-to-create-youtube-downloader-with-python-253637e3067a) [## 介绍用 Python 创建 Youtube 下载器的简单方法

### 初学者快速教程，关于如何建立你的个人 YT 下载

medium.com](https://medium.com/nerd-for-tech/introducing-the-simple-way-to-create-youtube-downloader-with-python-253637e3067a) [](https://python.plainenglish.io/up-in-arms-about-code-your-activity-advisor-in-python-84c79215e862) [## 竭力反对用 Python 编写活动顾问？

### 一个初学者的快速教程，让你在空闲时间开发一个活动顾问！

python .平原英语. io](https://python.plainenglish.io/up-in-arms-about-code-your-activity-advisor-in-python-84c79215e862) [](/how-to-build-an-ai-tale-generator-for-your-kids-34b8db153054) [## 如何为你的孩子建造一个人工智能故事发生器

### 这是一个非常简单的教程，讲述了如何使用预先训练好的模型通过 python 生成文本。

levelup.gitconnected.com](/how-to-build-an-ai-tale-generator-for-your-kids-34b8db153054)