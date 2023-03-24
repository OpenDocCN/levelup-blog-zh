# 推出 SwiftNEWKit —毫不费力地展示“新功能”

> 原文：<https://levelup.gitconnected.com/introducing-swiftnewkit-show-whats-new-effortless-53850fefa8a5>

## 专为使用 SwiftUI 的苹果开发者设计。

![](img/9bc8f57c6f0f57f254ab3e47f43c505d.png)

> 显示“最新动态？”使用#SwiftNEWKit 比以往任何时候都更容易

SwiftNEWKit 是一个新的开源 Swift 包，为苹果开发者向最终用户展示发行说明提供了一个简单的方法。它可以实现:

*   自动触发`.sheet`从版本和/或构建增加。
*   单行编码
*   JSON 兼容
*   版本控制(2.0 或更高版本)
*   本地可用
*   简单装订
*   简单模型
*   远程加载(3.0 或更高版本)
*   支持远程掉线通知(3.5.0 或以上)

# 环境

![](img/a51df55181f186fb7b18fd58e6d27487.png)![](img/7df617242a947f55ff6f52b56e2ea8b6.png)

# 设置

1.  导航到根项目
2.  选择项目

![](img/859a1f928a2493ba2c0e40943e6d8779.png)![](img/2eef9ec76bbab7fb9db81ecfb748b00b.png)

3.选择包依赖关系

![](img/6e80e3241756a82884c86f9f072a943d.png)

4.点击+并将`[https://github.com/1998code/SwiftNEWKit](https://github.com/1998code/SwiftNEWKit)`粘贴到搜索框中

![](img/93955581eba1c364ef315914f8e101bd.png)

5L。如果你想在设备上加载数据，创建一个名为`**data.json**` Sample 的新文件:

[](https://github.com/1998code/SwiftNEWKit/blob/main/Demo/What%27s%20New%3F/data.json) [## swift newkit/data . JSON at main 1998 code/swift newkit

github.com](https://github.com/1998code/SwiftNEWKit/blob/main/Demo/What%27s%20New%3F/data.json) 

5R。如果您想要远程加载数据，请使用`https://…/{}.json`来提供数据。

# 使用

1.  用`import SwiftNEW`导入
2.  在变量`body`或任何`view`前添加状态:

![](img/75e7e9fab5fc8cd4c6e9b49a26549ba3.png)

3.然后，将这段代码添加到主体或任何视图中。

```
SwiftNEW(show: $showNew, align: $align, color: $color, size: $size, labelColor: $labelColor, label: $label, labelImage: $labelImage, history: $history, data: $data)
```

4.完成后，您应该会看到如下所示的视图:

![](img/6455af594b72846941fa095534eb96ba.png)![](img/c22ec7287f8c749663e5574df294afa4.png)

要修改内容，您可以编辑`**data.json**` 文件。

![](img/32a9ca1e87f40fdc0d228d10d34dc13e.png)

如果您希望修改布局，只需参考状态和选项。

![](img/180b36e15f41483c46761224829c3532.png)

要了解更多信息，请访问 GitHub repo:

[](https://github.com/1998code/SwiftNEWKit) [## GitHub - 1998code/SwiftNEWKit:用 SwiftUI 显示“新功能”。

### 为苹果开发者向最终用户展示“新内容”提供了一种简单的方式。步骤描述截图 1 导航…

github.com](https://github.com/1998code/SwiftNEWKit) 

## 感谢阅读，享受 Swift 编码: )👏

## 今天在 Twitter 上关注我:

[](https://twitter.com/1998design) [## 明@ 1998 设计

twitter.com](https://twitter.com/1998design)