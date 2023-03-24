# Angular Universal +安装指南的优点以及如何避免常见缺陷

> 原文：<https://levelup.gitconnected.com/all-the-benefits-angular-universal-offers-installation-and-common-pitfalls-angular-universal-46b11887250a>

## Angular Universal 是一种允许开发人员在服务器上运行 Angular 应用程序的技术，支持服务器端渲染和改进的性能、SEO、安全性和服务器端功能。

## **什么是角通用**

Angular Universal 是一个工具，它允许 Angular 开发人员对他们的应用程序进行服务器渲染，从而提高性能并提供更好的用户体验。服务器端呈现包括在服务器上呈现应用程序的初始视图，而不是在浏览器中。这可以提供几个好处，包括提高性能、更好的搜索引擎优化和更流畅的用户体验，尤其是在较慢的设备或低质量的网络上。

![](img/9258a87bb77584e5a2736a5ff2971b4d.png)

斯蒂芬·菲利普斯-Hostreviews.co.uk 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Angular Universal 使 Angular 开发人员可以轻松地将服务器端渲染添加到他们的应用程序中。它提供了一组 API 和模块，可用于在服务器上呈现 Angular 应用程序，并自动处理服务器端呈现的细节，如处理请求、呈现视图以及在服务器和客户端之间传输状态。

总的来说，Angular Universal 对于希望提高应用程序性能和用户体验的 Angular 开发者来说是一个有用而强大的工具。通过使用 Angular Universal 添加服务器端渲染，开发人员可以创建更快、更高效、更具吸引力的应用程序，从而提供更好的用户体验。

## Angular Universal 提供哪些好处？

Angular Universal 提供了几个好处，包括:

1.  改进的性能和用户体验:服务器端渲染允许您的应用程序在服务器上渲染初始视图，这可以改善初始加载时间，并提供更好的用户体验。
2.  改进的 SEO:服务器端渲染允许搜索引擎索引你的应用程序的内容，这可以提高你的应用程序在搜索结果中的可见性。
3.  增强的安全性:服务器端呈现通过在将用户生成的内容发送到客户端之前在服务器上呈现这些内容，有助于抵御某些类型的攻击，如跨站点脚本(XSS)。
4.  改进的服务器端功能:Angular Universal 允许您在服务器上使用 Angular 应用程序，这为服务器端处理和渲染开辟了新的可能性。
5.  改进的代码共享和可移植性:Angular Universal 允许您在服务器和客户端之间共享应用程序的代码，这可以提高代码的可维护性和可移植性。

## 如何安装 Angular Universal？

1.  要安装 Angular Universal，您应该在系统上安装最新版本的 Angular 和 Angular CLI。您可以通过运行以下命令来检查您的当前版本:

```
ng --version
```

2.如果您没有最新版本，可以通过运行以下命令来更新 Angular 和 Angular CLI:

```
npm install -g @angular/cli@latest
npm install -g @angular/core@latest
```

3.一旦安装了 Angular 和 Angular CLI 的最新版本，就可以安装 Angular Universal 了。为此，您需要使用 Angular CLI 创建一个新的服务器端应用程序。这可以通过运行以下命令来完成:

```
ng generate universal <app-name>
```

4.将`<app-name>`替换为您的角度应用的名称。该命令将基于您现有的 Angular 应用程序创建一个新的服务器端应用程序，并将自动配置和安装 Angular Universal 的必要依赖项。

5.创建服务器端应用程序后，您需要更新现有的 Angular 应用程序，以使用 Angular Universal 提供的服务器端渲染。为此，您需要更新您的 Angular 应用程序的`AppModule`,以便从`@angular/platform-server`包中导入`ServerModule`和`ServerTransferStateModule`。

以下是为 Angular Universal 添加必要的导入后，您的`AppModule`应该是什么样子的示例:

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { ServerModule } from '@angular/platform-server';
import { ServerTransferStateModule } from '@angular/platform-server';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule.withServerTransition({ appId: '<app-id>' }),
    ServerModule,
    ServerTransferStateModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule
```

## 我如何检查 angular 是否在服务器上而不是在客户端上渲染我的应用程序？

要检查 Angular 是否在服务器而不是客户端上渲染你的应用，你可以使用`@angular/common`包提供的`isPlatformServer`函数。此函数返回一个布尔值，表明您的应用程序是在服务器上运行还是在客户端上运行。

以下是如何使用`isPlatformServer`功能检查您的应用程序是否正在服务器上运行的示例:

```
import { Component, Inject, PLATFORM_ID } from '@angular/core';
import { isPlatformServer } from '@angular/common';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  constructor(@Inject(PLATFORM_ID) private platformId: Object) {
    if (isPlatformServer(this.platformId)) {
      console.log('App is running on the server');
    } else {
      console.log('App is running on the client');
    }
  }
}
```

在这个例子中，`AppComponent`类注入了`PLATFORM_ID`令牌，并使用它来检查应用程序是运行在服务器上还是客户端上。如果应用程序正在服务器上运行，控制台会记录一条消息来表明这一点。否则，会记录一条不同的消息，指示该应用程序正在客户端上运行。

通过使用`isPlatformServer`功能，您可以检查您的 Angular 应用程序是否在服务器上而不是在客户端上进行渲染，并且您可以根据这些信息采取适当的措施。

## 安装 angular universal 有哪些常见的错误？

安装 Angular Universal 时的一些常见错误包括:

1.  未使用最新版本的 Angular and Angular CLI:Angular Universal 需要最新版本的 Angular and Angular CLI 才能正常工作。如果您使用的不是最新版本，在安装或使用 Angular Universal 时可能会遇到问题。
2.  没有正确配置你的应用程序:Angular Universal 使用一个基于你现有 Angular 应用程序的服务器端应用程序。如果这个服务器端应用程序配置不正确，Angular Universal 可能无法按预期工作，或者根本无法工作。
3.  不更新 Angular 应用程序的`AppModule`:为了使用 Angular Universal 提供的服务器端渲染，你需要更新 Angular 应用程序的`AppModule`,以便从`@angular/platform-server`包中导入必要的模块和组件。如果忘记这样做，Angular Universal 可能无法按预期工作。
4.  安装 Angular Universal 后不测试您的应用程序:安装 Angular Universal 后，测试您的应用程序以确保它按预期工作是很重要的。这可能包括在不同的设备和浏览器上测试应用程序，并检查可能出现的任何错误或问题。通过彻底测试您的应用程序，您可以确保 Angular Universal 正常工作，并提供您期望的好处。

综上所述，安装 Angular Universal 有几个常见的错误是你可以避免的，包括没有使用 Angular 的最新版本，没有正确配置你的应用，没有更新你的 Angular app 的`AppModule`，安装 Angular Universal 后没有测试你的 app。

## **其他一些常见的窍门让棱角变得万能**

1.  **通过使用为常见任务提供抽象的内置角度服务，避免引用窗口、文档和其他特定于 DOM 的全局变量**，例如使用 DOM 的 Renderer2 服务。您可以将 Renderer2 服务注入到组件中，并使用它的方法与 DOM 交互，而不是直接访问窗口或文档对象。否则，您的应用程序可能无法在服务器上正确呈现。

下面是一个如何使用 Renderer2 服务向 DOM 中的元素添加类的示例:

```
import { Component, OnInit, Renderer2 } from '@angular/core';

@Component({
  selector: 'app-my-component',
  templateUrl: './my-component.component.html',
  styleUrls: ['./my-component.component.css']
})
export class MyComponent implements OnInit {

  constructor(private renderer: Renderer2) { }

  ngOnInit() {
    // Get the element with the id 'my-element'
    const el = this.renderer.selectRootElement('#my-element');

    // Add the class 'my-class' to the element
    this.renderer.addClass(el, 'my-class');
  }

}
```

通过使用 Renderer2 服务和其他 Angular 服务，您可以避免引用窗口、文档和其他 DOM 特定的全局变量，并使您的代码更加模块化、可移植和适合 SSR。

2.**检查你的一些包是否引用了特定于 DOM 的全局变量，如窗口或文档**

在 Angular 中，您可以使用 Augury Chrome 扩展来检查模块的依赖关系，并检查它们是否引用了窗口、文档或其他特定于 DOM 的全局变量。

要使用 Augury，首先从 Chrome 网络商店安装:[https://Chrome . Google . com/Web Store/detail/Augury/elgalmkoelokbchhkhackoklkejnhcd](https://chrome.google.com/webstore/detail/augury/elgalmkoelokbchhkhacckoklkejnhcd)

一旦安装了占卜，在谷歌浏览器中打开你的 Angular 应用，点击工具栏中的占卜图标，打开占卜面板。在占卜面板中，单击依赖关系图选项卡，查看应用程序模块之间的依赖关系图。

要检查某个特定的模块，请在图中单击它的节点，并查找引用窗口、文档或其他 DOM 特定全局变量的依赖项。如果您看到任何这样的依赖，这意味着模块正在使用这些全局变量，您可能要考虑使用角度服务来代替。