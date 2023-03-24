# 使用 Infobip 快速启动 WebRTC

> 原文：<https://levelup.gitconnected.com/quick-start-webrtc-with-infobip-35675c8fae38>

Web 实时通信(WebRTC)已经迅速成为事实上的框架，用于构建应用程序以促进网络上的音频和视频通信。WebRTC 通过浏览器或移动设备将用户联系起来。在本帖中，我们将让您了解为什么 WebRTC 在当今的开发中被如此广泛地采用。在这个过程中，我们将介绍如何利用 Infobip 的 SDK 集来开始使用 WebRTC。

让我们开始更深入地了解 WebRTC 的细节和历史。

# 什么是 WebRTC？

在高层次上，WebRTC 是一种对等(P2P)数据传输技术。虽然它有多种用途，但它最常用于延迟敏感的应用程序，这使得它非常适合 P2P 视频和音频流。许多主要的技术公司——包括 Zoom、Slack 和 Discord——在他们的实现中使用 WebRTC 来为他们的用户提供安全和无缝的流媒体体验。

# WebRTC 已经存在多久了？

大多数人认为，2013 年 2 月，业界首次看到 WebRTC 用于跨浏览器视频通话。这意味着 WebRTC 已经存在将近 10 年了！对于那些注重文书工作的人来说，WebRTC 在 2017 年获得了 W3C 的官方“[候选人推荐](https://www.w3.org/TR/2017/CR-webrtc-20171102/)”地位，并在 2021 年 1 月获得了“[推荐](https://www.w3.org/2021/01/pressrelease-webrtc-rec.html.en)地位。从这个角度来看，WebRTC 作为官方支持技术的地位是相当新的。然而，在管理技术团体和行业使用的支持下，WebRTC 的采用率正在上升。对于寻求构建音频和视频应用的公司来说尤其如此。

# 为什么选择 WebRTC 而不是其他选项？

为什么一家公司会选择在 WebRTC 之上构建，而不是像 RTMP 这样的另一个选择呢？有几个原因，我们将在这里强调最重要的几个:

## 没有中央服务器

因为 WebRTC 是 P2P，所以客户端管理连接。换句话说，在处理媒体和静态资产时，不存在具有代理连接的中央基础设施的客户端-服务器架构。最终用户设备完成所有的连接管理。虽然这给客户端带来了更多的资源负载，但是 P2P 方法更加安全和有弹性。

值得一提的一个细微差别是，WebRTC 需要一种将客户机彼此连接起来的方法；这个过程被称为“信号传递”因此，虽然没有连接管理意义上的中央服务器，但您确实需要一个信令服务器来帮助客户端知道彼此的存在并协商它们的连接。尽管如此，这个信令服务器所需的资源远远少于维护整个客户机-服务器体系结构所需的资源。

## W3C/IETF 标准

从历史上看，开发人员可能并不太相信 W3C 或 IETF 对某项技术的认可。尽管如此，WebRTC 得到这些机构的支持意味着大量的思想、设计和讨论进入了这项技术。WebRTC 已经发展了十多年，它的官方推荐得到了一些积极使用它的大型科技公司的支持。你和 WebRTC 是好伙伴。

## 高质量和高速度

与基于 TCP 的流替代方案(例如，MPEG-DASH 或 RTMP)不同，WebRTC 使用 UDP 包广播。使用 UDP 减少了数据包级重新加密和重新传输的需要。对于视频和音频流应用程序，UDP 带来的轻微数据包丢失对最终用户体验的负面影响可以忽略不计。然而，最终的结果是，WebRTC *变得更快*。这是仅有的能够持续实现亚秒级延迟的协议之一，它开箱即用！

## 本机功能

作为 HTML5 支持的官方认可的技术，WebRTC 非常容易上手。你不需要插件。您只需要一个兼容 WebRTC 的浏览器，就可以立即访问设备检测、媒体捕获、数据传输等等。当您在 WebRTC 上构建时，应用程序中的一切都是本机的；您的用户不需要下载或安装任何其他东西。

# Infobip 能有什么帮助？

为了与 WebRTC 合作，Infobip 提供了针对 [Android](https://github.com/infobip/infobip-rtc-android) 、 [iOS](https://github.com/infobip/infobip-rtc-ios) 、 [React Native](https://github.com/infobip/infobip-rtc-react-native) 和 [JavaScript](https://github.com/infobip/infobip-rtc-js) 的 SDK。这几乎涵盖了 WebRTC 的所有应用程序。Infobip 让入门变得非常容易。

# 正在设置

假设您正在开始一个新的基于 JavaScript 的项目。入门非常简单，只需安装带有 npm 的 Infobip JavaScript SDK，或者在项目中包含发行版文件:

```
$ npm install infobip-rtc --save# main.js 
let InfobipRTC = require('infobip-rtc');# or as an ES6 Import
import {InfobipRTC} from "infobip-rtc";
```

Infobip 还提供了一个分发文件，您可以包含:

```
<script src="//rtc.cdn.infobip.com/latest/infobip.rtc.js"></script>
```

# 将它投入使用

Infobip 采取了一种不干涉用户管理的方法。你的用户是你的用户。要为您的用户启用 Infobip 的服务，您只需要为他们创建一个令牌。下面是令牌请求的一个示例，以及一个响应:

```
# main.js
var settings = {
    "url": "https://{baseUrl}/webrtc/1/token",
    "method": "POST",
    "timeout": 0,
    "headers": {
        "Authorization": "{authorization}",
        "Content-Type": "application/json",
        "Accept": "application/json"
    },
    "data": JSON.stringify({"identity":"Alice","applicationId":"2277594c-76ea-4b8e-a299-e2b6db41b9dc","displayName":"Alice in Wonderland","capabilities":{"recording":"ALWAYS"},"timeToLive":43200}),
};$.ajax(settings).done(function (response) {
    console.log(response);
});# Response
{
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpZGVudGl0eSI6IkFsaWNlIiwibmFtZSI6IkFsaWNlIGluIFdvbmRlcmxhbmQiLCJleHAiOjE1NzkyOTA2MzgsImNhcHMiOlsyXX0.QyCMqjH8DsftChibW2Rw4EByH-eEviUp3-kHVKuJpKg",
  "expirationTime": "2020-01-17T19:50:38.488589Z"
}
```

一旦创建并存储了令牌，就可以开始了。

虽然应用程序的用例会有很大的不同，但是每个 SDK 的 Github 库都有很棒的入门资源。Infobip 提供的 WebRTC 的官方文档可以在这里找到。

# 包装 WebRTC

我们已经讨论了很多关于 WebRTC 的内容，所以让我们花点时间来回顾一下。WebRTC 是:

*   一个稳定的、受行业支持的框架，主要用于浏览器和移动设备之间的 P2P 音频和视频通信。
*   被许多科技公司广泛采用，用于各种不同的使用案例。
*   优于基于 TCP 的替代方案，因为其基于 UDP 的方法在保持质量的同时提供了令人难以置信的速度。

并查看 [Infobip 的平台](https://www.infobip.com/signup)——一个免费且快速的 WebRTC 入门方式。