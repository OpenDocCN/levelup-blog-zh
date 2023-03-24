# 将命令性重构为声明性

> 原文：<https://levelup.gitconnected.com/refactoring-imperative-to-declarative-7b3e9a532f7c>

## 这是一次旅程，看看我如何能把一个换成另一个

![](img/15bbe7b1675743f3261ee67e6e15c727.png)

安托万·道特里在 [Unsplash](https://unsplash.com/s/photos/math?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

2019 年，苹果发布了两个声明性框架 SwiftUI 和 Combine。这让我在这个月开始思考，并确实发表了几篇关于这个主题的文章，就像这样。让联合收割机工作很有趣，真的。也许没有 SwiftUI 那么激动人心，但我保证这很有趣，也很有意义。

[](https://medium.com/better-programming/integrating-combine-in-swiftui-f3ffa2e170ba) [## 在 SwiftUI 中集成联合收割机

### 在 SwiftUI 代码中直接使用消费者和操作者

medium.com](https://medium.com/better-programming/integrating-combine-in-swiftui-f3ffa2e170ba) 

另一个相关但不同的问题是——如果你觉得文章有时有些学术性，我肯定你知道我在说什么——充满了你似乎无法融入现实世界的例子。让我们一石二鸟。

因此，考虑到这一点，让我们尝试将一些用命令式编码编写的代码重新分解为反应式/声明式代码。对我来说是一次有用的学习练习，希望对你来说也是一次有趣的旅程。加入我吧。

## 案情摘要

我有一个单词列表，法语动词，我想在我的应用程序上创建一个搜索字段，这将帮助我使用类似 lookup 的浏览器找到一个。你知道当你开始打字的时候，你的浏览器是如何提示你可能正在寻找的网站的。我想这么做。事实上，在我完成之后，我会用反应式/声明式编码来重构它。尽管在我们开始之前，让我来回答你需要问的问题。更改代码对我有什么好处？

## 收益

反应式编码的一个主要优点是，我可以很容易地取消我在其中启动的操作。这是反应式编码的理想选择，因为——想象一下，我希望所有动词都以 a 开头。我键入 a，然后立即在代码中启动一个查询，查找所有以 **a** 开头的动词。但是等我说完我就打 a **d** 。现在你要停止寻找以 **a** 开头的动词，开始寻找以 **ad** 开头的动词。你需要可以启动和停止的代码，实时响应你的请求的代码，反应式代码。

## 代码

我从一个单例类开始，它将保存我的动词表、我的搜索字段和找到的动词子集。

```
let changePublisher = PassthroughSubject<String,Never>()
var subscriptionsV = Set<AnyCancellable>()class Verbs: ObservableObject {
  @Published var name: String = "" {
    willSet {
      changePublisher.send(newValue)
    }
  }
@Published var table:[String] = []
@Published var verbs:[String] = []
static var shared = Verbs()
}
```

这里真的没有火箭科学。名称变量是搜索字段。当我更改它的值时，它将触发一个组合发布器，我将在界面中捕获该发布器，并使用正则表达式启动一个查询来匹配我的表中的动词。我加载在 verbs 变量中找到的动词[这也是一个表]。

这当然也是你需要的代码。

```
extension String {
  func beginning(with: String) -> Bool {
    if self.range(of:with, options: .regularExpression) != nil { return true }
  return false
  }
}
```

当然，这不会像现在这样工作，你遗漏了 downLoadVerbsV 方法，我将在这里发布。

以及调用它的方法。

最后还有一件事，这是我使用的动词表的一小部分。存储在应用程序内的文本文件，称之为“动词. txt”。

```
7,parler,0,easy,avoir,parlé,parl
15,créer,0,easy,avoir,créé,cré
25,finir,0,easy,avoir,fini,fin
26,haïr,0,easy,avoir,haï,ha
31,assaillir,0,easy,avoir,assailli,assaill
34,battre,0,easy,avoir,battu,batt
39,conclure,0,easy,avoir,conclu,conclu
43,courir,0,easy,avoir,couru,cour
```

把所有这些放在一起，它应该工作。

邦。但是我们还没有完成，不，我们实际上还没有开始——你看最后一个方法，下载动词有一些有趣的/挑战性的重构机会，我还没有包括在内。解析数据文件的循环，我们开始吧。

我的第一次传球是这样的。

```
for lines in content! {
  Just(lines)
    .filter { $0.contains(",") }
    .sink(receiveValue: {
      let verb = $0.split(separator: ",")
      verbsUploaded.append(String(verb[1]))
    })
    .store(in: &subscriptionsV)
}
```

我们像以前一样使用相同的循环，发布每一行，同时过滤掉空白的行，解析行中的 CSV 并保存它。还可以，但是我想我可以做得更好。第二个解析如下所示。

```
for lines in content! {
  Just(lines)
    .filter { $0.contains(",") }
    .compactMap { $0.singleWord(no: 1) }
    .sink(receiveValue: {
       newVerb = $0
    })
  .store(in: &subscriptionsV)
}
```

在这一篇中，我介绍了另一个扩展——我将在下面展示，实际上，这是您在下载方法中已经有的 willSet 安排。

```
extension String {
  func singleWord(no: Int) -> String {
    let words = self.split(separator: ",")
    return String(words[no])
  }
}
```

这个更好，但是我们能不能把那个循环去掉？您需要使用 flatMap 来实现这一点。开始了，这是第三版。

当然它是原来的两倍大，但是记住这个理论上你可以取消，原来的你不能。

但是好了，我们已经完成了。让我们回到 SwiftUI 代码。这里有一个循环，我也可以重构。这一个。

```
for verb in searches.table {
  if verb.beginning(with: searchString) {
    searches.verbs.append(verb)
  }
}
```

嗯——老实说，这是“循环”。这里我们开始搜索动词，如果我的搜索参数中途改变，这是我可能想要重新开始的代码。现在我的第一步是直接进入这一行。

```
searches.verbs = searches.table.filter { $0.beginning(with: searchString) }
```

它非常高效，是声明式的；但我没有使用联合收割机，我不能重新启动它。我需要第二次机会。这是我需要的。

```
searches.table.publisher
  .filter { $0.beginning(with: searchString) }
  .sink(receiveValue: { searches.verbs.append(String($0)) })
  .store(in: &subscriptionsV)
```

在这一点上，我担心我需要停止，因为我已经达到了我的字数限制。对应用程序进行更改并试用。我只需要再做一件事。下次我要想办法取消搜索。跟随我在 medium.com 获得更多的文件，实际上读一些我的旧的，像这一个，另一个联合的旅程。

[](https://medium.com/better-programming/validate-passwords-in-swiftui-forms-using-combine-a6f2ba4fd83b) [## 使用 Combine 验证 SwiftUI 表单中的密码

### 验证凭证的声明式编程

medium.com](https://medium.com/better-programming/validate-passwords-in-swiftui-forms-using-combine-a6f2ba4fd83b) 

保持冷静，继续编码。