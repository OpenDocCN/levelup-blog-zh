# 在 Angular 中实现 TransferState 的初学者指南

> 原文：<https://levelup.gitconnected.com/a-beginners-guide-to-implementing-transferstate-in-angular-a313f3244f03>

![](img/7f71e858d0719d3ad854b3676ec569ab.png)

# 介绍

欢迎使用我们的初学者指南来实现 Angular 中的 TransferState！

如果您是 Angular 的新手，或者刚刚开始优化您的应用程序的性能，您可能想知道什么是 TransferState 以及它如何提供帮助。TransferState 是一种用于在 Angular 应用程序中的服务器和客户端之间共享数据的技术，它可以成为提高服务器端呈现(SSR)性能的强大工具。

在本指南中，我们将带您了解在 Angular 应用程序中实现 TransferState 的步骤。我们将从设置项目和配置服务器端应用程序模块开始。从这里开始，我们将研究如何在 TransferState 缓存中存储数据，以及如何在客户端检索数据。最后，我们将提供一些测试和调试 TransferState 实现的技巧。

学完本指南后，您应该对 TransferState 的工作原理以及如何使用它来提高 Angular 应用程序的性能有了很好的理解。所以，让我们开始吧！

# 先决条件

在我们深入探讨在 Angular 应用程序中实现 TransferState 的步骤之前，让我们确保我们拥有了所需的一切。

首先，对角度和服务器端渲染(SSR)有一个基本的了解是很有帮助的。如果您是这些主题的新手，那么在继续之前，您可能需要先温习一些基础知识。你可以在网上找到大量的资源来帮助你提高速度，或者你可以参考官方的 Angular 文档。

除了对 Angular 和 SSR 的基本理解之外，为了使用 TransferState，您还需要安装一些依赖项。具体来说，您需要安装@angular/platform-server 包。这个包提供了 Angular 平台的服务器端实现，这是在服务器上运行 Angular 应用程序所必需的。

要安装@angular/platform-server 包，可以使用以下命令:

```
npm install @angular/platform-server
```

一旦安装了所需的依赖项，就可以进入下一步了:设置项目。在下一节中，我们将介绍为转移状态准备项目需要做的事情。

# 步骤 1:设置项目

现在我们已经讨论了先决条件，让我们继续在 Angular 应用程序中实现 TransferState 的第一步:设置项目。

首先，您需要创建一个新的 Angular 项目。如果你不确定如何做到这一点，你可以参考官方的 Angular 文档或遵循许多可用的在线教程之一。一旦建立了新项目，就需要安装并导入所需的依赖项。

一旦安装了依赖项，您需要将它们导入到您的项目中。您可以通过将以下代码行添加到您的应用程序模块中来实现这一点:

```
import { ServerModule } from '@angular/platform-server';
```

安装并导入依赖项后，您就可以进入下一步了:配置服务器端应用程序模块。在下一节中，我们将介绍如何让您的应用程序模块为 TransferState 做好准备。

# 步骤 2:配置服务器端应用模块

既然我们已经设置了项目并安装了所需的依赖项，那么是时候进入下一步了:配置服务器端应用程序模块。

要开始，您需要将`ServerTransferStateModule`添加到应用程序模块。这个模块提供了 TransferState 的功能，这是在服务器和客户端之间传输数据所必需的。要将`ServerTransferStateModule`添加到您的应用程序模块中，您需要导入它并将其添加到应用程序模块的`imports`数组中。

这里有一个你可以如何做的例子:

```
import { ServerTransferStateModule } from '@angular/platform-server';

@NgModule({
  imports: [
    ServerTransferStateModule,
    // Other imports go here
  ],
  // Other module properties go here
})
export class AppModule { }
```

随着`ServerTransferStateModule`被导入并添加到 app 模块，您需要启用 TransferState。为此，您需要将`transferState`提供者添加到 app 模块的`providers`数组中。

这里有一个你可以如何做的例子:

```
import { TransferState } from '@angular/platform-server';

@NgModule({
  providers: [
    TransferState,
    // Other providers go here
  ],
  // Other module properties go here
})
export class AppModule { }
```

导入了`ServerTransferStateModule`并启用了 TransferState 之后，您就可以进入下一步了:在 TransferState 缓存中存储数据。在下一节中，我们将介绍如何使用 TransferState 服务在缓存中存储数据。

# 步骤 3:将数据存储在传输状态缓存中

既然我们已经设置了项目并配置了服务器端应用程序模块，那么是时候进入下一步了:将数据存储在 TransferState 缓存中。

要在 TransferState 缓存中存储数据，您需要使用`TransferState`服务。该服务提供了在缓存中存储和检索数据的方法，它可用于 Angular 应用程序的服务器端和客户端部分。

要在缓存中存储数据，可以使用`TransferState`服务的`set`方法。这个方法有两个参数:要存储的数据的键和数据本身。

下面是一个如何使用`set`方法在缓存中存储数据的例子:

```
import { TransferState } from '@angular/platform-server';

@Injectable()
export class DataService {
  constructor(private transferState: TransferState) {}

  fetchData(): Observable<any> {
    const data = this.transferState.get<any>('data');
    if (data) {
      return of(data);
    } else {
      return this.http.get<any>('/api/data').pipe(
        tap(response => this.transferState.set('data', response))
      );
    }
  }
}
```

在这个例子中，我们使用`TransferState`服务的`get`方法来检查我们想要获取的数据是否已经在缓存中。如果是，我们返回缓存的数据。如果不是，我们使用`http`服务从服务器获取数据，然后使用`set`方法将数据存储在缓存中。

您可能希望在 TransferState 缓存中存储许多类型的数据。一些例子包括:

*   API 响应
*   静态数据(如配置设置)
*   用户信息
*   图像或其他媒体资产

关键是要选择生成或检索成本很高的数据，并且应用程序的多个组件或页面可能需要这些数据。通过将这些数据存储在 TransferState 缓存中，可以减少服务器请求的数量并提高应用程序的性能。

# 步骤 4:从 TransferState 缓存中检索数据

既然我们已经介绍了如何在 TransferState 缓存中存储数据，那么让我们进入下一步:从缓存中检索数据。

要从 TransferState 缓存中检索数据，您需要使用`TransferState`服务的`get`方法。这个方法有一个参数:您想要检索的数据的键。如果在缓存中找到数据，它将被返回。如果没有找到，`get`方法将返回`null`。

下面是一个如何使用`get`方法从缓存中检索数据的例子:

```
import { TransferState } from '@angular/platform-server';

@Injectable()
export class DataService {
  constructor(private transferState: TransferState) {}

  fetchData(): Observable<any> {
    const data = this.transferState.get<any>('data');
    if (data) {
      return of(data);
    } else {
      return this.http.get<any>('/api/data').pipe(
        tap(response => this.transferState.set('data', response))
      );
    }
  }
}
```

在这个例子中，我们使用`get`方法来检查我们想要获取的数据是否已经在缓存中。如果是，我们返回缓存的数据。如果不是，我们使用`http`服务从服务器获取数据，然后使用`set`方法将数据存储在缓存中(我们在上一节中讨论过)。

与将数据存储在缓存中一样，关键是选择生成或检索成本高的数据，并且这些数据可能是应用程序的多个组件或页面所需要的。通过从 TransferState 缓存中检索这些数据，可以减少服务器请求的数量并提高应用程序的性能。

# 步骤 5:测试和调试

既然我们已经介绍了在 Angular 应用程序中实现 TransferState 的基础知识，那么是时候进入最后一步了:测试和调试。

测试和调试 TransferState 可能有点棘手，因为它涉及应用程序的服务器端和客户端部分。但是，您可以做一些事情来简化这个过程:

*   使用 Postman 或 Curl 之类的工具来测试您的 API 端点，并确保它们按预期工作。这将帮助您识别应用程序的服务器端部分的任何问题。
*   使用浏览器开发工具(如 Chrome DevTools)检查应用程序发出的网络请求。这将有助于您了解服务器和客户端之间正在传输哪些数据。
*   使用`TransferState`服务来记录从缓存中存储和检索的数据。这将有助于您了解服务器和客户端之间正在传输什么数据，还将有助于您识别 TransferState 实现中的任何问题。

以下是实现 TransferState 时要避免的一些常见陷阱:

*   不要在缓存中存储大量数据。TransferState 缓存旨在存储生成或检索成本高昂的少量数据。如果试图在缓存中存储太多数据，可能会遇到性能问题。
*   确保在数据过时时清除缓存。如果您在缓存中存储的数据不再准确，您的应用程序可能会出现意外行为。当数据变得陈旧，或者当用户从您的应用程序中注销时，请确保清除缓存。
*   不要依赖缓存来存储关键数据。虽然 TransferState 是提高性能的有用工具，但它不能代替适当的服务器端缓存。对于需要跨会话或跨多个用户持久存储的关键数据，不要依赖 TransferState 缓存。

# 结论

嗯，就这样吧！我们已经介绍了在 Angular 应用程序中实现 TransferState 需要遵循的所有步骤，从设置项目到测试和调试。

总而言之，以下是我们讨论的要点:

*   TransferState 是 Angular 提供的一项服务，允许您在服务器的缓存中存储和检索数据。
*   通过使用 TransferState，可以减少服务器请求的数量，并提高 Angular 应用程序的性能。
*   要实现 TransferState，您需要设置项目，配置服务器端应用程序模块，将数据存储在缓存中，并从缓存中检索数据。
*   在测试和调试 TransferState 实现时，一定要使用 Postman 或 Curl 之类的工具来测试 API 端点，使用浏览器开发工具来检查应用程序发出的网络请求，并使用`TransferState`服务来记录从缓存中存储和检索的数据。

我们希望本指南对您有所帮助，我们鼓励您尝试在自己的 Angular 项目中实现 TransferState。如果您有任何问题或需要进一步的帮助，请随时联系我们！

感谢您的阅读，祝您编码愉快！

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰更多内容请查看[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级达人集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)