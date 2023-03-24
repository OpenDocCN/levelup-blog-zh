# 该代码无服务器部署

> 原文：<https://levelup.gitconnected.com/that-code-deployed-serverless-8c1a455c5b98>

![](img/eb76df606a157daa26a562059f587f04.png)

保罗·花冈在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

## Nimbella 如何向 Flutter 应用程序发送升级

每一个移动应用程序在某一点上都需要升级，必须向用户交付**错误修复**和**新功能**。我最近意识到，使用无服务器升级现有应用程序是多么简单。

[**无服务器**](https://www.redhat.com/en/topics/cloud-native-apps/what-is-serverless) 是一种[云原生](https://www.redhat.com/en/topics/cloud-native-apps)开发模式，允许开发人员构建和运行应用程序，而无需管理服务器。

一旦部署完毕，无服务器应用就会响应，并且[会根据需要自动](https://www.redhat.com/en/topics/automation) **放大**和**缩小**。最后，当无服务器功能闲置时，它不会产生任何成本。

我将讨论一个无服务器**函数**的例子，它可以应答用 Flutter 编写的移动应用程序的呼叫。

## 摆动

我认为 **Flutter** 是开发移动应用程序的框架(顺便说一下，它是最近一次大升级的主角[，它也可以用于桌面应用程序)。](https://medium.com/flutter/whats-new-in-flutter-2-0-fe8e95ecc65)

无论如何，下一节中的**无服务器考虑**是不可知的，它们可以[很容易地应用到其他**框架**](https://filippovalle.medium.com/i-upgraded-my-app-to-universal-binary-cd969719c33e) 。

[](https://flutter.dev) [## 颤振-美丽的原生应用程序在创纪录的时间

### 借助状态热重新加载，在数毫秒内将您的应用描绘得栩栩如生。使用一组丰富的完全可定制的小部件来构建…

颤振. dev](https://flutter.dev) 

## 宁贝拉

为了与移动应用程序进行交互，需要一个服务器来提供有关当前版本和文件的信息，以执行升级。这可以通过多种方式实现，使用无服务器功能的一个好处是，它们只在真正需要的时候才在线。它们可以轻松扩展，而且通常比标准服务器便宜。

[](https://nimbella.com) [## 宁贝拉

### copy 他们平台的前瞻性云不可知论性质给我留下了深刻的印象，尤其是因为它可以…

nimbella.com](https://nimbella.com) 

部署无服务器功能有许多方法，其中之一是 **Nimbella** 。

Nimbella 提供了一个有一些限制的免费服务，但足以开始使用它。此外，部署到 Nimbella 非常**容易**和**快速**。一个人只需要写一个带有主函数的代码，当服务器被**调用**时**回答**。

## 文件树

宁贝拉项目的一个例子如下。

```
serverlessApp
|____packages
| |____project.yaml
| |____myApp
| | |____getVersion
| | | |____.gitignore
| | | |____appcast.xml
| | | |______main__.py
```

一个**文件夹** *serverlessApp* 包含一个**包** *myApp* 带有一个**函数** *getVersion* 。getVersion 包含 *__main__。py* 与本系统的**主功能**。

## 项目文件

项目的所有规范都在一个 *project.yaml* 文件中。**运行时**是 *python3* 并且 *web key* 被设置为 **false** :我们不打算部署 web app。

```
bucket:  
strip: 1
packages:  
- name: myApp    
  web: false    
  actions:      
    - name: getVersion        
      runtime: python:3
      limits:          timeout: 60000
```

## __main__。巴拉圭

这个系统的核心如果是**功能**主。它读取一个文件并返回一个带有**头**的 HTTP **主体**。 *Content-Type* 头被设置为 *application/rss+xml* ，因为该函数应该广播 rss 提要。

```
def main(params): 
  with open("appcast.xml") as file:  
    appcast = file.read()  
  return {"body":appcast,      
          "headers":{"Content-Type":"application/rss+xml"}
         }
```

***appcast.xml*** 是一个包含更新后的应用程序规格的文件。根据 Flutter 文档，它遵循 [sparkle](https://sparkle-project.org/documentation/publishing/) 模式。

```
<?xml version="1.0" encoding="utf-8"?>
<rss version="2.0" xmlns:sparkle="https://.nimbella.io/api/MyApp/getVersion">    <channel>        
<title>MyApp - Appcast</title>        
<item>
<title>Version 2.0.0</title>            
<description>New awesome version</description>            <pubDate>Thu, 11 Mar 2020 12:00:00 +0000</pubDate>            <enclosure url="https://url/to/app/file" sparkle:version="2.0.0" sparkle:os="android" />        
</item>    
</channel>
</rss>
```

## 部署无服务器

当目录中的所有文件都在正确的位置时，运行`nim project deploy serverlessApp`就足够了，几秒钟后，该项目将部署到 Nimbella 基础架构，并准备好由安装在不同手机上的应用程序进行查询。

额外收获:基础设施会根据请求的数量自动**缩放**，无论**一个**还是**几千个**手机都会查询它，它总能正确回答。

我们需要刚刚部署的服务的 url，这可以通过命令`nim action get myApp/getVersion --url`获得

## 升级程序包

来自 flutter 的[升级包](https://pub.dev/packages/upgrader)提供了所有必要的东西来检查新的更新，并最终下载我们应用程序的**升级版**。

有必要创建一个带有 Nimbella url 的 *AppCastConfiguration* 。

```
final appcastURL = ‘https://.nimbella.io/api/myApp/getVersion'; final cfg = AppcastConfiguration(url: appcastURL, supportedOS: [‘android’]);
```

然后在**构建**我们的**主**接口的过程中，将现有代码封装到一个 *UpgradeAlert* 小部件中就足够了。

```
UpgradeAlert( appcastConfig: cfg, 
              debugLogging: true, 
              child: YourWidget())
```

## 结论

就这样，从现在开始，只需更新 *appcast.xml* 文件并将新项目部署到 Nimbella et voilà，每台设备都将由新更新**触发**。