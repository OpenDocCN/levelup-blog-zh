# WebRTC 简介

> 原文：<https://levelup.gitconnected.com/introduction-to-webrtc-5310fae89085>

## 开源实时通信入门

![](img/6d046d3ac115ff342376b54b0d53560b.png)

克里斯·蒙哥马利在 [Unsplash](https://unsplash.com/s/photos/video-call?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

如果你没听说过，WebRTC 是实时获取和共享用户视频、音频和其他数据的途径。代表**网络实时通信**，它是同类产品中的第一个——使用 JavaScript APIs 获取用户的媒体，创建点对点连接，并在浏览器中打开数据通道。在 2011 年 WebRTC 发布之前，需要几个开发人员和大量 C#来完成这些任务。

现在，将近 10 年过去了，WebRTC 是 web 和移动媒体共享的标准。它由万维网联盟(W3C)和互联网工程任务组(IETF)标准化，在 HTML5 中有本地支持，并且是开源和完全免费的。所有这一切意味着你可以开始使用它，而不需要安装任何插件或软件包。

在这篇文章中，我将向您介绍使 WebRTC 能够完成所有这些工作的 API，并提供一些简单的代码片段来帮助您熟悉数据类型以及如何开始使用它们。

注意:WebRTC 有很多功能，尤其是对等连接和数据通道。我将从头到尾提供一些链接，以了解关于这些工具、它们的上下文以及使用它们所需的一些配置的更多信息。

## 三个 WebRTC APIs

WebRTC 使用三种 JavaScript APIs:

1.  `MediaStream`又名`getUserMedia`——允许开发人员访问用户的媒体流:网络摄像头、麦克风和屏幕(当然是在用户允许的情况下)
2.  `RTCPeerConnection` —支持用户之间通过点对点连接进行音频和视频“通话”
3.  `RTCDataChannel` —使用对等连接实现用户之间任何其他通用数据的实时共享

## 媒体流

媒体流是 WebRTC 视频/音频/屏幕共享功能的核心。开始使用它非常容易。因为浏览器中已经有这个 API 了，所以你只需要调用`getUserMedia`:

如您所见，它在全局`navigator`对象上，作为`mediaDevices`上的一个方法。传入一些参数来获得您想要的特定媒体——这里我们从用户那里获得视频和音频，对视频或音频的大小、帧速率、纵横比等没有任何限制。阅读有关 MDN 上的[用户媒体约束的更多信息](https://developer.mozilla.org/en-US/docs/Web/API/Media_Streams_API/Constraints)。

`getUserMedia`用用户的媒体流返回一个承诺，媒体流是一个如下所示的对象:

在原型中，你可以看到这个流可以访问一系列方便的方法，比如`getAudioTracks`和`getVideoTracks`，它们可以在我们的流上使用，以进一步处理和潜在操纵媒体(关于你可以对音频轨道做什么的想法，请参见 [Web Audio API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API) )。

一旦我们访问了这个媒体流，我们也可以通过 HTML 中的`<video>`标签显示它。无论您如何在代码中保存返回的流，只需在`ref`属性中引用该位置:
`<video ref={usersSavedMediaStream} />`。

了解更多关于在 Flavio Copes 使用 `[MediaStream](https://flaviocopes.com/getusermedia/)` [和](https://flaviocopes.com/getusermedia/) `[getUserMedia](https://flaviocopes.com/getusermedia/)` [的](https://flaviocopes.com/getusermedia/)[。](https://flaviocopes.com/getusermedia/)

## RTCPeerConnection

好了，我们有了用户的媒体，但是现在我们如何分享它呢？输入对等连接。`RTCPeerConnection`表示本地用户计算机和远程对等用户计算机之间的连接，包括连接、维护和关闭连接的方法。您可以通过创建一个新的`RTCPeerConnection`实例并传递您的配置来建立对等连接:

配置可以很多，但唯一绝对必要的是`iceServers`，它将保存一个 URL 对象数组，其中包含在寻找 ICE 候选对象的过程中使用的 STUN 和 TURN 服务器的信息。哇，那是整整一口！在一个非常基本的层面上，ICE 候选是关于对等体之间交换的网络的信息。在进行对等连接时，两台远程计算机就它们之间的最佳连接达成一致。在 Temasys 的 Sherwin Sim 的这篇文章中阅读更多关于[冰候选者和眩晕/变身服务器的信息。](https://temasys.io/webrtc-ice-sorcery/)

现在，虽然 API 将允许您进行这些对等连接，但是您还需要使用某种类型的*信令*来促进通过服务器的对等连接。WebRTC 没有为此提供 API，但是有很多选择。这方面做同行之间的连接我就不赘述了，不过可以推荐一下 [Socket。IO](https://socket.io/) 作为基于 Node.js 的 web socket 选项，您可以在 MDN 上了解更多关于[信令的信息。](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Signaling_and_video_calling)

你可能认为这听起来很复杂——你是对的。自从 WebRTC 最初发布以来，已经开发了许多库来掩盖这种复杂性，并使对等实时连接变得稍微容易一些。Simple-Peer 是一个众所周知的开源库，制造商也有一个很好的信号库，叫做 [Simple-Signal](https://github.com/t-mullen/simple-signal) (它使用了 [Socket。IO](https://socket.io/) )。此外，您可以查看 WebRTC 模块和资源的这个[集合。](https://github.com/openrtc-io/awesome-webrtc)

## rtcdataschannel

WebRTC 中的最后一个 API 允许开发人员在两个对等点之间打开一个通道，通过它我们可以发送和接收其他数据，例如实时聊天的文本，甚至是从一台计算机到另一台计算机的任何文件的文件传输，例如基于 torrent 的文件共享。这与 web sockets 非常相似，通过 [WebSocket API](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API) 引入，包含在 Socket.IO 的特性中。

首先，您需要一个通过`RTCPeerConnection`打开的对等连接(或者一个为您处理这个的库)。一旦建立了连接，就可以在本地对等体上调用`createDataChannel`,在远程对等体上调用`ondatachannel`:

## 结论

这是对通过 WebRTC 可以获得的特性的高度概括。使用`getUserMedia`开始使用用户的媒体相当容易，而在用户之间建立点对点连接和数据通道的复杂性会迅速增加。您将需要使用第三方 API 来处理服务器信令(如 [Socket)。IO](https://socket.io/) ，你可以选择使用另一个库来帮助导航`RTCPeerConnection`和`RTCDataChannel`的复杂性，就像[简单对等](https://github.com/feross/simple-peer)。

虽然这套强大的 API 可能需要一点时间来开始使用，但它是一种优秀的、有良好文档记录的、立即可用的免费方式，可以将实时视频和音频通信、文件共享以及任何其他点对点远程交换引入您的应用程序。

有关 WebRTC 的更深入的教程，请查看以下两个资源:

1.  [教程点的 WebRTC“快速指南”](https://www.tutorialspoint.com/webrtc/webrtc_quick_guide.htm)
2.  [从 HTML5 Rocks 开始使用 WebRTC](https://www.html5rocks.com/en/tutorials/webrtc/basics/)