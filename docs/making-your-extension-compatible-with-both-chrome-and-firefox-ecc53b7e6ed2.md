# 让你的扩展兼容 Chrome 和 Firefox

> 原文：<https://levelup.gitconnected.com/making-your-extension-compatible-with-both-chrome-and-firefox-ecc53b7e6ed2>

![](img/ac25856dc388cf20c2093bcd15409c7f.png)

*本文原载于* [*我的个人博客*](https://blog.shahednasser.com/making-your-extension-compatible-with-both-chrome-and-firefox/) *。*

为不同的浏览器开发扩展大部分都是相似的，但是，还是有一些不同之处需要注意。

本文列出了开发 Chrome 扩展和 Firefox 附加组件之间的区别，帮助您理解如何确保您的扩展兼容这两种浏览器。最后，我还将介绍在不同平台上打包和发布扩展时的区别。

# 显示

以下是`manifest.json`文件的主要区别:

1.  Firefox 在清单中有一个`developer`键，它是一个包含`name`和`url`的对象。Chrome 没有。
2.  如果你正在使用存储 API，并且想通过从你的机器上加载来测试你的扩展，Firefox 需要`browser_specific_settings`键才能工作，否则存储 API 将无法工作。一个例子是:

```
"browser_specific_settings": {
  "gecko": {
    "id": "addon@example.com",
    "strict_min_version": "42.0"
  }
}
```

# 清单 V3

目前，Chrome 正在推动使用 Manifest V3，这有几个原因引起了争议。至于 Firefox，在 2019 年的一篇博文中，Mozilla 表示他们也将支持 Manifest V3，然而，他们没有义务实现它的每个部分。Mozilla 打算保留 Chrome 在 V3 中放弃的许多功能和 API。

*建议阅读:* [*了解如何将你的 Chrome 扩展从清单 V2 迁移到 V3*](https://blog.shahednasser.com/chrome-extension-tutorial-migrating-to-manifest-v3-from-v2/) *！*

# 应用程序接口

在 Chrome 中，API 命名空间是`chrome.*`，而在 Firefox 中是`browser.*`。Firefox 声称支持`chrome.*`，但更倾向于使用`browser.*`。

然而，两者之间的主要区别在于`chrome.*`在处理异步事件时只支持回调，而`browser.*`支持回调和承诺。

下面是一个如何在 Chrome 中查询标签的例子:

```
chrome.tabs.query({active: true}, function (tabs) {
    console.log(tabs[0].title);
});
```

下面是 Firefox 中使用承诺时的相同示例:

```
browser.tabs.query({active: true})
    .then ((tabs) => console.log(tabs[0].title))
    .catch ((err) => console.error(err))
```

然而，Mozilla 提供了一个 polyfill，允许你在所有的浏览器扩展中使用承诺。你可以点击查看[。](https://github.com/mozilla/webextension-polyfill/)

# 功能差异

对于每种浏览器，一些函数具有不同的签名或行为:

1.  Chrome 在他们的`chrome.notifications` [API 文档](https://developer.chrome.com/docs/extensions/reference/notifications/#type-NotificationOptions)中声明，对于`chrome.notifications.create`来说，参数`iconUrl`是必需的，而对于 Firefox [来说，参数](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/notifications/NotificationOptions)是可选的。
2.  对于函数`insertCSS`和`executeScript`中的`tabs` API，Firefox 解析相对于当前页面传递的 URL，而 Chrome 解析相对于扩展根目录传递的 URL。要解决这个问题，使用`chrome.runtime.getURL`(或者对于 Firefox，用`browser`替换`chrome`)来获取扩展名中文件的全限定 URL。
3.  没有`manifest.json`中的选项卡权限`tabs.query`在 Firefox 中是不允许的，但是如果选项卡与`manifest.json`中的主机权限匹配，那么在 Chrome 中是允许的。
4.  Chrome 拥有的`declarativeContent` API 还没有在 Firefox 中实现。

# 一些额外的差异

1.  Firefox 中 CSS 文件中的 URL 相对于 CSS 文件进行解析，而 Chrome 中它们相对于当前页面进行解析。
2.  Firefox 不允许在后台脚本中使用`alert`、`confirm`或`prompt`这样的函数。
3.  Chrome 允许在发出请求时传递相对 URL(例如，`/user`)，然而 Firefox 要求绝对 URL。

# 打包和发布扩展体验

当打包扩展以发布它时，在 Chrome 中`manifest.json`文件应该在包的根目录中。而在 Firefox 中，扩展应该封装在一个目录中，该目录的根包含`manifest.json`。

下面是一个 Chrome 扩展包结构的例子:

```
root
|
|
|_ _ _ manifest.json
```

下面是它在 Firefox 扩展包中的样子:

```
root
|
|
|_ _ _ my-extension
       |
       |
       |_ _ _ manifest.json
```

当要发布你的扩展时，Google 要求一次性支付 25 美元(在撰写本文时)来创建一个开发者账户。一旦你这样做，你不需要作出任何额外的付款时，增加更多的扩展。有了 Firefox，你不需要支付任何费用来发布一个扩展。

一旦你在两个平台上都有了开发者账号，你就可以上传你的扩展了。

在 Chrome 上上传你的扩展时，你会被要求输入大量信息，包括扩展的名称、描述、各种不同大小的图像，以及用户在下载你的扩展时会看到的其他信息。根据您在`manifest.json`中要求的权限，您还需要输入一些关于隐私和处理用户数据的详细信息。你也可以输入谷歌分析代码，帮助你更彻底地跟踪你的扩展和它的用户。完成后，在你的扩展发布到 Chrome 网上商店之前，审核过程可能需要一些时间。

在 Firefox 上上传扩展时，首先会要求您输入一些关于远程代码执行、隐私和其他安全信息的信息。然后，你将输入与 Chrome 几乎相同的信息，如名称、描述等。然而，Firefox 需要的图片较少，对大小的要求也没有 Chrome 严格。Firefox 不允许添加谷歌分析跟踪代码来跟踪你的扩展。完成后，您的扩展将立即发布。

至于更新你的扩展，对于 Chrome 来说，你只需要上传最新的包就可以了，如果权限没有变化的话，其实不需要输入任何其他信息。如果您的权限有任何更改，您可能需要填写更多隐私和安全相关的信息。完成后，我们将审核您的扩展，如果获得批准，将会发布。

对于 Firefox，在更新时，您需要输入与之前相同的关于安全性和远程执行的信息。您还将被要求添加变更日志信息，以便您的用户知道发生了什么变化。完成后，您的扩展将立即发布。

*原载于 2021 年 3 月 23 日【https://blog.shahednasser.com】[](https://blog.shahednasser.com/making-your-extension-compatible-with-both-chrome-and-firefox/)**。***