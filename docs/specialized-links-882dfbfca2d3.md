# 专门的超链接

> 原文：<https://levelup.gitconnected.com/specialized-links-882dfbfca2d3>

## 在 Dart 中展示

![](img/373cc4eb07aa1902b2dcb75f27a94d22.png)

超链接(或更短的链接)是网站不可分割的一部分。尽管打开它们有时很有挑战性，但大多数时候还是很简单的(应该如此)。

如果你想体验一下如何在[颤振](https://flutter.dev)中做到这一点，你可以[阅读这篇文章](https://medium.com/@constanting/flutter-hyperlinks-d2eee3fd24f)。

上面引用的文章只讨论了 **web 地址**或[统一资源定位符](https://en.wikipedia.org/wiki/URL)(**URL**)，一种特定类型的[统一资源标识符](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier) ( **URIs** )。

打开网址的示例:

```
**openLink**('[https://g.co/FlutterInteract](https://g.co/FlutterInteract)');
```

在这里，我们将探索语言无关的 URIs，它使我们能够做的不仅仅是导航到网址。

## 发送电子邮件

```
**mailto**:<email>[,<email>]*[?<arguments>]
```

[**mailto**](https://tools.ietf.org/html/rfc6068) 链接是目前使用最多的特殊链接。

当我们想要打开设备上的默认电子邮件客户端时，这非常有用。

大多数人没有充分利用这个特殊链接接受的**参数**，通常像这样使用它:

```
openLink('**mailto**:email@domain.tld');
```

为了让它打开具有多个电子邮件地址的电子邮件客户端，我们将使用以下格式:

```
openLink('**mailto**:emailOne@domain.tld,emailTwo@domain.tld');
```

我们可以忽略的**论点有:**

*   **主题** -我们想要的电子邮件主题
*   **正文** -我们想要的电子邮件
*   **抄送** -我们的消息[抄送](https://en.wikipedia.org/wiki/Carbon_copy)的逗号分隔的电子邮件列表
*   **密件抄送** -我们的消息[密件抄送](https://en.wikipedia.org/wiki/Blind_carbon_copy)的逗号分隔的电子邮件列表

下一个例子是利用 mailto 链接提供的所有选项的电子邮件:

```
openLink('**mailto**:email@domain.tld?**subject**=Support&**body**=Good morning,%0D%0A%0D%0AFeedback%20goes%20here&**cc**=emailOne@domain.tld&**bcc**=emailTwo@domain.tld');
```

**主体**参数可以使用`%0D%0A`作为行分隔符写在多行上，或者写一个多行消息，然后 [url 对其进行编码](https://www.w3schools.com/tags/ref_urlencode.asp)。

注意 **Gmail** 有点特别(至少在移动设备上)并且更喜欢新行的 html `**<br>**`标签(`%3Cbr%3E`是`<br>`的 URL 编码版本)。当电子邮件客户端没有将内容呈现为`text/html`时，这些标签将被呈现为`text/plain`。在这种情况下，您必须检测用户使用的是什么浏览器，然后调整新行是用于纯文本还是 html。

上面为 Gmail 修改的示例如下:

```
openLink('**mailto**:email@domain.tld?**subject**=Support&**body**=Good morning,%3Cbr%3E%3Cbr%3EFeedback%20goes%20here&**cc**=emailOne@domain.tld&**bcc**=emailTwo@domain.tld');
```

当涉及到定制用户反馈和支持网站的交互时，这可能是非常有用的，提供从平台自动收集的规范，而不会使用户经历对于普通用户来说提供这种数据的乏味任务，以及其他场景。

**电话呼叫**

```
**tel**:<phone>
```

当我们谈论有用性时， [**电话**](https://tools.ietf.org/html/rfc5341) 是其次的。

当我们想要触发一个呼叫时，我们可以使用这个。它可以在 iOS、Android 和 macOS 上运行。

```
openLink('**tel**:+40987654321');
```

当我们希望客户不输入我们的电话号码就能联系到我们时，我们需要打电话给某人，但又懒得手动输入电话号码，以及其他情况下，这很有用。

## 发送短信

```
**sms**:<phone>[,<phone>]
```

当我们想发送短信时，我们有 [**短信**](https://tools.ietf.org/html/rfc5724) 特殊链接。它也可以在 iOS、Android 和 MacOS 上运行。

```
openLink('**sms**:+40987654321,+40123456789');
```

我们可以让客户通过短信联系我们，同时避免让他们键入或复制/粘贴我们的电话号码。

## 其他人

还有很多其他的特殊链接，iOS[和 macOS](https://developer.apple.com/library/archive/featuredarticles/iPhoneURLScheme_Reference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40007899-CH1-SW1) ， [Android](https://developer.android.com/guide/components/intents-common) 和 [Windows](https://docs.microsoft.com/en-us/windows/uwp/launch-resume/launch-default-app) 都支持。

都可以在这里看到[。](https://en.wikipedia.org/wiki/List_of_URI_schemes)

我已经使用[Dart](https://dart.dev)(Flutter 下的编程语言)展示了一些特殊的链接，尽管它们是语言不可知的。其中一些是特定于平台的，这里我们只探讨了那些在最流行的平台上受支持的。

**openLink** 效用函数的定义如下。这负责打开我们发送给它的任何链接。在幕后，它将确定链接的类型(特殊或不特殊)，并基于我们所在的平台实际打开链接。

```
import 'package:url_launcher/url_launcher.dart';
import 'package:universal_html/html.dart' as html;
import 'package:flutter/foundation.dart';void **openLink**(String **_url**, [String **_target**='_blank']) async {
  List<String> _special = <String>['mailto:', 'sms:', 'tel:'];
  bool **_isSpecial** = false;
  _special.forEach((String val)=>_isSpecial=(_url.contains(val)?true:_isSpecial));
  _target = _isSpecial?'_self':_target;if(kIsWeb) {
    html.window.open(_url, _target);
  } else {
    if(await canLaunch(_url)) {
      launch(_url);
    }
  }
}
```

我已经开始了一个系列，命名为 [**飘动镖**](https://medium.com/tag/fluttering-dart/archive) ，旨在逐步解决从飘动开始所需的镖要领。

如果你愿意，你可以和我一起踏上探索飞镖的旅程。

就这些！