# 如何在 Puppeteer 的 HTML 输出中包含 iframes

> 原文：<https://levelup.gitconnected.com/how-to-include-iframes-in-puppeteers-html-output-8f2f36b5f36c>

这将是一个简短的教程，关于我们前几天在 [textcloud](https://www.textcloud.co) 遇到的一个问题。

在内部，我们使用 headless Puppeteer 和一系列隐形插件来抓取网页。当然，木偶师可能比仅仅用 Python 做`import requests`要慢，但现在是 2021 年，我们知道成功的网络抓取只需要渲染整个网站，如果你想得到合适的结果。

虽然在 Puppeteer 加载页面后，一个简单的`const html = await page.content();`可以很好地满足大多数目的，但不幸的是，它不包含嵌入的 iframes 的内容。

![](img/ebedf5d7b524a1273660fc9bdc0b214d.png)

用木偶师渲染页面。就像画画一样，是吧？|照片由[伊尔努尔·卡里穆林](https://unsplash.com/@kalimullin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 在木偶戏中渲染 iframes

事实证明，这不是 iframes 的内容尚未加载的问题。拍个整版截图很快就排除了这种可能。在我们的例子中，也有必要向下滚动到页面的底部，让浏览器有机会加载所有的内容，但是稍后会详细介绍。

因此，如果我们知道 Puppeteer 已经完全加载了页面，并且屏幕截图上的所有内容都是可见的，为什么 iframe 元素是空的呢？

> *如果我们确认页面已加载，为什么 iframe 元素为空？*

我们了解到它们确实被渲染了，但是木偶师将它们包含在一个单独的*帧*中，而这个帧不包含在`page.content()`结果中。

相反，我们必须手动插入它们的内容:

1.  查找页面上的所有 iframes。
2.  获取每个 iframe 元素的`ContentFrame`及其`ExecutionContext`。
3.  在 iframe 的`ExecutionContext`中，运行一个返回其 HTML ( `document.querySelector("*")`)的函数
4.  将 HTML 插入到页面的 iframe 元素中。

这是最后的片段。

# 额外收获:自动滚动到页面底部

这是我们用来一直向下滚动到页面底部的小片段。根据你的喜好选择一个距离(这里是 T7)和频率(这里是 T8)，但是注意不要滚动得太快，否则浏览器不会加载所有内容。

有兴趣看看我们的刮削技术吗？[申请 textcloud 的早期访问](https://form.typeform.com/to/os5vkuEV)，立即开始自动化您的文本、内容&监控工作流程！