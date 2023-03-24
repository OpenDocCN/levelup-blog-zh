# 构建一个角度为 10°的 QR 码生成器

> 原文：<https://levelup.gitconnected.com/build-a-qr-codes-generator-with-angular-10-d27f1b16fb80>

在本教程中，我们将学习如何使用最新的 Angular 10 版本构建一个 QR 码生成器应用程序。

首先，什么是二维码，它有什么作用？

据[维基百科](https://en.wikipedia.org/wiki/QR_code):

> *QR 码(快速响应码的缩写)是一种矩阵条形码(或二维条形码)，于 1994 年首次为日本的汽车行业设计。条形码是一种机器可读的光学标签，包含有关其所附着的物品的信息。实际上，QR 码通常包含指向网站或应用程序的定位器、标识符或跟踪器的数据。QR 码使用四种标准化编码模式(数字、字母数字、字节/二进制和汉字)来高效存储数据。*
> 
> *快速响应系统在汽车行业之外也很受欢迎，因为与标准的 UPC 条形码相比，它具有更快的可读性和更大的存储容量。应用包括产品跟踪、物品识别、时间跟踪、文档管理和一般营销*

因此，这是一种简洁高效的数据存储方式。

现在让我们通过示例来看看如何在 Angular 10 应用程序中生成二维码。

# 先决条件

在开始之前，您需要一些先决条件:

*   打字稿的基础知识。特别是熟悉面向对象的概念，如类型脚本类和装饰器。
*   装有**节点 10+** 和 **NPM 6+** 的本地开发机。Angular CLI 像当今大多数前端工具一样需要节点。你可以直接进入[官网](https://nodejs.org/downloads)的下载页面，下载适合你操作系统的二进制文件。您还可以参考特定的系统说明，了解如何使用软件包管理器安装节点。不过推荐的方法是使用[NVM](https://github.com/nvm-sh/nvm)——节点版本管理器——一个 POSIX 兼容的 bash 脚本来管理多个活动的 Node.js 版本。

**注意**:如果你不想为 Angular 开发安装一个本地环境，但是仍然想尝试本教程中的代码，你可以使用 [Stackblitz](https://stackblitz.com/) ，一个用于前端开发的在线 IDE，你可以用它来创建一个与 Angular CLI 兼容的 Angular 项目。

# 步骤 1 —安装 Angular CLI 10

在这一步，我们将[安装最新的 Angular CLI 10](https://www.ahmedbouchefra.com/install-angular-cli/) 版本(在撰写本教程时)。

[Angular CLI](https://cli.angular.io/) 是用于初始化和使用 Angular 项目的官方工具。要安装它，请打开一个新的命令行界面并运行以下命令:

```
$ npm install -g @angular/cli
```

在撰写本教程时， **angular/cli v10** 将安装在您的系统上。

# 步骤 2-创建新的 Angular 10 应用程序

现在让我们创建我们的项目。回到您的命令行界面，运行以下命令:

```
$ cd ~
$ ng new angular10qrcode
```

CLI 将询问您几个问题—如果**您想要添加角度路由？**键入 **y** 表示是，键入**表示您希望使用哪种样式表格式？**选择 **CSS** 。

接下来，导航到您的项目文件夹，并使用以下命令运行本地开发服务器:

```
$ cd angular10qrcode
$ ng serve
```

打开网络浏览器，导航至`http://localhost:4200/`地址，查看您的应用程序运行情况。

接下来，打开一个新的终端，确保导航到您的项目文件夹，并运行以下命令，使用以下命令从 npm 安装`[ngx-qrcode](https://github.com/techiediaries/ngx-qrcode)` [库](https://github.com/techiediaries/ngx-qrcode):

```
$ npm install @techiediaries/ngx-qrcode
```

接下来打开`src/app/app.module.ts`文件，在你的模块中从`@techiediaries/ngx-qrcode`导入`NgxQRCodeModule`，如下所示:

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { NgxQRCodeModule } from '@techiediaries/ngx-qrcode';
import { FormsModule } from '@angular/forms'; import { AppComponent } from './app.component';@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    FormsModule,
    NgxQRCodeModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

一旦库被导入，您可以在您的 Angular 应用程序中使用`ngx-qrcode`组件。

> *请注意，我们还导入了* `*FormsModule*` *。*

接下来，打开`src/app/app.component.ts`文件，并按如下方式更新它:

```
import { Component } from '@angular/core';
import { NgxQrcodeElementTypes, NgxQrcodeErrorCorrectionLevels } from '@techiediaries/ngx-qrcode';@Component({
  selector: 'my-app',
  templateUrl: './app.component.html',
  styleUrls: [ './app.component.css' ]
})
export class AppComponent  {
  elementType = NgxQrcodeElementTypes.URL;
  correctionLevel = NgxQrcodeErrorCorrectionLevels.HIGH;
  value = 'https://www.techiediaries.com/';
}
```

接下来，打开`src/app/app.component.html`文件并添加以下代码:

```
<ngx-qrcode
  [elementType]="elementType"
  [errorCorrectionLevel]="correctionLevel"
  [value]="value"
  cssClass="bshadow"></ngx-qrcode>
```

我们使用各种属性来配置我们的 QR 码，例如:

*   类型，
*   纠错级别，
*   价值，
*   CSS 类。

您可以从官方`[ngx-qrcode](https://www.techiediaries.com/ngx-qrcode/)` [文档](https://www.techiediaries.com/ngx-qrcode/)中找到关于这些属性和其他受支持属性的更多信息。

接下来，添加一个文本区域，用于输入要编码的值:

```
<textarea [(ngModel)] = "value"></textarea>
```

最后打开`src/styles.css`文件，添加以下样式:

```
.bshadow { display: flex;
  align-items: center;
  justify-content: center;
  filter: drop-shadow(5px 5px 5px #222222);
  opacity: .5;}textarea {
    margin-top: 15px; 
    display: block;
    margin-left: auto;
    margin-right: auto;
    width: 250px;
    opacity: .5;
}
```

这是我们应用程序的屏幕截图:

![](img/c611d0873f0f24a6542c26b1a4905a97.png)

就这样，我们完成了 Angular 10 示例，演示了如何在 Angular 应用程序中生成二维码。你可以在`**Techiediaries**`上访问我们，获得关于 Angular 和现代 web 开发实践的教程。

您可以在[https://stackblitz.com/edit/angular-ngx-qrcode-example](https://stackblitz.com/edit/angular-ngx-qrcode-example)查看我们在本文中构建的应用程序。