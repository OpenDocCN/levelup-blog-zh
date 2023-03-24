# 如何为 Web 应用程序使用联系人选择器 API

> 原文：<https://levelup.gitconnected.com/how-to-use-contacts-list-in-your-web-application-f002fc2c0e6c>

## 使用网络上的联系人

![](img/e420b5d0edb0e975d19beb7b77e6fe0f.png)

照片由[克里斯蒂安·威迪格](https://unsplash.com/@christianw?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

你好，中号！在本文中，我想解释并展示一个 Web 联系人选择器 API 的例子。

***开始前注意事项*** *:本文不需要具体知识，比较基础的 Javascript。*

# 这是什么？

选择联系人选取器模型是为了让用户完全控制共享数据，允许用户准确选择向网站提供哪些联系人。该功能使网站可以一次性访问用户的联系人，这意味着开发者必须在每次需要时请求访问用户的联系人。

**使用案例:**

1.  列出事件参与者(姓名和号码)
2.  在电子邮件应用程序中选择邮件的收件人。
3.  指定发送包的物理地址

# 代码示例

在开始使用浏览器 API 之前，您应该始终检查您的设备和浏览器的组合是否支持它。根据规范，浏览器中的联系人选取器 API 呈现为`navigator.contacts`。以下是检查联系人选取器支持的代码示例:

检查支持后，我们可以开始与联系人合作。我们需要`navigator.contacts.select()`——该方法允许您获取用户的联系人列表，并为每个联系人选择特定的数据，以便在您的 web 应用程序中使用。`select()`打开联系人选择器，允许用户选择他们想要与站点共享的联系人。

`navigator.contacts.select()`接受两个输入参数:

1.  必需的参数。您希望返回的每个联系人的属性数组(允许的值为`'name'`、`'email'`、`'tel'`、`'address'`或`'icon'`中的任意一个)。除了`icon`，所有的值都返回一个字符串数组。该属性返回一个 Blob 数组( [image mime type](https://mimesniff.spec.whatwg.org/#image-mime-type) )。
2.  可选参数。带有一个布尔选项`multiple`的对象—选择一个或多个联系人(默认为假)。

*注意:* `"email", "address", "icon"` *部分设备和浏览器可能不支持。*

下面是`navigator.contacts.select()`的例子:

我们将走得更远。事实上，并非所有字段在某些设备和浏览器上都可用。因此，我们将使用`navigator.contacts.getProperties()`。此方法允许您获取设备和浏览器上可用联系人属性的列表。属性以字符串数组的形式返回。

在我们的例子中，`navigator.contacts.getProperties()`检查用户的设备是否支持`adress`属性。

下面是实现联系人选取器 API 的最后一个示例:

# 支持

除了 Android 上的 IOS Safari 和 Firefox，大多数移动设备都支持联系人选择器 API。在桌面上，Chrome、Opera 和 Edge 已经支持这一功能。

你可以在这里 找到关于联系人选择器 API 支持 [*的完整信息。*](https://caniuse.com/#search=ContactsManager%20API)

# 例子

下面是一个来自 [*我的宠物项目“我的聚会列表”*](https://contacts-picker.firebaseapp.com/) *的联系人 Picker API 的视频示例。*

# 最终想法

感谢你阅读这篇文章，我希望它对你有用。

联系人选择器 API 的实现看起来非常简单，成本也不高。在合适的地方，这个功能可以改善用户的生活。即使你没有地方实现它，那么关于这种 API 的知识也不会是多余的。

祝你好运，回头见！

# 资源

1.  [联系提货人 API 规范](https://wicg.github.io/contact-api/spec/#contacts-source-supported-properties)
2.  [支持我可以使用吗](https://caniuse.com/#search=ContactsManager%20API)
3.  [真实示例“聚会列表”](https://contacts-picker.firebaseapp.com/)
4.  [Github 回购](https://github.com/jyggiz/my-party-list/)