# 用 Branch.io 深度链接 Ionic 应用

> 原文：<https://levelup.gitconnected.com/deeplinking-in-ionic-apps-with-branch-io-ba1a1c4ed227>

## 如何使用 Branch.io 和 intercept 参数为您的 Ionic 应用程序设置深度链接

![](img/11265238b7687fb1a4339bd97105fbee.png)

Javier Allegue Barros 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

我分享[一天一招](https://medium.com/@david.dalbusco/one-trick-a-day-d-34-469a0336a07e)直到原定的 2020 年 4 月 19 日瑞士新冠肺炎隔离期结束。离第一个里程碑还有六天。希望更好的日子就在前面。

上周五，我不得不快速实现一个应用程序的深度链接支持，我们正在敲定。它的目标过去是，现在仍然是，帮助孤独的人减少孤独感，更容易在他们的亲戚中找到帮助，这在平时非常有用，特别是对老年人，我们认为，但在危机时可能更有用。

结果苹果公司周六拒绝了它，因为他们认为我们的项目只专注于新冠肺炎，只接受认证机构的相关项目😢。如果有兴趣，美国消费者新闻与商业频道发表了一篇关于[主题](https://www.cnbc.com/2020/03/05/apple-rejects-coronavirus-apps-that-arent-from-health-organizations.html)的文章。

无论如何，不是在这里分享我对那些变得比国家和人民更强大的公司的看法，而是在这里分享技术技巧和诀窍😉。

这就是为什么这里是我如何快速实现这样一个概念，包括如何捕捉链接的参数。

# 什么是深度链接？

对我来说，深度链接解决了与移动应用程序相关的各种问题:

1.  一个应用程序可以在应用程序商店 Google Play 中作为渐进式网络应用程序使用，甚至可能在其他商店中使用。对于所有这些，它将在不同的链接或 URI 的帮助下访问，因此，它使你的沟通有点困难，因为你必须沟通多个链接(“如果你使用 Android，请单击此处。如果您使用的是 iOS，请点击此处。等等。).由于深层链接，有可能提供一个单一的网址链接用户到适当的资源，适当的目标。
2.  用户可能已经或尚未安装您的应用程序。这就是为什么，当你给他们提供一个链接时，你可能希望他们直接链接到它，如果已经安装，或者自动链接到商店，如果他们还没有安装。
3.  最后，您可能需要为您的应用程序提供参数。如果用户点击一个链接，没有应用程序，去商店，安装应用程序，然后启动它，会发生什么？参数将会丢失。也就是说，深度链接可以帮助维护和提供参数，直到用户有效地启动应用程序。

# 深度链接与自定义 URI 方案

深度链接不应该与自定义 URI 方案相混淆。

一个定制的 URI 方案，例如`myapp://`，是一个可以在移动设备上调用你的应用程序**的方案，但是**只有在应用程序被安装后才起作用。在此之前，设备不知道这样的方案。

# Branch.io

我一般不写任何开源解决方案，但到目前为止，我没有找到比 [Branch.io](https://branch.io) 更好的建立深层链接的解决方案。

有一个 [Cordova Ionic 插件](https://github.com/ionic-team/ionic-plugin-deeplinks)来处理这些由社区维护的链接。几年前，当 Ionic 推出时，我起初使用它，但最终选择了分支解决方案，但老实说，我不记得具体原因了，可能是一个特定的案例。

Branch 有一个免费计划。

# 设置

Branch 提供了全面的[文档](https://help.branch.io/developers-hub/docs/cordova-phonegap-ionic)关于如何配置他们的服务，甚至他们的平台，一旦你注册了，就可以直接使用了。

只有重要的事情值得注意:您的应用程序**必须在商店**中可用，才能正确设置深层链接。当你配置它的时候，你将不得不搜索它并把它和你的帐户联系起来，因此它必须是公开的和可用的，否则你将无法完成配置。

但是，值得注意的是，您不必等到发布后才实现和测试它。

# 就是这样

已经这样了！如果你的唯一目标是为我们的现有和潜在用户提供深层链接，这些用户要么指向你的应用程序(如果安装了的话)，要么通过一个链接指向商店，那你就完了。

不需要安装插件，不管你使用的是 [Cordova](https://cordova.apache.org) 还是[电容](https://capacitor.ionicframework.com)。

例如，在 [GitHub](https://github.com/peterpeterparker/tietracker) 上查看我的应用程序领带追踪器的源代码。正如你所注意到的，这里没有任何对 Branch 的引用，但即使如此，我也可以提供一个单独的链接[https://tietracker.app.link/](https://tietracker.app.link/)，它会引导你找到已安装的应用程序、商店或者 PWA。

# 截距参数

设置好之后，您可能会有兴趣截取所有进程的参数，当然要记住跟踪是不好的，只有匿名使用才是可接受的。

## 装置

相关的 Cordova 插件在 [npm](https://www.npmjs.com/package/branch-cordova-sdk) 中找到位置，并且可以如下使用:

```
ionic cordova plugin add branch-cordova-sdk
```

## 配置

安装后，您必须为您的平台配置它。在您的`config.xml`中，必须添加一个相关条目。

当你设置你的应用程序时，所有分支机构的信息将由他们提供，iOS 团队发布标识符在你的苹果“appstoreconnect”仪表板中找到一个位置。

```
<branch-config>
    <branch-key value="key_live_2ad987a7d8798d7a7da87ad8747" />
    <uri-scheme value="myapp" />
    <link-domain value="myapp.app.link" />
    <ios-team-release value="BE88ABJS2W" />
</branch-config>
```

# 履行

[Ionic Native](https://ionicframework.com/docs/native/branch-io) 提供了对 Branch 的支持，但是到目前为止我从未使用过它，这就是为什么我不会在下面的例子中使用它。我也只用 Cordova 在 [Angular](https://angular.io/) 应用中实现了它，这就是为什么我在这里使用这样的技术。

在我们的主组件`app.component.ts`中，我们首先声明一个新的接口，它表示我们想要截取的参数。在这个例子中，我将其命名为`$myparam`。值得注意的是，开箱即用，分支填充前向参数前缀为 **$。**

```
interface DeeplinkMatch {
    $myparam: string;
}
```

一旦我们的主要组件被初始化，我们就在平台上添加一个监听器，以便只在它被挂载时初始化拦截。

```
ngAfterViewInit() {
    this.platform.ready().then(async () => {
        await this.initDeepLinking();
    });
}
```

根据用户的设备，他/她可以作为移动应用或网络应用来启动应用。这是两种不同的查询参数的方式，这就是为什么我们处理它们的方式不同。

```
private async initDeepLinking(): Promise<void> {
    if (this.platform.is('cordova')) {
        await this.initDeepLinkingBranchio();
    } else {
        await this.initDeepLinkingWeb();
    }
}
```

当涉及到网络时，我们可以使用`platform`来搜索参数。然后，它可以以不同的方式提供和前缀，当然，我喜欢测试所有的可能性。

```
private async initDeepLinkingWeb(): Promise<void> {
    const myparam: string = 
                   this.platform.getQueryParam('$myparam') ||
                   this.platform.getQueryParam('myparam') ||
                   this.platform.getQueryParam('%24myparam');
    console.log('Parameter', myparam);
}
```

最后可以处理 Branch 提供的手机 app 参数。

值得注意的是，参数是异步提供的。这就是为什么你不能修正它是在启动时出现的，但必须考虑到它可能是在延迟后提供的。

```
private async initDeepLinkingBranchio(): Promise<void> {
    try {
        const branchIo = window['Branch'];

        if (branchIo) {
            const data: DeeplinkMatch = 
                        await branchIo.initSession(); if (data.$myparam !== undefined) {
                console.log('Parameter', data.$myparam);
            }
        }
    } catch (err) {
        console.error(err);
    }
}
```

瞧，🥳，我们能够处理参数的深层链接。例如，如果我们向用户提供一个 URL，如[https://myapp.app.link/?$myparam=yolo](https://myapp.app.link/?$myparam=yolo)，我们将能够拦截“yolo”😁。

## 总共

如果你需要的话，下面是上面的一段代码:

```
import {AfterViewInit, Component} from '@angular/core';

import {Platform} from '@ionic/angular';
import {SplashScreen} from '@ionic-native/splash-screen/ngx';
import {StatusBar} from '@ionic-native/status-bar/ngx';

interface DeeplinkMatch {
    $myparam: string;
}

@Component({
    selector: 'app-root',
    templateUrl: 'app.component.html',
    styleUrls: ['app.component.scss']
})
export class AppComponent implements AfterViewInit {
    constructor(
        private platform: Platform,
        private splashScreen: SplashScreen,
        private statusBar: StatusBar
    ) {
        this.initializeApp();
    }

    initializeApp() {
        this.platform.ready().then(() => {
            this.statusBar.styleDefault();
            this.splashScreen.hide();
        });
    }

    ngAfterViewInit() {
        this.platform.ready().then(async () => {
            await this.initDeepLinking();
        });
    }

    private async initDeepLinking(): Promise<void> {
        if (this.platform.is('cordova')) {
            await this.initDeepLinkingBranchio();
        } else {
            await this.initDeepLinkingWeb();
        }
    }

    private async initDeepLinkingWeb(): Promise<void> {
        const myparam: string = 
                       this.platform.getQueryParam('$myparam') ||
                       this.platform.getQueryParam('myparam') ||
                       this.platform.getQueryParam('%24myparam');
        console.log('Parameter', myparam);
    }

    private async initDeepLinkingBranchio(): Promise<void> {
        try {
            const branchIo = window['Branch'];

            if (branchIo) {
                const data: DeeplinkMatch = 
                            await branchIo.initSession();

                if (data.$myparam !== undefined) {
                    console.log('Parameter', data.$myparam);
                }
            }
        } catch (err) {
            console.error(err);
        }
    }
}
```

# 摘要

它成功了。老实说，这不是这份工作最有趣的部分，不是说它很复杂，也不是说它需要设置或使用，只是不太有趣。

呆在家里，注意安全！

大卫