# 你能解决这 10 个 HTML DOM 练习吗？

> 原文：<https://levelup.gitconnected.com/youre-an-html-dom-pro-if-you-can-solve-these-10-exercises-2b1014286071>

## 从简单到困难的练习。

![](img/1d6efb3c0973235a33f31ca040f4ccc7.png)

[斯科特·格雷厄姆](https://unsplash.com/@homajob?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

众所周知，JavaScript 是一种非常强大的语言，无论它是运行在浏览器环境中还是服务器环境中。在这两种环境中，它都有强大的 API 支持，以帮助轻松执行给定的任务。

谈到浏览器环境，一个特别有用的 API，也是最常用的 API 之一是 DOM API。DOM 允许我们修改给定的 HTML/XML 文档的结构和内容。

专注于纯 HTML 文档，我们有 HTML DOM API，它用特定于 HTML 的特性扩展了核心 DOM API。

总的来说，HTML DOM API 是如此庞大和复杂，以至于一个人可能需要一周以上的时间才能完全熟悉一半的核心接口。

在这方面，我们收集了 10 个练习来测试你对来自 HTML DOM API 的各种接口的理解，来自我们自己的 [JavaScript 课程](http://codeguage.com/courses/js/)。

让我们看看他们…

# 1.文本内容

**🔗链接:**[https://www . code guage . com/courses/js/html-DOM-text-content-exercise](https://www.codeguage.com/courses/js/html-dom-text-content-exercise)

📈**难度:**一般

**🎯目的:**在`Node`界面手动定义`textContent`属性。

当我们只想获得给定元素节点内的文本内容时,`textContent`属性是一个非常方便的属性。但是因为它是在`Node`接口上定义的，所以它不能只在元素节点上访问——我们也可以在文本和注释节点上访问它。在文本和评论节点上，`textContent`返回它们的`nodeValue`。

通过在`Node`接口上重新定义`textContent`属性，您将了解如何从给定节点中移除元素节点、如何添加新节点、如何使用`nodeValue`等等。

# 2.重新定义 replaceChild()

**🔗链接:**[https://www . code guage . com/courses/js/html-DOM-redefining-replace child-exercise](https://www.codeguage.com/courses/js/html-dom-redefining-replacechild-exercise)

📈**难度:**容易

**🎯目的:**使用`Node`接口的其他方法重新定义`replaceChild()`方法。

`replaceChild()`方法允许我们用另一个节点替换给定元素的特定子节点。从技术上来说，`replaceChild()`可以完全使用`Node`接口的另外两种方法来定义，这就是这个练习的目的。

# 3.节点计数

**🔗链接:**[https://www . code guage . com/courses/js/html-DOM-node-count-exercise](https://www.codeguage.com/courses/js/html-dom-node-count-exercise)

📈**难度等级:**容易

**🎯目标:**创建一个`Node`实例方法，统计给定节点下的所有节点，包括自身。

当在像 DOM 这样的树结构中工作时，递归在许多情况下是我们的朋友。一种情况是，我们希望确切地知道从一个给定节点开始的子树中有多少个节点，包括节点本身。

这就是这个练习的内容。您将了解递归、`childNodes`属性、如何检查给定的节点类型等等。

# 4.重新定义 insertAdjacentElement()

**🔗链接:**[https://www . code guage . com/courses/js/html-DOM-redefining-insertadjacentelement-exercise](https://www.codeguage.com/courses/js/html-dom-redefining-insertadjacentelement-exercise)

📈**难度:**一般

**🎯目的:**用 JavaScript 手动重新定义`Element`接口的`insertAdjacentElement()`方法。

很多开发者都知道`Node`接口的`appendChild()`和`insertBefore()`方法。但是可能只有少数人知道`insertAdjacentElement()`方法，这是一种在另一个元素旁边添加元素节点的非常方便的方法。

在本练习中，您将只使用`Node`接口的现有方法重新定义`insertAdjacentElement()`，通过这种方式，您将了解各种想法，例如如何使用元素的兄弟元素、使用`insertBefore()`方法、如何在提供错误类型的参数时抛出错误等等。

# 5.重新定义 firstElementChild

**🔗链接:**[https://www . code guage . com/courses/js/html-DOM-redefining-firstelementschild-exercise](https://www.codeguage.com/courses/js/html-dom-redefining-firstelementchild-exercise)

📈**难度:**容易

**🎯目的:**用 JavaScript 手动重新定义`Element`接口的`firstElementChild`属性。

`Element`接口定义了一个`firstElementChild`属性，该属性返回调用元素节点的第一个子节点，该元素节点本身就是一个元素。从技术上来说，它可以完全使用`Node`接口的属性来定义，这就是这个练习的目的。

您将了解节点的`firstChild`属性，如何设置 while 循环，以及如何检查给定的节点是否是元素。

# 6.构建表格

**🔗链接:**[https://www . code guage . com/courses/js/html-DOM-building-tables-exercise](https://www.codeguage.com/courses/js/html-dom-building-tables-exercise)

📈**难度:**一般

**🎯目标:**创建一个函数的两个变量，根据给定的项目数组构建一个 HTML 表格。

借助存储在 JavaScript 数组中的数据来构建 HTML 表格并不罕见。这个练习测试你这样做的技巧。除此之外，它还测试你对单独通过`innerHTML`和通过`appendChild()`和`insertBefore()`等变异方法进行 DOM 变异的理解。

# 7.重新定义类名

**🔗链接:**[https://www . code guage . com/courses/js/html-DOM-redefining-class name-exercise](https://www.codeguage.com/courses/js/html-dom-redefining-classname-exercise)

📈**难度:**容易

**🎯目的:**用 JavaScript 手动重新定义`Element`接口的`className`属性。

# 8.HTML 序列化

**🔗链接:**[https://www . code guage . com/courses/js/html-DOM-html-serialization-exercise](https://www.codeguage.com/courses/js/html-dom-html-serialization-exercise)

📈**难度等级:**难

**🎯目标:**创建一个函数，在检索时复制`innerHTML`的行为。

HTML 序列化是将元素节点转换为其对应的字符串表示形式的过程的名称。这似乎是一件容易的事情，但它要求我们在设计算法时考虑几个想法。在本练习中，您将学习元素节点的`attributes`属性、递归、`Node`接口的`childNodes`属性等等。

# 9.动态列表

**🔗链接:**[https://www . code guage . com/courses/js/html-DOM-dynamic-lists-exercise](https://www.codeguage.com/courses/js/html-dom-dynamic-lists-exercise)

📈**难度:**容易

**🎯目标:**用带有`data-list`属性的`<li>`元素填充那些`<ol>`和`<ul>`元素。

# 10.append()聚合填充

**🔗链接:**[https://www . code guage . com/courses/js/html-DOM-append-poly fill-exercise](https://www.codeguage.com/courses/js/html-dom-append-polyfill-exercise)

📈**难度等级:**容易

**🎯目的:**为`Element`接口的`append()`方法创建一个 polyfill。

`append()`方法允许我们在给定元素的最后一个子元素之后批量插入节点。本练习测试您对文档片段的理解，以及如何使用它们对节点进行分组，然后将节点一次性插入 DOM。

# 最后

至此，我们完成了 10 个练习，以测试您对 HTML DOM API 的了解。

如果你觉得这些练习很有帮助，别忘了为这篇文章鼓掌。此外，请在评论中告诉我们，你认为哪一项最具挑战性。

要了解更多关于 JavaScript 的知识，你可以考虑我们绝对免费的 JavaScript 课程，链接如下:

[](https://www.codeguage.com/courses/js/) [## 学习 JavaScript——最全面的课程

www.codeguage.com](https://www.codeguage.com/courses/js/) 

最后，如果你喜欢阅读我在 Medium 和 CodeGuage 上的内容，你可以给我买些☕.咖啡来支持我

# 更多文章阅读

[](/the-story-of-my-journey-to-codeguage-part-2-87fa914c3a4d) [## 我的代码之旅——第二部分

### 进入算法的世界

levelup.gitconnected.com](/the-story-of-my-journey-to-codeguage-part-2-87fa914c3a4d) [](/my-story-on-how-i-got-to-codeguage-part-1-62313a880f10) [## 我如何学会代码语言的故事——第一部分

### 通过 7 年级书上提到的链接进入编程。

levelup.gitconnected.com](/my-story-on-how-i-got-to-codeguage-part-1-62313a880f10) [](/do-you-know-about-rulers-in-visual-studio-code-f754b221a135) [## 你了解 Visual Studio 代码中的标尺吗？

### 作为编码时的指导，它们非常有用

levelup.gitconnected.com](/do-you-know-about-rulers-in-visual-studio-code-f754b221a135) [](/top-7-publishers-of-books-on-computer-technologies-that-you-must-know-b36d51e29bc1) [## 你必须知道的 7 大计算机技术书籍出版商

### 以及如何从其中一本开始。

levelup.gitconnected.com](/top-7-publishers-of-books-on-computer-technologies-that-you-must-know-b36d51e29bc1) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)