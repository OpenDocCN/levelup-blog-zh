# 忘记 SwiftUI —使用合金！

> 原文：<https://levelup.gitconnected.com/forget-swiftui-use-alloy-857c94f2c40f>

![](img/a28c55573fa2d16126da9c4f8559a44c.png)

昨天苹果公布了 SwiftUI。用 Swift 写 UI 的一个非常简单的方法。此次发布对本地/Swift 开发人员来说是个好消息。然而，对于像我这样的 JavaScript 开发者来说，更好的解决方案是使用 JavaScript 的跨平台应用。

然后就是合金。一个基于 Titanium 的 MVC 框架，它是一个简单的 XML，给你一个非常清晰的 UI 结构概述。或者用迈克尔的话说:

Alloy 是一个非常灵活的平台，它位于最稳定、最本地的跨平台框架之上。想要一个包含 4 个选项卡的选项卡组吗？抓住你了。

```
<Alloy>
  <TabGroup>
    <Tab>
      <Window></Window>
    </Tab>
    ...
  </TabGroup>
</Alloy>
```

想要剥离窗口，使它成为自己的独立控制器，这样你就可以使它更干净？抓住你了。

```
<Tab>
  <Require src="myWindow" />
</Tab
```

想连接一个基于 REST 的 API 来呈现 ListView 中的数据吗？抓住你了。

```
<ListView>
  <ListSection dataCollection="myCollection" />
</ListView>
```

当然，我所有的例子都是简化的，缺少相关的控制器或样式代码，但是你应该明白要点。我认为最好的开源项目是官方的 kitchensink 应用程序。

想开始吗？检查 npm 上的[钛，并安装](https://www.npmjs.com/package/titanium)[合金 CLI](https://www.npmjs.com/package/alloy) 。