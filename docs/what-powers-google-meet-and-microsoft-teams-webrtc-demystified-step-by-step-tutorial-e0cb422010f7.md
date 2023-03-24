# Google Meet 和微软团队的动力是什么？WebRTC 揭秘|逐步教程

> 原文：<https://levelup.gitconnected.com/what-powers-google-meet-and-microsoft-teams-webrtc-demystified-step-by-step-tutorial-e0cb422010f7>

![](img/bfcef5c5ce827e4436d3c7a5b34ce8c5.png)

资料来源:webrtc.org

如果你的心脏还在跳动，你需要食物来生存，那么极有可能是这个疯狂的疫情强迫你参加了至少一次视频通话。随着[公司正在调整其基础设施](https://www.businessinsider.com/companies-asking-employees-to-work-from-home-due-to-coronavirus-2020)以适应越来越多的远程工作，Zoom [等产品的日活跃用户在短短 4 个月内增长了 2900%](https://www.businessofapps.com/data/zoom-statistics/#:~:text=Why%20ever%20people%20chose%20the,10%20million%20in%20December%202019.),视频通话显然将成为几乎每个人生活中日益重要的一部分。

![](img/d04f0e57e91be8ee1102f73d50f273c8.png)

猴子视频通话的罕见镜头(来源未经核实)

如果你想尝试建立自己的视频通话应用程序，为了工作，为了娱乐，或者仅仅是因为你没有其他事情可做，那么这篇文章就是为你准备的！

我们将看一看 **WebRTC** ，一个强大的开源框架，允许点对点通信，然后通过[基于 Angular 和 Peer JS](https://ullal-aaron.medium.com/how-to-build-a-video-chat-app-from-scratch-webrtc-demystified-step-by-step-tutorial-p-2e74767c673) ，一个精彩的 WebRTC JavaScript 库，构建一个简单的视频通话应用程序来看它的运行。

# **药丸中的 WebRTC**

通常在尝试任何新技术之前，我会试着对它的实际工作原理有一个基本的了解。如果你没有这种冲动，只是想看到东西工作，就[看下一篇文章](https://ullal-aaron.medium.com/how-to-build-a-video-chat-app-from-scratch-webrtc-demystified-step-by-step-tutorial-p-2e74767c673)——没有人会评判你。

简单地说，WebRTC 允许浏览器实时发送数据(如音频和视频)。它可以从网络摄像头和麦克风获取音频/视频流，并点对点传输这些流，而无需下载外部插件或软件。让我们来分析一下魔法是如何发生的。

## WebRTC —主要组件

WebRTC 包括以下 3 个主要组件:

*   **MediaStream** :该 API 允许访问网络摄像头、麦克风和屏幕。它控制流的消费位置，并能够控制产生音频/视频流的设备。
*   **RTCPeerConnection** :这个组件是 WebRTC 的核心，允许参与者(对等体)直接连接(或多或少)，不需要中介。每个对等体传输其流(通过 MediaStream API 获得),创建其他对等体可以订阅的音频/视频源。这个 API 处理音频/视频编解码器、NAT 穿越、丢包管理、带宽管理、数据传输等等。
*   **RTCDataChannel** :这个 API 被设计用来实现双向数据传输。它受 WebSocket 的启发，但使用 UDP 而不是 TCP，以减少 TCP 连接的拥塞和开销。

# WebRTC —数据交换流程

使用 YouTube 等网络服务器发送和接收音频/视频流很容易。鲍勃的计算机向一个 DNS 服务器询问 YouTube.com 的地址，然后砰，他得到了地址。鲍勃的浏览器向他收到的地址发出请求，当他这样做时，YouTube 知道如何向他发送他请求的[小鲨鱼视频](https://www.youtube.com/watch?v=XqZsoesa55w)。

但如果鲍勃在与爱丽丝激烈争吵后，想与他真正的朋友加雷思建立点对点的联系，告诉他爱丽丝和伊芙一直在背后说他的坏话，而现在他不想再与他们俩有任何关系，那该怎么办呢？他可以试着问同一个 DNS 服务器如何联系到 Gareth，但是如果他这么做了，DNS 服务器会回复说从来没有听说过这个人。那么一个 WebRTC 调用是如何建立的呢？让我们找出答案🚀

## 第 1 部分:SDP

为了与 Gareth 取得联系，我们的朋友 Bob 需要做的第一件事是生成一个 **SDP** ( [会话描述协议](https://www.3cx.com/pbx/sdp/))提议。该提议包含关于 Bob 想要开始的会话的信息:他能够理解什么编解码器，他想要传输什么类型的媒体(音频/视频/通用数据)等等。更重要的是，SDP 报价还应该包含 Bob 准备接收传入媒体流的 IP 地址和端口列表，Gareth 将使用这些 IP 地址和端口与他进行通信。Bob 是如何生成这个列表的？有请冰！

## 第二部分:冰(冰宝宝)

为了指导加雷斯如何与他联系，鲍勃经历了一个被称为*冰候选人聚会*的过程。

![](img/e60589b98fd327dca535b74ff89c6516.png)

互联网连接的建立非常重要

**ICE** (互联网连接建立)是 NAT 穿越的标准方法，通过执行连接检查来处理通过 NAT 连接媒体的过程。ICE 使用 **STUN** 或 **TURN** 服务器收集候选人。让我们快速看一下这些奇特的缩写代表什么:

1.  **STUN**([NAT](https://en.wikipedia.org/wiki/STUN)的会话遍历实用程序)服务器:允许 Bob 找出他的公共 IP 地址、他背后的 NAT 类型以及哪个互联网端端口通过 NAT 设备与 Bob 机器上的特定本地端口相关联。
2.  **TURN** ( [使用中继绕过 NAT](https://en.wikipedia.org/wiki/Traversal_Using_Relays_around_NAT) )服务器:如果由于某种原因，STUN 服务器无法与对等方建立连接，则向 TURN 服务器发出请求，该服务器将充当媒体中继。TURN 服务器将提供其公共 IP 地址和端口，该地址和端口将转发从双方接收到的数据包。该中继地址被添加到 ICE 候选列表中。当然，在这种情况下，呼叫不是对等的，因为 Bob 和 Gareth 之间交换的所有数据都将流经 TURN 服务器

## 第 3 部分:信号

现在，每个客户都收集了一些要发送的媒体并创建了一个要约，它如何到达另一个对等点呢？这就是 [**信令**](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Signaling_and_video_calling) 发生的地方:与 Gareth(或者更一般地说，另一个对等体)建立网络会话连接的发现和协商过程。WebRTC 不强制执行特定的信令协议，所以您可以使用几乎任何您喜欢的东西:SIP、Web Sockets、XMPP、信鸽，甚至是烟雾信号，如果您喜欢的话(延迟不是问题)。无论您最终选择什么，主要思想是每个对等体联系一个信令服务器，该服务器作为中介来交换必要的信息:

1.  **网络数据**:对等体在互联网上的位置，以便它们可以找到彼此
2.  **会话控制信息数据**:何时以及如何打开、关闭、修改会话
3.  **媒体数据:**双方都理解哪些音频/视频编解码器？

# WebRTC——JavaScript API

现在我们已经全面了解了幕后发生的事情，让我们看看需要哪些步骤和实际 API 的哪些部分来实现 Bob 和 Gareth 之间的最小通话。

1.  Bob 的浏览器要求使用`**navigator.mediaDevices.getUserMedia()**` **访问本地网络摄像头/麦克风。**视频分辨率、回声消除等约束条件可传递给`**getUserMedia()**`方法。
2.  Bob 创建了一个新的`**RTCPeerConnection**`实例，并将之前授权的媒体轨道添加到将被传输到其他对等体的轨道集合中。这是通过调用`**RTCPeerConnection.addTrack()**` 方法来完成的。`**RTCPeerConnection**`构造函数接受一个`**RTCConfiguration**`参数，在这里您可以指定各种设置，比如 STUN 服务器 URL、ICE 服务器、证书等等。
3.  鲍勃通过调用`**RTCPeerConnection.createOffer()**` 方法来创建 SDP 报价
4.  Bob 将新创建的要约设置为本地描述(`**RTCPeerConnection.setLocalDescription()**`)，并通过信令信道将其发送给接收者。
5.  加雷斯等待着一个新的报价。他一收到它就创建自己的实例`**RTCPeerConnection**`，然后立即通过`**RTCPeerConnection.setRemoteDescription()**`方法将收到的提议设置为远程描述。
6.  Gareth 重复步骤 1 和 2 来捕获本地媒体并将其附加到对等连接。
7.  Gareth 通过调用`**RTCPeerConnection.createOffer()**`方法创建 SDP 答案。
8.  Gareth 重复步骤 4，将答案设置为本地描述，并通过信令信道发送给 Bob。
9.  最后，Bob 收到了 Gareth 的回答，并将其设置为他的远程描述
10.  万岁！鲍勃和加雷斯现在可以交换媒体🎉🥳

# **结论**

本文提供了对 WebRTC is 和实现这一奇迹的宏组件的高级概述。想看看实际情况吗？

阅读本系列的下一篇文章，我们将使用 Angular 和 PeerJS(一个基于 WebRTC 构建的简洁的包装器库)构建一个简单的视频通话应用

*如果你喜欢这篇文章，请随时和你奶奶谈论它，给我发几个比特币，做一个后空翻，或者在下面的部分留下评论:)*

*ETH:0 xe 00 ef 8 a6 d 7 c 43 BD 164d 41332d 5 e 577 be 9 BD 830 b 6*