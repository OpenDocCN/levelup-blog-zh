# 使用 SASS Mixins 的媒体查询——一个可重用性和便利性的故事

> 原文：<https://levelup.gitconnected.com/media-queries-with-sass-mixins-a-tale-of-reusability-and-convenience-140416be11e7>

## 通过将媒体查询编写为独立的代码块来改进您的代码。

![](img/55239b369d98bfcdc2ecb97021c1f8dd.png)

马文·迈耶在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

有时候，我觉得生活在一个有 CSS 预处理程序来让我们的生活变得更容易的时代是很幸运的。除了其他优点，像 SASS 这样的预处理器可以让我们跟着干(不要重复自己)。对于那些不熟悉这个原则的人来说，它背后的主要思想是我们应该避免在我们的文件中重复相同的代码。让我们看看 SASS 如何帮助我们在设置媒体查询时做到这一点。

# 步骤 1—断点大小:

让我们通过将屏幕尺寸声明为变量来标准化它们。

# 第 2 步—混合:

使用 if/else 语句将媒体查询设置为 mixins。

# 步骤 3-样式表:

我们的媒体查询已经可以在样式表中使用了。在下面的例子中，“主按钮”根据屏幕大小改变背景颜色。

方便的部分来了。假设在项目的后期，您决定将小屏幕尺寸设为 700 像素，而不是 768 像素。有了上面我们创建的东西，你所要做的就是修改你的变量文件，把 *$sm* 的值改成你想要的值。

这是使用 mixins 编写媒体查询的一种干净简单的方式，对于小规模项目来说应该足够了。对于其他更加健壮和复杂的方法，[查看此链接。](https://css-tricks.com/approaches-media-queries-sass/)