# 简单的 PWA 模板项目

> 原文：<https://levelup.gitconnected.com/a-simple-pwa-template-project-85ae4927d14e>

## 渐进式 Web 应用程序的普通 JavaScript 模板项目

![](img/7d6c9e990d62b45811800d89c94474eb.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Clément Hélardot](https://unsplash.com/@clemhlrdt?utm_source=medium&utm_medium=referral) 拍摄

开始一个新项目是令人兴奋的。

创建模板项目不是。

自从几年前我创建了我的第一个渐进式网络应用程序(PWA ),我就把这个代码库作为每个新的 PWA 项目的起点。随着越来越多的项目成为 PWAs 或单页应用程序(spa ),我开始质疑我五年前的代码，并决定仔细检查一切，为 PWAs 创建一个简单的模板项目[。](https://github.com/pingpoli/pwa-template-project)

# PWA 要求

让我们从讨论[使应用程序成为可安装 PWA](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Installable_PWAs) 的要求开始:

*   Web 清单
*   经由 HTTPS 供应
*   应用程序图标
*   服务行业人员

## Web 清单

web manifest 是一个简单的 JSON 文件，它定义了应用程序的一些元属性，如名称和图标列表。唯一必需的字段是*名称*和*图标*，但是还有一些额外的可选参数。

```
{
"name": "PWA Template",
"short_name": "PWA Template",
"start_url": "index.html",
"display": "standalone",
"icons": [
    {
    "src": "img/icon_120.png",
    "sizes": "120x120",
    "type": "image/png"
    },
    {
    "src": "img/icon_180.png",
    "sizes": "180x180",
    "type": "image/png"
    },
    {
    "src": "img/icon_192.png",
    "sizes": "192x192",
    "type": "image/png"
    },
    {
    "src": "img/icon_512.png",
    "sizes": "512x512",
    "type": "image/png"
    }
],
"background_color": "#ffffff",
"theme_color": "#ffffff"
}
```

要使用清单文件，我们需要将它包含在主 HTML 文件中:

```
<link rel="manifest" href="manifest.json">
```

## 应用程序图标

PWA 需要一个应用程序图标，用于在主屏幕或启动器上显示应用程序。图标是在 web 清单文件中定义的。可能的图像大小有一个很大的列表，但看起来 iOS 的最小尺寸是 120x120 和 180x180 像素，Android 的最小尺寸是 192x192 和 512x512 像素，所以我将它们添加到了模板项目中。

## HTTPS

每个 PWA 都必须通过 HTTPS 进行安装。使用 SSL 总是一个好主意，考虑到获得证书是多么便宜(实际上是免费的，让我们加密一下吧)和容易，没有理由不使用它。不幸的是，SSL 需求已经成为我最近一个项目中的一个问题。这是一个涉及一些敏感数据的个人项目，我不想把它暴露在互联网上。所以我在我的本地 Raspberry Pi 服务器上运行它，它的自签名证书不能与 PWAs 一起工作。

# 服务行业人员

可以说 PWA 最重要的方面是服务人员。它负责缓存、推送通知和其他功能。五年前的大部分旧代码看起来仍然很好，我只做了一些小的改动。我没有在模板项目中包含任何推送通知代码，因为大多数项目不需要它，并且以后很容易添加。

服务工作者最有用的功能是缓存。这不仅使 PWA 能够在脱机模式下运行，而且大大加快了加载速度。

让我们从服务人员开始:

```
self.importScripts( "config.js" );

// cache name for cache versioning
var cacheName = "v"+serviceWorkerCacheVersion+":static";

// when the service is installed
self.addEventListener( "install" , ( event ) =>
{
    // cache all required files for offline use
    event.waitUntil( caches.open( cacheName ).then( ( cache ) =>
    {
        return cache.addAll( [
            "/",
            "config.js",
            "index.html",
            "manifest.json",
            "style.css",
            "src/App.js",
            "src/main.js",
            "img/favicon.png",
            "img/icon_120.png",
            "img/icon_180.png",
            "img/icon_192.png",
            "img/icon_512.png"
        ] );
    }));
    console.log( "sw > installed" );
    // activate the new service worker version immediately
    self.skipWaiting();
});
```

我们需要关注的第一个事件是*安装*事件。可以想象，它是在安装服务人员时触发的。此时，我们可以将所有需要的文件添加到缓存中。这些只是一些文本文件和一些小图片。如果您的应用程序需要更大的文件，如音频、视频或大量图像，您可能需要采用更复杂的缓存策略。

```
// when a new version of the service worker is activated
self.addEventListener( "activate" , ( event ) => 
{
    // delete the old cache
    event.waitUntil( caches.keys().then( ( keyList ) => Promise.all( keyList.map( ( key ) => 
    {
        if ( key !== cacheName ) return caches.delete( key );
    }))));
    console.log( "sw > activated" );
});
```

下一个事件是*激活*事件，该事件在服务人员被激活时触发。因为我们在 *install* 事件中使用了`self.skipWaiting()`函数，所以这将在安装服务人员后不久发生。每次我们更新服务工作者时，我们都应该增加版本号，以便为新版本生成一个新的缓存。正因为如此，我们应该很好地在新版本激活时删除旧版本的缓存。

```
// when the browser fetches a URL
self.addEventListener( "fetch" , ( event ) =>
{
    // cache first caching - only go to the network if no cache match was found, other caching strategies can be used too
    event.respondWith( caches.match( event.request ).then( ( response ) => 
    {
        return response || fetch( event.request );
    }));
});
```

最后，我们需要实现当浏览器请求 URL 时调用的 fetch 事件。在这里，我们希望快速响应缓存的文件，如果在缓存中找不到它，只从网络加载它。这就是所谓的缓存优先缓存。根据您的使用情况，您可能想要采用不同的缓存策略。

值得一提的是，服务人员过去无法处理范围请求。[这在大多数现代浏览器版本](https://chromestatus.com/feature/5648276147666944)中是固定的，但是如果您正在请求使用范围请求的大文件，并且需要支持广泛的平台，您可能需要研究这一点。我在旧的服务工作者代码中对此有一个解决方法，但是为了清晰起见，我选择在模板项目中删除它。

# 主 JavaScript 文件

服务人员完成后，我们需要将它安装在应用程序的主 JavaScript 文件中。幸运的是，这很容易做到:

```
// service worker
if ( "serviceWorker" in navigator ) 
{
    // register the service worker
    navigator.serviceWorker.register( "sw.js" ).then( ( reg ) =>
    {
        console.log( "service worker has been registered successfully" );
        serviceWorkerRegistration = reg;
    }
    ).catch( ( error ) =>
    {
        console.log( "failed to register service worker" , error );
    });
}
```

在这里，我们还可以显示安装提示，要求用户将 PWA 添加到他们的主屏幕。但是，这在某些平台上可能行不通。我最近没有做任何测试，但我认为目前主要是针对 Android 上的 Chrome。

```
// app install banner -- may not work on every platform
window.addEventListener( "beforeinstallprompt" , ( event ) =>
{
    event.userChoice.then( ( choiceResult ) =>
    {
        console.log( choiceResult.outcome ); // either "accepted" or "dismissed"
    });
});
```

我不擅长创建模板项目。大多数时候，我对一个新项目感到兴奋，我想尽快开始。所以我最终只是复制了一个相同类型的旧项目，做了一些调整，然后开始工作。这意味着来自第一个项目的混乱甚至可能不正确的代码被一遍又一遍地复制。

当我即将开始另一个 PWA 项目时，我终于花时间清理了代码并创建了一个简单的模板项目，我可以用它作为将来的起点。你也可以在你的项目中随意使用这个项目，如果你注意到任何可以改进的地方，请告诉我。

[**PWA 模板项目 Github**](https://github.com/pingpoli/pwa-template-project)