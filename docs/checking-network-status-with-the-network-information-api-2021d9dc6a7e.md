# 使用网络信息 API 检查网络状态

> 原文：<https://levelup.gitconnected.com/checking-network-status-with-the-network-information-api-2021d9dc6a7e>

![](img/1653090713df243e376d3e527d3290e7.png)

照片由[乔丹·哈里森](https://unsplash.com/@aligns?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

随着手机和平板电脑等移动设备的出现，了解连接状态非常重要，因为它可以随时改变，影响用户在此过程中的体验。我们还必须了解不同种类的互联网连接，因为它们的速度相差很大。

幸运的是，我们在浏览器中内置了网络信息 API 来检查互联网连接状态。

该 API 可用于浏览器和工作环境。

在本文中，我们将研究如何使用 API 来获取网络连接类型的变化和连接状态。

# 检测连接更改

检测连接变化很简单。我们可以使用`navigation.connection`对象来监听网络类型的变化，如下所示:

```
const connection = navigator.connection || navigator.mozConnection || navigator.webkitConnection;
let type = connection.effectiveType;const updateConnectionStatus = () => {
  console.log(`Connection type changed from ${type} to ${connection.effectiveType}`);
  type = connection.effectiveType;
}connection.addEventListener('change', updateConnectionStatus);
```

然后，我们可以通过按 F12 进入 Chrome 开发者控制台来测试连接类型的变化。然后转到网络选项卡，然后在右上角，有一个下拉菜单来更改连接类型。

如果我们在它们之间切换，我们应该看到类似这样的内容:

```
Connection type changed from 4g to 3g
Connection type changed from 3g to 4g
Connection type changed from 4g to 2g
```

从`console.log`输出。

`connection`对象是一个`NetworkInformation`对象实例，它具有以下属性:

*   `downlink` —有效带宽估计值，以每秒兆位为单位，四舍五入到最接近的 25 kbps。
*   `downlinkMax` —底层连接技术的最大下行速度，单位为 Mbps
*   `effectiveType` —由最近观察到的往返时间和下行链路值的组合确定的连接类型。它可以是“慢速 2g”、“2g”、“3g”或“4g”之一。
*   `rtt` —当前连接的估计有效往返时间，四舍五入到 25 毫秒的最接近倍数。
*   `saveData` —布尔值，表示是否设置了减少数据使用可选
*   `type` —与网络通信的连接类型。可以是`bluetooth`、`cellular`、`ethernet`、`none`、`wifi`、`wimax`、`other`、`unknown`中的一种

![](img/6474014ebcd00aa761c4c2693ec4417c.png)

由 [Krzysztof Kowalik](https://unsplash.com/@kowalikus?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 和睦相处

这个 API 是新的和实验性的，所以我们在使用它的时候要小心。自 Chrome 61 以来，Chrome 支持大多数开箱即用的属性。有些选项像`downlinkMax`和`type`只有 Chrome OS 才有。Firefox 和 Edge 不支持这个 API。

它也可用于其他基于 Chromium 的浏览器，如 Opera、Android Webview 和 Chrome for Android。

通过网络信息 API，我们可以获得网络连接的信息。这有助于检测移动设备的连接类型，并让我们相应地更改网页以适应较慢的连接。

![](img/fbda831351d3a4da7030d6fdf7c6196f.png)

托马斯·詹森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片