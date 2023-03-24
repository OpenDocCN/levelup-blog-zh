# 每个开发人员必须知道的 100 个基本系统设计概念(第 8 部分:71–80)

> 原文：<https://levelup.gitconnected.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-8-71-80-c1efc83663>

这些是作为开发人员必须知道的 100 个基本系统设计概念。

这些将帮助您设计高效、容错和可伸缩的系统。

为了保证可读性，我将这些分成多篇博文。

![](img/24a08ddda1fc9bea7c3f73c73489563b.png)

由 [Nirmal Rajendharkumar](https://unsplash.com/@neotronimz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

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

bamania-ashish.medium.com](https://bamania-ashish.medium.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-7-61-70-510cf1bfed63) 

## 71.开销

它是执行特定任务所需的间接资源量(计算时间/内存/带宽)。

## 72.领导人选举

对于执行特定任务的一组计算机节点，它是指定单个节点作为负责该任务的主要协调者/管理者的过程。

多节点系统中的所有节点使用领导者选举共识算法来相互通信并选举单个领导者节点。

![](img/799889b120919645382c8745644e6b01.png)

阿诺·杰格斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

## 73.共识；一致

这是一个多节点系统中的不同节点同意达成共同结论的过程。

分布式系统中的节点使用诸如 [Paxos](https://en.wikipedia.org/wiki/Paxos_(computer_science)) 和 [Raft](https://en.wikipedia.org/wiki/Raft_(algorithm)) 的算法来达成共识。

## 74.对等网络(P2P 网络)

它是一个由计算机节点组成的网络，这些节点通过相互通信来分配任务的工作量并提高效率。

P2P 网络可以在有或没有中央实体的情况下运行。

![](img/5f277c54f5f25e5c69c2db932f85cf78.png)

照片由 [Clarisse Croset](https://unsplash.com/@herfrenchness?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 75.分布式哈希表

许多 P2P 网络使用分发给所有节点的哈希表。

这记录了将任务资源的所有权分配给同级的情况。

然后，其他对等体可以使用这个 DHT 在 P2P 网络上搜索资源。

## 76.八卦协议/流行病协议

它是 P2P 网络用来在组成网络的所有节点之间传播数据的协议。

没有中央实体**并且信息通过将它传递给它的邻居节点而被发送到每个节点。**

这类似于流言如何在办公室传播，或者流行病如何在社区传播。

![](img/288f04ef4472afa0ea4b25e9660c6175.png)

本·怀特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 77.追踪者

它是 P2P 网络中的一个节点，充当**中央实体**来协调网络的运行。

文件共享系统如 Torrent 服务通常使用这些。

[](https://en.wikipedia.org/wiki/BitTorrent_tracker) [## BitTorrent 跟踪器-维基百科

### BitTorrent 跟踪器是一种特殊类型的服务器，有助于使用 BitTorrent 的对等方之间的通信…

en.wikipedia.org](https://en.wikipedia.org/wiki/BitTorrent_tracker) 

## 78.投票

它是以频繁和有规律的间隔从服务器获取数据/检查通信设备是否连接的过程。

轮询用于需要定期从服务器获取数据的应用程序中。例如，介质上的文章的阅读次数的数据。

## 79.流动

它是在两个计算机节点之间建立连续通信链路的过程。

这允许数据从服务器节点流向客户端节点，而无需客户端节点向服务器节点发出常规请求。

每当有状态变化时，服务器节点就将数据“推”到客户机节点。

聊天应用程序通常使用流式传输。

![](img/963cf6d2a78837e32ea998b9cf2593f3.png)

沃洛季米尔·赫里先科在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

## 80.(电源)插座

它是计算机网络上两个节点之间双向通信链路的端点之一。

每个套接字由一个由[传输协议](/100-essential-systems-design-concepts-that-every-developer-must-know-part-1-1318c2c402ca) (TCP/ UDP)、一个 [IP 地址](/100-essential-systems-design-concepts-that-every-developer-must-know-part-1-1318c2c402ca)和一个[端口号](/100-essential-systems-design-concepts-that-every-developer-must-know-part-1-1318c2c402ca)组成的地址来标识。

![](img/cf438a65e4e336267b53e1176ff8cb0b.png)

照片由 [Neven Krcmarek](https://unsplash.com/@nevenkrcmarek?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

*感谢阅读！下一部分再见！*

[](https://bamania-ashish.medium.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-9-81-90-a2048c9bfb84) [## 每个开发人员必须知道的 100 个基本系统设计概念(第 9 部分:81–90)

### 设计高效、容错和可扩展系统的首选清单

bamania-ashish.medium.com](https://bamania-ashish.medium.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-9-81-90-a2048c9bfb84) 

*如果你是 Python 或编程的新手，可以看看我的新书《没有公牛**t 学习 Python 指南**’***下面:**

[](https://bamaniaashish.gumroad.com/l/python-book) [## 学习 Python 的无牛指南

### 你是一个正在考虑学习编程却不知道从哪里开始的人吗？我有适合你的解决方案…

bamaniaashish.gumroad.com](https://bamaniaashish.gumroad.com/l/python-book) [](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium——Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership)