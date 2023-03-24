# 如何在您的 iOS 应用程序中查找 UIWebView 用途

> 原文：<https://levelup.gitconnected.com/how-to-find-and-remove-uiwebview-uses-in-your-ios-app-d9395f7baacc>

![](img/0b6280ca738c84141a22cabbe36d1889.png)

您可能在最近向 iTunes Connect 提交应用程序时看到过此警告，并想知道它是什么意思。

> ITMS-90809:不赞成使用的 API——苹果将停止接受使用 UIWebView APIs 的应用提交。更多信息参见[https://developer.apple.com/documentation/uikit/uiwebview](https://developer.apple.com/documentation/uikit/uiwebview)。

它说的是你的应用程序包含一个对 UIWebView API 的引用，该 API 现在在 iOS 12.0 中已被否决。苹果警告你，在你的应用被移除之前，它不会被批准。我一开始很困惑，因为我提交的应用程序没有使用任何 UIWebView 的。尽管如此，事实证明是依赖造成的。

如果您使用的是`UIWebView`，您应该将其迁移到使用 WKWebView。否则，如果依赖项存在，您应该将其升级到开发人员已移除该引用的较新版本。

# 查找对 UIWebView 的引用

要在您的应用程序中找到使用方法——在您的根目录中，您可以运行以下终端命令，该命令将突出显示包含该术语的文件的路径。然而，并不是所有的结果都暗示它包含了这一点。例如，一个众所周知的依赖项，PromiseKit 在注释中引用了它，但实际上并不使用 API，所以要确保首先查看使用它的代码行。

```
grep -r UIWebView .
```

## 二进制框架呢？

二进制框架不公开它们的纯文本源代码，所以 grep 不会直接作用于它们。相反，一旦你完成了你的应用程序的存档，你应该右击它，在 finder 中显示它。然后右键单击归档文件以显示包内容。从这里，您要导航到:

```
/Products/Applications/MyAppName.app
```

通过运行下面的 shell 脚本来查看`UIWebView`提及的框架符号。

```
for framework in Frameworks/*.framework; do
  fname=$(basename $framework .framework)
  echo $fname
  nm $framework/$fname | grep UIWebView
done
```

# 就成了

通过修复几个项目，我发现可能的罪魁祸首是社交登录，如`GoogleSignIn`和`FBSDKLoginKit`以及常见的依赖，如`Crashlytics`

希望这有助于您删除对 UIWebView 的引用。