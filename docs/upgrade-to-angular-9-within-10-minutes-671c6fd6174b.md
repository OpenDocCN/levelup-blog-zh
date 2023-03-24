# 10 分钟内升级到 Angular 9

> 原文：<https://levelup.gitconnected.com/upgrade-to-angular-9-within-10-minutes-671c6fd6174b>

## 现在轻松地将您现有的项目更新到 Angular 的新版本

![](img/60e2c7c4e983af721993c9b5d14a5a4f.png)

安东尼·加兰在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

又到了新的棱角版本。最新的版本再次突出了性能改进、更小的包大小和许多其他特性。此外，Ivy 渲染器现在将是默认的。

要遵循这个简短的指南，您现有的项目此时必须在版本`**8.x.x**` 上。我们使用`**9.0.0**`标签来升级我们的依赖关系。

# 自动更新

```
ng update @angular/cli @angular/core
```

[](https://medium.com/@jeroenouw/faster-testing-with-baretest-typescript-edition-5db12bd0beac) [## 使用 Baretest 进行更快的类型脚本测试

### 现在就在 TypeScript 项目中使用这个极简的 JavaScript 测试运行器吧

medium.com](https://medium.com/@jeroenouw/faster-testing-with-baretest-typescript-edition-5db12bd0beac) 

# 手动更新

## 角度 CLI(全局)

```
npm install @angular/cli@9.0.0 -g
```

## 角度从属关系(局部)

```
npm install @angular/animations@9.0.0 @angular/common@9.0.0 @angular/compiler@9.0.0 @angular/core@9.0.0 @angular/forms@9.0.0 @angular/platform-browser@9.0.0 @angular/platform-browser-dynamic@9.0.0 @angular/platform-server@9.0.0 @angular/router@9.0.0
```

可选；如果使用材料设计:

```
npm install @angular/cdk@9.0.0 @angular/material@9.0.0
```

## 角度偏差从属关系(局部)

```
npm install @angular-devkit/build-angular@0.900.1 @angular/compiler-cli@9.0.0 @angular/cli@9.0.0 @angular/language-service@9.0.0 --save-dev
```

## 所有其他相关依赖项(本地)

```
npm install core-js@latest zone.js@latest rxjs@latest
```

## 和相关的开发依赖项(本地)

```
npm install @types/jasmine@latest @types/node@latest codelyzer@latest karma@latest karma-chrome-launcher@latest karma-cli@latest karma-jasmine@latest karma-jasmine-html-reporter@latest jasmine-core@latest jasmine-spec-reporter@latest protractor@latest tslint@latest webpack@latest rxjs-tslint@latest --save-dev
```

## TypeScript + tslib(本地)

您应该安装了 TypeScript 的 3.6+版本(所以 3.7 也是允许的)。不再支持 3.4 和 3.5 版。通过将下面的代码添加到您的`package.json`的底部，添加 TypeScript 和 tslib 作为对等依赖项。

```
"peerDependencies": {
  "typescript": "3.6.4",
  "tslib": "1.10.0"
}
```

如果这里出现错误，请移除`package-lock.json`和`node_modules`并运行`npm install`。

# 重要的是要知道

## 常春藤渲染器

可能会出现一些与 Ivy 相关的问题。关于您可能在项目中看到的这些变化的信息可以在[这里](https://next.angular.io/guide/ivy-compatibility#changes-you-may-see)和[这里](https://next.angular.io/guide/ivy-compatibility#less-common-changes)找到。

## 惰性装载

由于不赞成，现在必须使用动态导入而不是字符串。

```
// Before
loadChildren: './path/lazy.module#LazyModule'// Now
loadChildren: () => import('./path/lazy.module').then(m => m.LazyModule)
```

## 平台网络工作者

`@angular/platform-webworker`包将被弃用。

# 很高兴知道

## 不推荐使用的 API

通过检查[这个](https://angular.io/guide/deprecations#index)列表，你可以知道哪些 API 在 Angular 9 中变得不可用/不推荐使用。

## 角度兼容性编译器(ngcc)

通过在你的`package.json`中添加下面的`postinstall`脚本，你可以确保你的`node_modules`与 Ivy 渲染器兼容。它将在`npm install`之后运行。

```
"scripts": {
   "postinstall": "ngcc"
}
```

## 常春藤

如果您想关闭新的 Ivy 渲染器，请在`tsconfig.json`中添加以下内容:

```
"angularCompilerOptions": {
   "enableIvy": false
}
```

## 材料设计

> *“与其从* `*@angular/material*` *导入，不如从具体的组件深度导入。如*`*@angular/material/button*`

# *角度变化记录*

*在这里可以找到[的变更和弃用。](https://github.com/angular/angular/blob/master/CHANGELOG.md)*

## *感谢您的阅读！我的 [Github](https://github.com/jeroenouw/) 或者 [Twitter](https://twitter.com/jeroenouw) 。如果你觉得这篇文章有用，请点击👏关注 button，并考虑阅读我的其他文章:*

*[](https://itnext.io/angular-8-how-to-use-cookies-14ab3f2e93fc) [## Angular 9—如何使用 cookies

### Cookies 是一些小的信息包，可以由您的浏览器和网站临时存储/保存

itnext.io](https://itnext.io/angular-8-how-to-use-cookies-14ab3f2e93fc) [](/what-every-developer-should-be-doing-923d6ca67ea) [## 每个开发人员应该做的事情

### 作为开发人员，这四点对我帮助最大

levelup.gitconnected.com](/what-every-developer-should-be-doing-923d6ca67ea) [](/essential-tool-for-every-developer-18ce57deb37b) [## 每个开发人员的必备工具

### 节省大量编写手册文档的时间

levelup.gitconnected.com](/essential-tool-for-every-developer-18ce57deb37b) 

# 分级编码

感谢您成为我们社区的一员！ [**订阅我们的 YouTube 频道**](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1) 或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题

### 掌握编码面试的过程

技术开发](https://skilled.dev)*