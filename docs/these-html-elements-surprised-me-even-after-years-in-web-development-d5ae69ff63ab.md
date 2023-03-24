# 这些 HTML 元素让我惊讶，即使我从事了多年的 Web 开发

> 原文：<https://levelup.gitconnected.com/these-html-elements-surprised-me-even-after-years-in-web-development-d5ae69ff63ab>

## 看看一些不常见的 HTML 元素

![](img/7b669b0072edb88f7a22cd3870304e53.png)

我最近参加了一个 HTML 技能测试，其中一些问题是关于我以前从未见过的 HTML 元素和属性的。尽管我通过了测试，但我意识到我对 HTML 已经相当生疏了，于是决定从头开始。

很显然，在研究过程中，我碰到了一些我从未见过的 HTML 元素；这些甚至不在试卷上。这些元素的行为完全不同于我以前见过的任何其他元素。在这里，我们将看看这些有趣的 HTML 元素，以及它们何时会派上用场。

## 地图和区域

如果你运行上面的 CodePen 并悬停在 QR 码周围，你会注意到只有左上角的框和 QR 码中心的一小块区域是可点击的。

声明:如果你点击这些链接，你将被带到我的 GitHub 页面。

这是可能的，因为我只在图像的特定部分添加了超链接。`map`和`area`是可以用来实现这一点的两个元素。

代码本身很简单。我们创建一个`map`元素，它包含一个或多个`area`元素作为子元素。每个`area`元素定义了应该有一个超链接与之关联的区域。

下面的代码定义了一个圆形区域，中心在坐标`100,100`，半径为`50`。我选择这些值是因为图像的大小是`200px`。因此，使用`100,100`的中心将使圆形区域正好位于中心。

```
<map name="hit-areas">
  <area
    shape="circle"
    coords="100,100,50"
    href="..."
    target="..."
  />
</map>
```

下一步只是告诉图像本身使用这个地图。这可以使用`usemap`属性来完成。

```
<img
  usemap="#hit-areas"
  width="..."
  src="..."
/>
```

有许多形状可以用于`area`元素。有关您可以使用哪种形状以及每个形状的`coords`值的更多信息，请查看[本 MDN 页面](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/area)。

## 双向隔离

上面的代码演示了混合从左到右(LTR)和从右到左(RTL)语言时出现的常见问题。如果在文本框中输入 RTL 文本，输出将是`Hello 5 ! <name> people have visited before you`。相反，如果在文本框中输入 LTR 文本，输出将是`Hello <name>! 5 people have visited before you`。

RTL 案文没有意义。这里的问题是文本是按如下方式生成的:

```
const text = `Hello ${val}! 5 people have visited before you`
```

这里，如果`val`是 RTL 文本，它将把`! 5`拉到左边。这是因为浏览器使用一些规则来处理双向文本。浏览器的双向文本算法将在大多数情况下获得正确的文本方向，除了像这样的边缘情况。

这就是`bdi`(双向隔离)元素的用武之地。这个元素告诉浏览器把它里面的文本和它周围的文本分开。这确保了`! 5`不受用户输入文本方向的影响。因此，可以使用以下代码生成文本:

```
const text = `Hello <bdi>${val}</bdi>! 5 people have visited before you`
```

代码栏中还有一个复选框，允许您启用`bdi`。请注意，如果您输入 LTR 文本，那么无论我们是否使用`bdi`都没有区别。然而，当输入 RTL 文本时，情况就大不一样了。

有关`bdi`元素的更多信息，请查看[这个 MDN 页面](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/bdi)。

如果你想了解更多关于使用 RTL 语言的知识，请查看劳拉·沃里克的这篇文章。

[](https://medium.com/blackboard-design/fundamentals-of-right-to-left-ui-design-for-middle-eastern-languages-afa7663f66ed) [## 中东语言从右到左用户界面设计的基础

### Blackboard 设计团队一直与开发部门紧密合作，为中东市场准备我们的产品…

medium.com](https://medium.com/blackboard-design/fundamentals-of-right-to-left-ui-design-for-middle-eastern-languages-afa7663f66ed) 

查看[这篇由](https://uxplanet.org/right-to-left-development-tips-and-tricks-fa481e86ae26)[或`href="?show"`，所以你需要使用完整的链接。另一个原因是大多数第三方库将依赖于默认行为，其中没有使用`base`。](https://medium.com/u/61eefd2ebfab#some-heading)

[乍一看，`base`可以方便地重写 URL，但是在使用这个元素之前，我们应该考虑我们的开发环境。要阅读其他有`base`的人的经历，看一看这个](https://medium.com/u/61eefd2ebfab#some-heading) [stackoverflow 讨论](https://stackoverflow.com/questions/1889076/what-are-the-recommendations-for-html-base-tag)。

有关`base`元素的更多信息，请查看本页面[本 MDN 页面](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/base)。

## 最后的想法

有一些 HTML 元素很难在大多数网站上找到。这些元素中的一些可能很适合您正在开发或将来将要开发的应用程序。

然而，就像编程世界中的大多数事情一样，如果它们实际上是一个很好的匹配，并且如果与它们相关联的陷阱和缺点会影响您的应用程序，那么屏蔽它们是很重要的。

还有更多我们在本文中没有涉及的元素。如果你想像我一样追根溯源，这里有一个 HTML 元素的[参考。](https://developer.mozilla.org/en-US/docs/Web/HTML/Element)