# 在 Angular 中进行 API 调用，并在组件中显示数据

> 原文：<https://levelup.gitconnected.com/angular-tutorial-part-ii-application-8c30566e7c5d>

![](img/c9008ec396a1ebda428807b81aa237e8.png)

这是两部分教程的第二部分。 [*第一部分*](https://medium.com/@alexanderegiannini/angular-tutorial-part-i-setup-9a367f77ac2e) *覆盖设置。第二部分演示了如何构建一个服务类来从互联网上检索数据，以及一个组件来显示数据。*

# **第 0 步:了解琐碎的 App**

现在我们已经使用命令行界面生成了简单的 web 应用程序，是时候构建一个应用程序来进行 API 调用了。

ng new 命令生成了 200 多兆字节的数据和 700 多个依赖项。这些文件提供了广泛的功能，包括单元测试。首先，找到 index.html 的文件。

Index.html 的身体里会有一个标签叫做 <app-root></app-root> 。这个标签引用了一个名为 app 的组件，并将显示包含在其组件文件中的任何信息。

如果你愿意，在页面顶部的标签中粘贴一个链接到你最喜欢的 css 框架。现在，要查看<app-root>的内容，导航到 src 文件夹内的 app 文件夹。</app-root>

# **第一步:修改<APP-ROOT>**

<app-root>组件由四个文件组成:app.component.ts、app.component.css、app.spec.ts 和 app.component.ts。该文件夹还包含一个名为 app.module 的附加文件，该文件将用于构建服务类。app.spec.ts 是用于测试的，所以现在只关注另外三个。</app-root>

打开 app.component.ts、app.component.ts 和 app.component.css。html 文件，删除标签下的所有内容。接下来导航到。ts 文件，您会注意到其中定义了一个名为“title”的变量。

通常，在。可以在 html 文件中通过用双括号将变量括起来来访问 ts 文件。这被称为“插值”。现在，在。ts 文件，并改成类似“我的第一个 Angular App”的东西。现在，观察。html 文件，该文件应在。ts 文件。

最后，打开 app.component.css，将 h1 标记的颜色修改为:

```
h1{ color: red;}
```

保存每个文件，并通过在 localhost:4200 重新加载浏览器来确认您的更改。如果一切都加载正确，那么是时候创建一个服务类来为您的应用程序检索数据了。

# **步骤 2:构建一个服务类来进行 API 调用**

最佳实践是使用 CLI 生成服务类。但是，保持 ngserve 运行是一个好主意，这样您就可以检查您的应用程序。因此，打开第二个命令行窗口并键入:

```
ng generate service api
```

这将在您的 app 文件夹中创建两个新文件:api.service.ts 和 api.service.spec.ts。

为了进行 API 调用，这个服务类需要与互联网通信。为此，我们需要将 HttpClientModule 和 HttpClient 导入到 into app.module 和 service 类中。有关这些模块的更多信息，请访问:

[https://angular.io/api/common/http/HttpClientModule](https://angular.io/api/common/http/HttpClientModule)和[https://angular.io/guide/http](https://angular.io/guide/http)。

首先，打开 App.module.ts 并将以下内容粘贴到页面顶部。

```
import { HttpClientModule } from ‘@angular/common/http’;
```

接下来，将 HttpclientModule 包含在 imports 数组中。

```
imports: [ BrowserModule, HttpClientModule]
```

现在，导航到新生成的 service.ts 文件。该文件将用于与互联网通信，因此通过在文件顶部添加以下语句来导入 http 客户端。

```
import { HttpClient } from ‘@angular/common/http’;
```

然后，通过将它添加到构造函数中，使它在 ApiService 类中可访问。

```
constructor (private http:HttpClient) {}
```

这将创建一个名为 http 的 HttpClient 实例，任何 ApiService 对象都可以访问该实例以通过 internet 进行通信。下一步是找到一些要检索的数据。为此，我们可以使用任何 API 或数据库，但对于本教程，我将使用免费的“国家”API 来检索他们数据库中每个国家的基本数据点(更多信息可在 https://restcountries.eu/[找到)。](https://restcountries.eu/)

将下面的函数添加到 ApiService 类中(就在构造函数下面)。

```
getCountries(){return this.http.get('https://restcountries.eu/rest/v2/all'); 
}
```

现在，服务类已经完成，可以被您的应用程序调用了。

# **第三步:修改< APP-ROOT >接收数据**

要使用新的服务类，必须在其中一个组件中实例化它。首先，我们将在<app-root>中完成，然后创建一个额外的组件来保存数据。</app-root>

首先，转到 app.component.ts 文件，通过在现有导入调用上粘贴以下语句来导入 service 类和 OnInit 类。

```
import { Component, OnInit } from ‘@angular/core’;import {ApiService} from ‘./api.service’;
```

现在，向 app 组件类添加一个构造函数以及一个名为 countryData 的变量(设置为 null)。这将创建一个名为 API service 的 API 服务实例和一个名为 countryData 的变量来显示您的数据。

```
countryData = null;constructor(private api:ApiService) {}
```

下一步是添加一个名为 ngInit 的函数，它将调用服务类中的 getCountries 函数。

```
ngOnInit() { this.api.getCountries().subscribe((data)=>{ this.countryData = data;});
}
```

如您所见，该函数使用了名为 subscribe 的东西。这被称为可观察的，类似于 Javascript 中的承诺。欲了解更多关于可观测量的信息，请参见[https://angular.io/guide/observables](https://angular.io/guide/observables)

# **第四步:修改< APP-ROOT >显示数据**

既然数据已经被引入到组件中，就必须显示它。

要显示<app-root>中的数据，您需要迭代从服务类中检索的数据。为此，需要一个*ngFor 来循环 countryData 变量中的对象。ngFor 是一个用于操作 DOM 的结构指令。它们包括 ngFor、ngIf 和 ngSwitch。有关 Angular 结构指令的更多信息，请参见 https://angular.io/guide/structural-directives 的文档。</app-root>

将下面的代码粘贴到

# 标签中，但是在您的欢迎消息下面:

```
<h1>
     Welcome to {{ title }}!
     <p *ngFor='let country of countryData'>
        {{country.name}}
        Population: {{country.population}}
        Location: {{country.latlng}}
     </p>
</h1>
```

这将访问由国家 api 发送的每个国家的名称、人口和位置。如果您使用不同的 api，请使用您的相关键。只需确保 countryData 与您在 ts 文件中定义的变量名相同。

现在，保存您的所有文件，并在您的浏览器 localhost:4200 中检查应用程序。它应该以与标题相同的颜色显示您的数据。这可能看起来很奇怪，但稍后将用于演示 css 的范围。现在，是时候创建一个新组件来处理数据了。

# **第五步:创建一个新组件**

Angular 使用组件来划分应用程序中的代码段。组件通过标签访问，并且可以相互嵌套。命令行界面使得生成组件变得容易。只需导航到您的 Angular 文件夹并键入:

```
ng generate component data
```

其中数据是组件的名称。这将向 app.modules 注册新组件，并生成四个不同的文件，这些文件现在位于 app 文件夹中名为 data 的文件夹中。虽然 CLI 为您完成了所有这些工作，但理解发生了什么是很重要的，因为组件可以在不同的应用程序中重用，并且您应该能够手动连接它们。CLI 在 app.module.ts 的顶部添加一条 import 语句，然后在声明中列出 is:

```
...
import { DataComponent } from ‘./data/data.component’;[@NgModule](http://twitter.com/NgModule)({
 declarations: [
 AppComponent,
 DataComponent
 ],
...
```

所有组件文件都在 src 文件夹中的一个新文件夹中。

现在，导航到新的“数据”文件，就像之前一样，忽略测试文件并打开 data.component.ts、data.component.ts 和 data.component.css

首先，观察。ts 文件。在组件装饰器中，注意这一行:

```
selector : ‘app-data’
```

这是用于访问组件内容的标记的名称。它将在调用<app-root>中的组件时使用。首先，您需要在。ts 文件。它应该看起来像:</app-root>

```
import { Component, OnInit, Input } from ‘@angular/core’;
```

这允许您从父组件接收数据。现在，要指定变量名，请将以下代码添加到 DataComponent 类中:

```
@Input() country // Capitalize Input!
```

这告诉您的组件在生成名为 country 的变量时查找它。现在是嵌套组件的时候了。返回到应用程序根目录。html 文件，app.component.html。用以下代码替换

标签中的代码:

```
<p>
    <app-data *ngFor= 'let country of countryData' app-data [country]="country">
    </app-data>
  </p>
```

这将为对象 countryData 中的每个项目创建一个新的 app-data 组件实例。对于每个实例，它将数据对象分配给指定的变量名(括号指定一个对象，如果传递一个字符串，它们是不必要的)。这个功能是通过@Input 声明实现的。如果使用不同的变量名，请记住括号中的输入变量“app-data [country]”必须与。ts 文件。

保存文件并加载应用程序。它应该显示新的组件默认消息'数据工程'，为每个国家在他们的数据库。

要显示国家数据，请导航回您的数据组件文件夹。打开 data.component.html 并将代码粘贴到文件中。它应该看起来像:

```
<p> {{country.name}} Population: {{country.population}} Location: {{country.latlng}}</p>
```

这将调用 data.component.ts 文件中@Input 中定义的 country 变量。该应用程序现在应该显示与标题相同颜色的所有国家名称。(这是因为它在

# 标签中，并且从父组件继承 css 信息。)

要更改颜色，请转到。css 文件并更改字体大小和颜色，以观察 css 文件的范围如何应用于其组件:

```
p{ color: green; font-size: 14px;}
```

现在保存一切，并确保它正确加载。应用程序现在应该以红色显示欢迎消息，以绿色显示所有的国家名称。

# **结论**

本演练旨在演示使其成为当今最流行的 web 框架之一的一些基本构造块。为了更全面地了解 Angular，我建议学习一下测试、路由和表单。