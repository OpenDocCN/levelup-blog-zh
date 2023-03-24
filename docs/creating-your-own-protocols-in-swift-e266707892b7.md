# 在 Swift 中创建您自己的协议

> 原文：<https://levelup.gitconnected.com/creating-your-own-protocols-in-swift-e266707892b7>

## 简单的解释

![](img/5a1fa7097e91870b98ac8e87886a7c08.png)

照片由[谢尔盖·佐尔金](https://unsplash.com/@szolkin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

简单来说，协议就是描述一些属性和方法的接口。协议允许开发人员在 Swift 中编写灵活且可扩展的代码，而不必牺牲语言的表现力。

# 我们开始吧

要创建协议，请使用 protocol 关键字，后跟您想要的名称，并用花括号定义。协议有两种类型:只读/读写。只读意味着只能获取变量，但不能设置变量。读写意味着您可以设置和获取属性。

协议的示例

然后，通过添加冒号并附加您想要采用的协议的名称来采用该协议。

在您采用`FullName`协议后，编译器会自动要求您添加所有必要的需求

# 协议扩展

创建协议扩展是一个非常强大的特性，它允许您扩展协议，而不必担心破坏现有代码的兼容性。通过这样做，您可以使一些方法成为可选的，或者为某些东西单独添加一个方法。

这个扩展使一个属性拥有一个常量默认字符串作为您将来要添加的名称。

# 包扎

这就是你应该知道的关于协议和如何创建协议的全部内容。我希望它是有趣的！

对其他相关协议感兴趣吗？欢迎访问我的其他相关文章:

*   [Swift 中的可编码协议是什么？](/what-is-codable-protocol-in-swift-50103c2d9d02)
*   [Swift 中的可比协议是什么？](/what-is-comparable-protocol-in-swift-8cb854e30cf7)
*   [Swift 中的公平协议是什么？](https://medium.com/cleansoftware/what-is-the-equatable-protocol-in-swift-3cced3f28219)
*   [Swift 中的 CustomStringConvertible 协议是什么？](https://medium.com/better-programming/what-is-the-customstringconvertible-protocol-in-swift-cbb766afac4d)

如果你有任何批评、问题或建议，欢迎在下面的评论区发表！

感谢阅读。