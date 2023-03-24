# 为您的离子应用添加多种组件

> 原文：<https://levelup.gitconnected.com/adding-multiple-components-to-your-ionic-application-8ba5d5523aa7>

该组件的用例包括创建需要可重用的代码部分。例如，一个公司标志，或者一个日期，或者可能是一个需要独立于应用程序的其余部分运行的过程。

![](img/1dfcd88bb5f11db04da2aea4f8d477ea.png)

照片由[巨浪 926](https://unsplash.com/@billow926?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/twins?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

我想生成 2 个组件，将做到这一点，即显示一个标志和日期。

如果你想在你的应用程序中添加组件，这里有一个关于如何做的快速指南。

请注意，将用于构建组件的 Ionic 版本为 **Ionic CLI 版本为 6.3.0，构建于 Ionic 版本 5** 。

我假设你有一个离子项目准备好了，因为我不想深究如何建立一个。如果您正在考虑建立一个 Ionic 项目，您可以访问以下网站:

[](http://www.ionicframework.com) [## Ionic -跨平台移动应用开发

### Ionic 是面向 web 开发者的应用开发平台。构建令人惊叹的跨平台移动、web 和桌面应用程序…

www.ionicframework.com](http://www.ionicframework.com) 

一旦你建立了一个 Ionic 项目，使用命令行终端导航到你的项目文件夹。

## **生成组件**

要生成组件，请在项目文件夹的命令行终端中运行以下两个命令:

```
ionic g component components/logo
ionic g component components/date
```

上述命令将在您的应用程序文件夹中生成一个组件/徽标文件夹和一个组件/日期文件夹。

下一步是构建显示徽标和日期的组件。

## **构建您的组件**

在你的 logo.component.html 文件中，删除里面的所有内容，并添加以下代码来显示公司的徽标:

```
<img src="company_logo.png">**Goes without saying, don't forget to change company_logo.png to your company file
```

下一步是构建日期组件。这里首先需要做的是在 date.component.ts 中创建短日期组件。

编辑 date.component.ts 并添加以下代码:

```
dateToday:any;ngOnInit() { let date_today=new Date(); this.dateToday=date_today.toLocaleDateString();}
```

在您的 date.component.html 文件中，删除里面的所有内容，并添加以下代码来显示我们在 ts 文件中设置的`dateToday`变量:

```
{{dateToday}}
```

## **生成模块**

通过在命令行中键入以下内容，为您的组件生成一个模块:

```
ionic g module components/component
```

## **将徽标组件和日期组件放置在模块内**

下一步是将徽标组件和日期组件添加到模块的组件中，该组件是通过将它添加到新创建的 components/component.module.ts 中而创建的，添加的项目以粗体显示:

```
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';

**import { LogoComponent } from '../logo/logo.component';
import { DateComponent } from '../date/date.component';**

@NgModule({
  declarations: [**LogoComponent,DateComponent**],
  imports: [
    CommonModule
  ],
  exports:[**LogoComponent,DateComponent**],
  **entryComponents:[LogoComponent,DateComponent]**
})
export class ComponentModule { }
```

## **将 component.module.ts 导入您的页面**

通过将以下代码添加到特定的页面模块文件中，将新创建的 component.module.ts 文件导入到您的页面中。在本例中，我将把这段代码添加到 home.page.module.ts 中。我所添加的代码是 **bold** :

```
import { NgModule} from '@angular/core';
import { CommonModule } from '@angular/common';
import { IonicModule } from '@ionic/angular';
import { FormsModule } from '@angular/forms';
import { RouterModule } from '@angular/router';

import { HomePage } from './home.page';
**import { ComponentModule } from '../components/component/component.module';**

@NgModule({
  imports: [
    CommonModule,
    FormsModule,
    IonicModule,
    RouterModule.forChild([
      {
        path: '',
        component: HomePage
      }
    ]),
 **ComponentModule,**    PipeModule
  ],
  declarations: [HomePage]
})
export class HomePageModule {}
```

## **在 Ionic 应用程序中使用徽标和日期组件**

最后，通过添加以下代码片段，您可以在页面中使用徽标和日期组件。在本例中，由于我已经将代码添加到 home.page.module.ts 中，所以我将把下面的代码片段添加到我的 home.page.module.ts 中。我将把它放在内容的最顶端:

```
<app-logo></app-logo>
<app-date></app-date>
```

给你。这就是向您的应用程序添加组件的全部工作。通过将模块文件添加到页面的 module.ts 文件中，然后将标记添加到该特定页面的任意位置，可以重用这些组件。

对此的扩展是添加参数，您可以在组件中插入文本，这很方便，这将在另一篇文章中介绍。

快乐尝试！