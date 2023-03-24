# 每个开发人员必须知道的 100 个基本系统设计概念(第 3 部分:21–30)

> 原文：<https://levelup.gitconnected.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-3-256762dbed0e>

![](img/ebc62ba68e2aa543881ea5363d3b8045.png)

照片由[UX](https://unsplash.com/es/@uxindo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这些是作为开发人员必须知道的 100 个基本系统设计概念。

这些将帮助您设计高效、容错和可伸缩的系统。

*为了保证可读性，我将这些分成多篇博文。*

*如果你是 Python 或编程的新手，可以看看我的新书，书名是'* [**【没有公牛**t 学习 Python 指南**](https://bamaniaashish.gumroad.com/l/python-book)**'***:*

[](https://bamaniaashish.gumroad.com/l/python-book) [## 学习 Python 的无牛指南

### 你是一个正在考虑学习编程却不知道从哪里开始的人吗？我有适合你的解决方案…

bamaniaashish.gumroad.com](https://bamaniaashish.gumroad.com/l/python-book) 

可以在下面找到以前部分的链接:

[](/100-essential-systems-design-concepts-that-every-developer-must-know-part-1-1318c2c402ca) [## 每个开发人员都必须知道的 100 个基本系统设计概念(第 1 部分)

### 设计高效、容错和可扩展系统的首选清单

levelup.gitconnected.com](/100-essential-systems-design-concepts-that-every-developer-must-know-part-1-1318c2c402ca) [](/100-essential-systems-design-concepts-that-every-developer-must-know-part-2-b6c4c6239af8) [## 每个开发人员都必须知道的 100 个基本系统设计概念(第 2 部分)

### 设计高效、容错和可扩展系统的首选清单

levelup.gitconnected.com](/100-essential-systems-design-concepts-that-every-developer-must-know-part-2-b6c4c6239af8) 

## 21.潜伏

这是在一个系统中完成一项操作所需的时间/在一个系统中从原因到结果之间的时间。

例如，你点击“登录”按钮和被重定向到你的脸书主页之间的时间间隔，表明了脸书服务器的延迟(粗略)。

## 22.吞吐量

它是每单位时间(比特/秒或数据包/秒)内可以通过网络发送的数据量。

![](img/aa5eb560175cdb65aa9ea57773478947.png)

照片由[乔丹·哈里森](https://unsplash.com/@jordanharrison?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 23.**带宽**

它是在特定时间段内可以通过网络发送的最大数据量。

## 24.一致性

在分布式系统中，它指的是通过在不同的节点上读取、写入或更新数据来获得可预测的结果。

![](img/ba343a27bbb5abb2b6c6a50354db3290.png)

[Kelly Sikkema](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 25.有效性

它是系统运行并完成预期任务/正常运行时间的概率。

## 26.停工期

它是系统不可用的时间段。

## 27.九分可用性

可用性以九(9)来衡量。

可用性为 90%的系统(“1/9”)每年将有 36.53 天的停机时间。

可用性为 99%的系统(“两个 9”)每年将有 3.65 天的停机时间。

**高可用性**系统被定义为可用性达到 99.999%(“五个九”)或更高，每年停机时间为 5.26 分钟的系统。

![](img/c1bbdb4e15ec3913c17ccfb91d1016f4.png)

由 [Vladimir Malyutin](https://unsplash.com/@vladimir__film?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 28.可靠性

它是系统在给定时间内正确执行预期功能的概率。

## 29.裁员

它是复制系统的关键组件(例如，web 应用程序的数据库)以增加其可靠性的过程。

它消除了系统中的单点故障。

## 30.服务水平协议

这是服务提供商和客户之间就服务水平目标/ SLO(性能指标)达成的服务协议。

例如，当我们在 AWS S3 上存储图像数据时，我们和亚马逊之间的 SLA 具有 SLOs 的高可用性和安全性。

![](img/21f71f174ba18ea674e0f81f698520a7.png)

照片由 [Romain Dancre](https://unsplash.com/@romaindancre?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

*感谢阅读！下一部分再见！*

[](https://bamania-ashish.medium.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-4-31-40-733d19958c37) [## 每个开发人员必须知道的 100 个基本系统设计概念(第 4 部分:31–40)

### 设计高效、容错和可扩展系统的首选清单

bamania-ashish.medium.com](https://bamania-ashish.medium.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-4-31-40-733d19958c37) 

*如果你是 Python 或编程的新手，可以看看我的新书，书名是'* [**【没有公牛**t 学习 Python 指南**](https://bamaniaashish.gumroad.com/l/python-book) **'** *下面:*

[](https://bamaniaashish.gumroad.com/l/python-book) [## 学习 Python 的无牛指南

### 你是一个正在考虑学习编程却不知道从哪里开始的人吗？我有适合你的解决方案…

bamaniaashish.gumroad.com](https://bamaniaashish.gumroad.com/l/python-book) [](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium——Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership)