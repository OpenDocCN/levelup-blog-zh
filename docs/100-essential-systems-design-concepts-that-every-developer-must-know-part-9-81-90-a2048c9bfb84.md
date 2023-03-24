# 每个开发人员必须知道的 100 个基本系统设计概念(第 9 部分:81–90)

> 原文：<https://levelup.gitconnected.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-9-81-90-a2048c9bfb84>

这些是作为开发人员必须知道的 100 个基本系统设计概念。

这些将帮助您设计高效、容错和可伸缩的系统。

为了保证可读性，我将这些分成多篇博文。

![](img/ea705e15ea067f8aef0b44c59e85dc65.png)

[带着红帽子的女孩](https://unsplash.com/@girlwithredhat?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

可以在下面找到以前部分的链接:

[](/100-essential-systems-design-concepts-that-every-developer-must-know-part-1-1318c2c402ca) [## 每个开发人员都必须知道的 100 个基本系统设计概念(第 1 部分)

### 设计高效、容错和可扩展系统的首选清单

levelup.gitconnected.com](/100-essential-systems-design-concepts-that-every-developer-must-know-part-1-1318c2c402ca) [](/100-essential-systems-design-concepts-that-every-developer-must-know-part-2-b6c4c6239af8) [## 每个开发人员都必须知道的 100 个基本系统设计概念(第 2 部分)

### 设计高效、容错和可扩展系统的首选清单

levelup.gitconnected.com](/100-essential-systems-design-concepts-that-every-developer-must-know-part-2-b6c4c6239af8) [](/100-essential-systems-design-concepts-that-every-developer-must-know-part-3-256762dbed0e) [## 每个开发人员都必须知道的 100 个基本系统设计概念(第 3 部分)

### 设计高效、容错和可扩展系统的首选清单

levelup.gitconnected.com](/100-essential-systems-design-concepts-that-every-developer-must-know-part-3-256762dbed0e) [](https://bamania-ashish.medium.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-4-31-40-733d19958c37) [## 每个开发人员必须知道的 100 个基本系统设计概念(第 4 部分:31–40)

### 设计高效、容错和可扩展系统的首选清单

bamania-ashish.medium.com](https://bamania-ashish.medium.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-4-31-40-733d19958c37) [](/100-essential-systems-design-concepts-that-every-developer-must-know-part-4-41-50-8bfa8c3292c) [## 每个开发人员都必须知道的 100 个基本系统设计概念(第 5 部分:41–50)

### 设计高效、容错和可扩展系统的首选清单

levelup.gitconnected.com](/100-essential-systems-design-concepts-that-every-developer-must-know-part-4-41-50-8bfa8c3292c) [](/100-essential-systems-design-concepts-that-every-developer-must-know-part-6-51-60-978801728a4b) [## 每个开发人员都必须知道的 100 个基本系统设计概念(第 6 部分:51–60)

### 设计高效、容错和可扩展系统的首选清单

levelup.gitconnected.com](/100-essential-systems-design-concepts-that-every-developer-must-know-part-6-51-60-978801728a4b) [](https://bamania-ashish.medium.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-7-61-70-510cf1bfed63) [## 每个开发人员都必须知道的 100 个基本系统设计概念(第 7 部分:61–70)

### 设计高效、容错和可扩展系统的首选清单

bamania-ashish.medium.com](https://bamania-ashish.medium.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-7-61-70-510cf1bfed63) [](/100-essential-systems-design-concepts-that-every-developer-must-know-part-8-71-80-c1efc83663) [## 每个开发人员都必须知道的 100 个基本系统设计概念(第 8 部分:71–80)

### 设计高效、容错和可扩展系统的首选清单

levelup.gitconnected.com](/100-essential-systems-design-concepts-that-every-developer-must-know-part-8-71-80-c1efc83663) 

# 81.速率限制/节流

这是一个控制系统发送和接收请求速率的过程。

对传入请求进行速率限制是为了防止 ***DoS 攻击*** 。

一种流行的速率限制策略是通过跟踪客户端的 *IP 地址。*

![](img/42ad0b7bb723e40f7c4416bce95ef48e.png)

照片由[本工程图](https://unsplash.com/@thisisengineering?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 82.拒绝服务(DoS)攻击

这是一种网络攻击，目标系统被来自恶意参与者的请求淹没。

攻击的目的是使目标机器的容量过饱和，使其无法为其通常的客户服务。

![](img/a0bc71e5f5280343b825983ed059d4fd.png)

[阿吉特](https://unsplash.com/@arget?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 83.分布式拒绝服务(DDoS)攻击

这是一种 DoS 攻击，向系统发出的请求来自多个不同的来源。

阅读一篇关于谷歌防御**僵尸网络 DDoS 攻击**的有趣文章:

[](https://cloud.google.com/blog/products/identity-security/how-google-cloud-blocked-largest-layer-7-ddos-attack-at-46-million-rps) [## 谷歌云如何阻止迄今最大的第 7 层 DDoS 攻击，4600 万 rps |谷歌云博客

### 通过预测 DDOS 攻击，谷歌云客户能够在它关闭他们的网站之前阻止它。他们只是…

cloud.google.com](https://cloud.google.com/blog/products/identity-security/how-google-cloud-blocked-largest-layer-7-ddos-attack-at-46-million-rps) 

# 84.信息传递

它是一种在不直接接触系统的情况下传递指令来调用系统行为的技术。

消息传递可以是:

*   **同步**:同时运行的对象之间
*   **异步**:在接收到指令的同时，可能没有激活/准备好执行命令的对象之间

消息传递可以包括:

*   **本地** **对象**:发送方和接收方在同一个系统上
*   **分布式** **对象**:发送方和接收方在不同的计算机上/运行不同的操作系统或编程语言(例如[远程过程调用](https://en.wikipedia.org/wiki/Remote_procedure_call))

# 85.面向消息的中间件

它是一种允许异步应用程序间通信的架构模式。

消息传递**队列**(允许 FIFO 操作)被用作在消息创建者/发送者和消息接收者之间发送消息(信息块/指令)的中间件。

![](img/9ab808c82e4289512dbdae0b475447af.png)

照片由[最大焦点](https://unsplash.com/@maximalfocus?utm_source=medium&utm_medium=referral)在[防溅板](https://unsplash.com?utm_source=medium&utm_medium=referral)上拍摄

# 86.发布-订阅模式(发布订阅)

它是一种架构模式，包括:

*   出版商
*   订阅者
*   主题/逻辑频道
*   信息

***发布者*** 创建消息并广播给不同的 ***话题*** 。

***订阅者*** 链接到他们感兴趣的 ***话题*** ，从他们那里接收 ***消息*** 。

这是一种**消息传递**的方法，其中服务(发布者和订阅者)可以**异步**相互通信，而无需实现直接通信。

***PubSub*** 通常是更大的面向消息的中间件系统的一部分。

![](img/e58bb6b84c5d348cb6f853afeabbe3e3.png)

约翰·巴克利普在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 87.阿帕奇卡夫卡

它是一个流行的开源分布式事件流平台，允许:

*   **发布和订阅**事件流/消息(PubSub)
*   **事件流的存储**
*   **流**处理**流**

它高度可扩展、高度可用，并允许高吞吐量的高耐用性。

[](https://kafka.apache.org/) [## 阿帕奇卡夫卡

### 超过 80%的财富 100 强公司信任并使用卡夫卡。阿帕奇卡夫卡是一个开源的分布式事件…

kafka.apache.org](https://kafka.apache.org/) 

# 88.分布式计算

它是一种计算模型，其中网络中的不同计算机相互通信，划分任务，并作为一个统一的系统朝着一个共同的目标努力。

![](img/a0c0a7e50f10d20e9d5c51199e2da6f5.png)

由 [Marvin Meyer](https://unsplash.com/es/@marvelous?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 89.MapReduce

这是一个为处理大型分布式数据集而实现的编程模型。

大体上，它包括三个步骤:

*   **映射**:程序员指定一个**映射函数**，它从分布式数据集的不同部分生成一组键/值对
*   **中间步骤(洗牌/排序/分区)**:这个步骤重新组织键值对，以便于 ***减少*** 步骤。
*   **Reduce** :一个 **reduce 函数**合并所有与同一个中间密钥相关联的混洗中间值，以创建所需的最终数据

在 Google 的原始白皮书中阅读有关 MapReduce 的更多信息:

Google 的 MapReduce 白皮书([https://static . Google user content . com/media/research . Google . com/en/us/archive/MapReduce-osdi 04 . pdf](https://static.googleusercontent.com/media/research.google.com/en/us/archive/mapreduce-osdi04.pdf))

另一个很好的资源是由[电脑爱好者](https://www.youtube.com/channel/UC9-y-6csu5WGm29I7JiwpnA)制作的视频。

A **pache Spark** 对于数据处理来说是一个更好的选择，因为它允许节点在内存中而不是在磁盘上处理数据(就像使用 MapReduce 时一样)。

# 90.Apache Hadoop

它是分布式计算中用于存储和处理大量数据(大数据)的软件实用程序的集合。

[](https://hadoop.apache.org/) [## Apache Hadoop

### 这是 Apache Hadoop 3.3 系列的一个版本。它包含少量安全和关键集成修复，因为…

hadoop.apache.org](https://hadoop.apache.org/) 

它包括:

*   存储部分，即 **Hadoop 分布式文件系统(HDFS)**
*   一个处理部分，即 **MapReduce 编程模型**

![](img/05afe419925ae26085830038e42c9487.png)

Apache Hadoop 徽标(图片来自维基百科)

非常感谢您阅读这篇文章！下一部分再见！

[](https://bamania-ashish.medium.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-10-91-100-e947abc837ce) [## 每个开发人员都必须知道的 100 个基本系统设计概念(第 10 部分:91–100)

### 设计高效、容错和可扩展系统的首选清单

bamania-ashish.medium.com](https://bamania-ashish.medium.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-10-91-100-e947abc837ce) 

*如果你是 Python 或编程的新手，可以看看我的新书，书名为“* [**”《没有公牛**t 学习 Python 指南**](https://bamaniaashish.gumroad.com/l/python-book)**”***下面:*

[](https://bamaniaashish.gumroad.com/l/python-book) [## 学习 Python 的无牛指南

### 你是一个正在考虑学习编程却不知道从哪里开始的人吗？我有适合你的解决方案…

bamaniaashish.gumroad.com](https://bamaniaashish.gumroad.com/l/python-book) [](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium——Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership)