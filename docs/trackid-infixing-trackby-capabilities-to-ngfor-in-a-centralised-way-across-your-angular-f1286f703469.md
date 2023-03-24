# TrackId:将 trackBy 功能以集中的方式嵌入到您的 Angular 应用程序中

> 原文：<https://levelup.gitconnected.com/trackid-infixing-trackby-capabilities-to-ngfor-in-a-centralised-way-across-your-angular-f1286f703469>

## 只是这个帖子的先决条件: [TrackByFunction](https://angular.io/api/core/TrackByFunction#trackbyfunction)

![](img/2055f1f559adfe5440809f9015acb5ef.png)

照片由 [Blocks Fletcher](https://unsplash.com/@blocksfletcher?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

很多时候，你可能已经意识到为你的`ngForOf`指令提供 trackBy 输入以优化性能，但是下一刻你就懒得一次在几个地方这么做了，而且感觉为正在进行的开发继续这么做开销太大。

我研究了实现这个`ngFor`结构指令的源代码，关于我如何扩展它来提供和支持这个用例。

在这里，我们模仿`ngFor`的选择器，针对我们的主机指令，并在其生命周期的正确时刻注入我们的定制功能。现在，除了输入属性，您还可以通过模块或组件提供者中的`TRACK_ID` DI 令牌提供`trackId`值，例如:

```
providers: [{provide: TRACK_ID, useValue: 'id'}]
```

并在你的层次上获得对这种优化技术的集中控制。

顺便提一下，如果你的[数据表](https://material.angular.io/cdk/table/api#CdkTable)需要一些数据源作为输入，并且在内部使用`ngFor`指令来遍历列表，你可以为它做类似的解决方案。

继续吧，只需用最少的代码就能显著提高应用程序的渲染性能。

> 如果你发现它是 interesting✨，请欣赏👏。这当然会激发😊