# 使用 Angular CLI 构建 Angular 应用程序

> 原文：<https://levelup.gitconnected.com/angular-tutorial-part-i-setup-9a367f77ac2e>

![](img/cd964baa561aa45cbd99cdfb83c81b7c.png)

这个两部分的教程是 Angular 2+的一个简要介绍。第一部分演示了如何设置 Angular 并使用命令行界面生成一个简单的应用程序。 [*第二部分*](https://medium.com/@alexanderegiannini/angular-tutorial-part-ii-application-8c30566e7c5d) *涵盖组件和服务类。它不假设任何框架的知识。*

# 步骤 0:定位

Angular 是当今最流行的前端 web 框架之一。开始使用 Angular 时，了解两件事很重要。

1.  **版本(Angular 2+ vs Angular.js)**

Angular 版本可以分为 AngularJS(又名 Angular 1)和 Angular 2+两类。它们不能交叉兼容。

AngularJS 于 2010 年首次开发。它今天仍在使用，但目前正在逐步淘汰。最后一次发布是在 2018 年，支持将在 2021 年停止。

Angular 2+指 Angular 2 或更大的版本。Angular 2 于 2016 年首次发布，是 Angular 1/AngularJS 的全面重写。本教程将使用 Angular 7，但它应该适用于任何版本 2 或更高版本。

1.  **打字稿**

Angular2+使用 typescript，一种产生 JavaScript 的 transpiled 语言。它是 Javascript ECMAScript 2015 的超集。通过定义变量和返回类型，它允许更大的规范。有关 typescript 的更多信息，请访问网站[https://www.typescriptlang.org/index.html](https://www.typescriptlang.org/index.html)。

# **第一步:安装**

安装很简单。首先，您需要用 npm 安装 NODE.js。如果你还没有，请按照 node 网站提供的说明:[https://nodejs.org/en/](https://nodejs.org/en/)。

接下来，您需要安装 Typescript。这是 Angular 用来处理其 javascript 逻辑的语言。要安装，请打开您的控制台并键入:

```
npm install -g typescript
```

现在，您可以安装 Angular 命令行界面(CLI)。这是一个重要的工具，允许你托管你的应用程序，并生成项目，组件和服务类。更多信息，请参见[https://angular.io/cli](https://angular.io/cli)。要安装 Angular CLI，请键入:

```
npm install -g @angular/cli
```

# 步骤 2:使用 CLI 启动一个小应用程序

最后，是时候创建一个有角度的 App 了。首先使用 CLI 生成一个项目。为此，请键入:

```
ng new firstAngular
```

这将创建一个简单的应用程序，作为项目的起点。现在，通过导航到您的目录来测试它…

```
cd firstAngular
```

和类型:

```
ng serve
```

过一会儿，CLI 应该会加载应用程序并将其托管在端口 4200 上。要进行确认，请转到您的浏览器，在地址栏中键入 localhost:4200。这将加载您的应用程序。它将包含一些到 Angular 网站的链接，包括官方的 Angular 教程。

在处理应用程序时，保持浏览器打开，并保持 ng serve 运行。它会随着你的代码不断更新，所以你可以实时修改。这与 nodemon、括号 live 模式或 Flask 的调试模式的功能类似。

要了解服务类别、组件和显示数据，请查看第二部分，网址为[https://medium . com/@ alexandereganinini/angular-tutorial-part-II-application-8c 30566 e7c 5d](https://medium.com/@alexanderegiannini/angular-tutorial-part-ii-application-8c30566e7c5d)。