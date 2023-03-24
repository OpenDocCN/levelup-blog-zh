# 使用 SwiftUI 进行本地化

> 原文：<https://levelup.gitconnected.com/localization-with-swiftui-5abbeb275d5>

## 如何添加本地化？如何调试错误？

SwiftUI 代码中的本地化就像写字符串一样简单。要了解如何设置可本地化的文件，需要学习关于本地化的其他基础知识(可以忽略与 UIKit 相关的代码):

[](https://medium.com/lean-localization/ios-localization-tutorial-938231f9f881) [## iOS 本地化教程

### 本地化是使您的应用程序支持其他语言的过程。在许多情况下，你用英语制作你的应用程序…

medium.com](https://medium.com/lean-localization/ios-localization-tutorial-938231f9f881) 

对于 SwiftUI 本地化，我们将使用以下可本地化的设置:

🇬🇧

🇷🇺

🇬🇧Plural

🇷🇺Plural

SwiftUI localisable 与[字符串插值](https://www.hackingwithswift.com/read/0/5/string-interpolation)一起工作。SwiftUI 文本有一个默认初始化器:

```
**public** init(_ key: LocalizedStringKey, **//localisation key**
             tableName: String? = nil, **//table name if nil takes Localisable.strings and Localisable.stringsDict**
             bundle: Bundle? = nil, **//default main bundle**
             comment: StaticString? = nil)
```

我们可以使用本地化键直接初始化文本，如下所示:

```
Text(**"Quotes"**)//renders: 
/* 
   English: Quotes
   Russian: Котировки
*/---------------------------------------------------------------let author = "Jhon Doe"
Text("**Author_Name** \(**self**.author)")
//renders: 
/* 
   English: Author Name: Jhon Doe
   Russian: Имя автора: Jhon Doe
*/---------------------------------------------------------------**let** likes: UInt = 10
Text("\(**self.**likes) **Like(s)**")
//renders: 
/* 
   English: 10 Likes
   Russian: 10 Лайков
*/
```

轻松干净！

![](img/155f9bcd1be08ac77436d74f1b62a011.png)![](img/b1c488d66416364c46b829c1ae8703f0.png)

茹恩

**调试错误:**

*   **用逐字文本初始化器处理，**文本有两个初始化器。

```
init(verbatim content: String)  **//this keeps string literal i.e. No localisation** **//Use For Localization**
init(_ key: LocalizedStringKey, 
             tableName: String? = nil,
             bundle: Bundle? = nil, 
             comment: StaticString? = nil)
```

*   **用本地化字符串键** **初始化器处理。** LocalizedStringKey 有两到三个初始化器。都有不同的目的:

```
**init**(stringLiteral value: String)
//**as parameter name suggest, it does not interpolate and try  to 
// to localize string as it is**
//Text(**"Quotes"**) -> **Works!**
//Text("**Author_Name** \(**self**.author)") -> **Will not** **Work.**
//Text("\(**self.**likes) **Like(s)**") -> **Will not** **Work.
_________________________________________________________****init**(**_** value: String)
//All type of Localizable key work with this initialiser 
_________________________________________________________ **init**(stringInterpolation: LocalizedStringKey.StringInterpolation)
// this requires manual and 
```

*   **错误/不同的插值值类型。**插值发生在编译时，编译器可能以不同的格式插值变量。这可能导致不连续的定位。

```
let **likes**: UInt = 10
print(LocalizedStringKey(**"**\(**likes) Like(s)"**))**LocalizedStringKey(key: "%llu Like(s)", hasFormatting: true, arguments: [SwiftUI.LocalizedStringKey.FormatArgument(value: 10, formatter: nil)])
------------------------------------------------** let like = 10
print(LocalizedStringKey(**"**\(**like) Like(s)"**))**LocalizedStringKey(key: "%lld Like(s)", hasFormatting: true, arguments: [SwiftUI.LocalizedStringKey.FormatArgument(value: 10, formatter: nil)])** //this will not localize as **%lld** is not part of our expected localizable key.
```

**限制:**

SwiftUI canvas 无法呈现**可插值的**局部变量。

```
//Text(**"Quotes"**) -> **Works on canvas!**
//Text("**Author_Name** \(**self**.author)") -> **Will not** **render on canvas.**
//Text("\(**self.**likes) **Like(s)**") -> **Will not** **render on canvas.**
```

**演示:**

[](https://github.com/prafullakumar/SwiftUiProj) [## prafullakumar/SwiftUiProj

### 通过在 GitHub 上创建一个帐户，为 prafullakumar/SwiftUiProj 开发做出贡献。

github.com](https://github.com/prafullakumar/SwiftUiProj) 

**参考:**

[](https://developer.apple.com/videos/play/wwdc2019/402/) [## Swift 新功能- WWDC 2019 -视频-苹果开发者

### Swift 现在是苹果所有平台上许多主要框架的首选语言，包括…

developer.apple.com](https://developer.apple.com/videos/play/wwdc2019/402/)