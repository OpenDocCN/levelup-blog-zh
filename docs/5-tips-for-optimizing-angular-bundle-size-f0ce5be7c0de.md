# 优化角度束尺寸的 5 个技巧

> 原文：<https://levelup.gitconnected.com/5-tips-for-optimizing-angular-bundle-size-f0ce5be7c0de>

# 介绍

欢迎阅读我们关于优化角度束大小的文章！如果您是 Angular 开发人员，您会知道包的大小对应用程序的性能有很大的影响。包越大，应用程序加载和运行的时间就越长。

这就是为什么关注包的大小并采取措施最小化它是很重要的。在这篇文章中，我们将分享 5 个技巧来减少角度束的大小并提高应用程序的性能。

![](img/7f71e858d0719d3ad854b3676ec569ab.png)

我们将从仔细研究代码分割开始，这是一种将代码分成可以按需加载的小块的技术。我们还将讨论树抖动，这是一种从您的包中删除不必要的代码的方法。

接下来，我们将讨论优化依赖关系的重要性，以及选择正确的依赖关系如何有助于减少包的大小。我们还将讨论 bundler 优化器在最小化包大小方面的作用，以及如何根据您的需求选择合适的优化器。

最后，我们将讨论聚合填充对束尺寸的影响，以及如何最大限度地减少它们的使用以减小束尺寸。

到本文结束时，您将对如何优化角度束大小和提高应用程序的性能有一个坚实的理解。我们开始吧！

# 技巧 1:使用代码分割策略

代码分割是一种将代码分成可以按需加载的小块的技术。这对于角度应用尤其有用，由于其模块化架构，这些应用可能具有较大的束尺寸。

代码分割的工作原理是这样的:您可以使用 Webpack 这样的工具将代码分割成更小的块，而不是将所有代码捆绑到一个文件中。然后可以根据需要加载这些块，而不是一次全部加载。这可以显著减少应用程序的初始加载时间，尤其是当您有大量代码或大量依赖项时。

有几种方法可以在 Angular 中实现代码拆分。一种选择是使用角度路由器来延迟加载模块。这意味着只有当用户导航到使用该模块的路线时，才会加载该模块及其依赖项。这是减少包大小的一个很好的方法，特别是如果您有一个包含许多路由和模块的大型应用程序。

另一个选择是使用角度延迟加载特性，它允许您延迟加载组件及其依赖项。对于优化应用程序的性能来说，这是一项非常有用的技术，尤其是当您有很多组件并不是在每个页面上都要用到的时候。

要在 Angular 应用程序中实现代码分割，您需要使用 Webpack 这样的捆绑器。Webpack 内置了对代码分割的支持，有几个插件和工具可以帮助您设置它。

## 示例 Webpack 配置

首先，您需要安装 Webpack 和必要的依赖项，比如`@ngtools/webpack`插件:

```
npm install --save-dev webpack webpack-cli @ngtools/webpack
```

接下来，您需要配置 Webpack 来使用代码分割。下面是一个 Webpack 配置文件的例子，它使用`SplitChunksPlugin`设置代码分割:

```
const path = require('path');
const { AngularCompilerPlugin } = require('@ngtools/webpack');
const { SplitChunksPlugin } = require('webpack').optimize;

module.exports = {
  // Other Webpack configuration options...
  optimization: {
    splitChunks: {
      chunks: 'all',
      minSize: 30000,
      maxSize: 0,
      minChunks: 1,
      maxAsyncRequests: 5,
      maxInitialRequests: 3,
      automaticNameDelimiter: '~',
      name: true,
      cacheGroups: {
        vendors: {
          test: /[\\/]node_modules[\\/]/,
          priority: -10
        },
        default: {
          minChunks: 2,
          priority: -20,
          reuseExistingChunk: true
        }
      }
    }
  },
  plugins: [
    // Other Webpack plugins...
    new AngularCompilerPlugin({
      // AngularCompilerPlugin options...
      // This option specifies the Angular module to be loaded on demand
      entryModule: path.join(__dirname, 'src/app/app.module#AppModule')
    })
  ]
};
```

这个 Webpack 配置使用`SplitChunksPlugin`设置代码分割，它将您的代码分成更小的块，并按需加载。您可以通过修改`splitChunks`选项来定制代码分割策略，比如最小块大小和将代码分割成块的标准。

要在 Angular 应用程序中使用代码分割，您需要将`loadChildren`属性添加到您的 routes 中，并指定应该按需加载的块的路径。以下是一个带有代码分割的路由配置示例:

```
const routes: Routes = [
  {
    path: 'dashboard',
    loadChildren: () => import('./dashboard/dashboard.module').then(m => m.DashboardModule)
  },
  {
    path: 'users',
    loadChildren: () => import('./users/users.module').then(m => m.UsersModule)
  }
  // Other routes...
];
```

在本例中，`dashboard`和`users`路线被配置为使用代码分割。当用户导航到这些路线之一时，相应的块将按需加载。这有助于减少初始包的大小，提高应用程序的性能。

您还可以通过在`Routes`数组中指定`loadChildren`属性，将代码拆分与角度延迟加载结合使用。下面是一个带有延迟加载和代码分割的路由配置示例:

```
const routes: Routes = [
  {
    path: 'dashboard',
    loadChildren: () => import('./dashboard/dashboard.module').then(m => m.DashboardModule)
  },
  {
    path: 'users',
    loadChildren: () => import('./users/users.module').then(m => m.UsersModule)
  }
  // Other routes...
];
```

在这个例子中，`dashboard`和`users`路由被配置为使用代码分割和延迟加载。当用户导航到这些路线之一时，相应的块将按需加载，并且块中的组件和依赖项将仅在需要时加载。这有助于进一步减小初始包的大小，并提高应用程序的性能。

需要注意的是，代码分割和延迟加载会增加应用程序的复杂性，而且它们并不适合所有的项目。仔细考虑权衡并决定这些技术是否适合您的特定需求是一个好主意。

在决定是否在 Angular 应用程序中使用代码分割和延迟加载时，有几个因素需要考虑。以下是一些需要记住的事情:

*   包大小:如果您的应用程序有一个大的包大小，代码分割和延迟加载可以帮助减少初始加载时间和提高性能。
*   用户体验:通过减少初始加载时间，代码分割和延迟加载可以改善用户体验，特别是对于使用较慢连接或设备的用户。
*   开发时间:实现代码分割和延迟加载可能需要额外的开发时间，因为您需要设置必要的依赖项并配置您的应用程序来使用这些技术。
*   维护:代码分割和延迟加载会增加应用程序的复杂性，从而增加维护和调试的难度。

在决定是否在您的 Angular 应用程序中使用这些技术之前，仔细权衡代码分割和延迟加载的利弊是很重要的。在某些情况下，减少包大小和提高性能的好处可能超过额外的开发和维护成本。在其他情况下，增加的复杂性可能是不合理的，以其他方式优化您的应用程序可能更有效。

总之，代码分割和延迟加载是优化 Angular 应用程序性能的强大工具，但它们并不适合所有项目。仔细考虑权衡并决定这些技术是否适合您的特定需求是很重要的。

# 技巧 2:使用摇树方法

树抖动是一种通过消除未使用的代码来减少包大小的技术。通过只包含实际使用的代码，您可以显著减小包的大小并提高应用程序的性能。以下是在角度模式下实现树抖动的一些技巧:

*   使用支持树抖动的捆绑器:要在 Angular 中使用树抖动，您需要使用像 Webpack 这样支持树抖动的捆绑器。您还需要配置您的 bundler 来对您的应用程序代码执行树抖动。
*   谨慎使用`forRoot()`方法:当导入一个包含`forRoot()`方法的模块时，确保只在应用程序的根模块中调用一次。这将有助于确保树抖动可以从导入的模块中删除不必要的代码。
*   在构建应用程序时使用`--prod`标志:在为生产构建 Angular 应用程序时，一定要使用`--prod`标志来启用树抖动和其他优化技术。
*   避免使用通配符导入:通配符导入，如`import * as myModule from './my-module'`，可以阻止树抖动有效工作。不要使用通配符导入，考虑使用命名导入，比如`import { myFunction } from './my-module'`。
*   使用`@angular/cli` `build`和`test`命令:`@angular/cli` `build`和`test`命令可以通过自动设置必要的编译器选项来帮助优化您的树抖动应用程序。

## 示例树摇动配置

```
const path = require('path');
const { AngularCompilerPlugin } = require('@ngtools/webpack');

module.exports = {
  // Other Webpack configuration options...
  optimization: {
    usedExports: true
  },
  plugins: [
    // Other Webpack plugins...
    new AngularCompilerPlugin({
      // AngularCompilerPlugin options...
      // This option enables tree shaking for the imported modules
      tsConfigPath: path.join(__dirname, 'tsconfig.json'),
      skipCodeGeneration: true,
      sourceMap: true,
      compilerOptions: {
        enableIvy: true,
        fullTemplateTypeCheck: true,
        strictInjectionParameters: true,
        enableTreeShaking: true
      }
    })
  ]
};
```

在本例中，`AngularCompilerPlugin`配置为使用`enableTreeShaking`选项启用树摇动。`optimization.usedExports`选项也被设置为`true`,以确保只有使用过的导出包含在捆绑包中。

要使用这个配置，您需要在您的项目中包含`@ngtools/webpack`包，并配置您的 Webpack 构建来使用`AngularCompilerPlugin`。您还需要将`tsConfigPath`选项设置为 TypeScript 配置文件的路径，并将`skipCodeGeneration`和`sourceMap`选项设置为适合您的项目。

有了这个配置，当您为生产构建应用程序时，Webpack 将对您的 Angular 应用程序代码执行树抖动。这有助于减小包的大小，提高应用程序的性能。

需要注意的是，这只是一个如何在 Angular 应用中实现树抖动的例子。根据您的具体需求和要求，在您的应用程序中还有许多其他方式来配置树抖动。

# 技巧 3:优化你的依赖关系

影响包大小的一个关键因素是应用程序中包含的依赖项的数量和大小。通过优化您的依赖项，您可以帮助减少您的包的大小并提高您的应用程序的性能。以下是一些在 Angular 中优化依赖性的技巧:

*   使用轻量级库:在为应用程序选择库和依赖项时，寻找提供所需功能的轻量级选项，而不添加大量不必要的代码。例如，您可以使用一个更小、更集中的库，比如 Bamburgh UI，它只提供您需要的组件，而不是使用像 Material 这样的全功能 UI 库。
*   使用依赖项分析器工具:有几种工具可以帮助您分析应用程序中的依赖项，并确定可以优化或删除不必要的依赖项的区域。例如，Webpack bundle Analyzer 插件可以向您展示您的 Bundle 的可视化，并突出显示最大的依赖项，因此您可以看到哪些库对您的 Bundle 的大小贡献最大。
*   考虑使用代码分割和延迟加载方法:通过将代码分割成更小、更集中的代码块，并且只延迟加载需要的代码，您可以帮助减少代码包的大小并提高应用程序的性能。
*   使用`ng build --prod`命令:在为生产构建应用程序时，一定要使用`ng build --prod`命令，它可以帮助优化您的依赖关系，并从您的包中删除不必要的代码。

通过遵循这些提示并优化您的依赖项，您可以帮助减少您的包的大小并提高您的 Angular 应用程序的性能。

总之，优化依赖关系是减少包的大小和提高 Angular 应用程序性能的重要一步。通过选择轻量级库、使用依赖项分析器工具以及考虑代码分割和延迟加载方法，您可以帮助确保您的应用程序尽可能高效。

# 技巧 4:使用捆绑器优化器

bundler optimizer 是一种工具，可以分析您的应用程序代码并针对生产进行优化，有助于减少您的包的大小并提高您的应用程序的性能。Angular 有几个可用的 bundler 优化器，每个都有自己的一套功能和优点。以下是选择和使用捆绑器优化器的一些提示:

*   选择一个非常适合您需求的捆绑器优化器:有许多捆绑器优化器可用，每一个都有自己的特性和功能。选择 bundler 优化器时，请考虑应用程序的大小和复杂性，以及您的特定性能和优化目标。
*   使用与您的工作流程集成的捆绑器优化器:选择一个能够无缝融入您的工作流程且易于使用的捆绑器优化器非常重要。寻找一个有好的文档并受到社区良好支持的工具。
*   正确配置 bundler 优化器:要充分利用 bundler 优化器，请确保正确配置它。这可能涉及到设置选项或在配置文件中添加插件。请务必仔细阅读所选 bundler optimizer 的文档，以确保正确使用它。
*   在构建应用程序时使用`--prod`标志:在为生产构建 Angular 应用程序时，确保使用`--prod`标志来启用 bundler 优化和其他优化技术。

通过遵循这些提示并使用捆绑器优化器，您可以帮助减少捆绑器的大小，并提高角度应用程序的性能。

## 示例 Webpack 捆绑包分析器插件配置

```
npm install --save-dev webpack-bundle-analyzer
```

接下来，更新 Webpack 配置文件以包含插件:

```
const { BundleAnalyzerPlugin } = require('webpack-bundle-analyzer');

module.exports = {
  // Other Webpack configuration options...
  plugins: [
    // Other Webpack plugins...
    new BundleAnalyzerPlugin()
  ]
};
```

最后，构建您的 Angular 应用程序并运行 bundle analyzer:

```
ng build --prod
webpack-bundle-analyzer dist/<your-project-name>/stats.json
```

这将在您的默认浏览器中打开 Webpack Bundle Analyzer，显示您的 Bundle 的可视化表示，并突出显示最大的依赖项。您可以使用这些信息来确定可以优化或删除不必要的依赖项的区域，以减小包的大小。

需要注意的是，这只是在 Angular 应用程序中使用捆绑器优化器的一个例子。还有许多其他可用的 bundler 优化器，每一个都有自己的特性和功能。请确保选择最适合您特定需求和要求的工具。

# 提示#5:最小化有角度的多边形填充

聚合填充是为并非所有现代浏览器都提供的功能提供支持的库。虽然 polyfills 有助于确保您的应用程序在各种平台上运行，但它们也会增加包的大小并影响性能。以下是一些在角度上最小化聚合填充的提示:

*   使用有针对性的聚合填充策略:不要在应用程序中包含所有可能的聚合填充，可以考虑使用有针对性的方法，只包含特定目标平台所需的聚合填充。这有助于减小包的大小，提高应用程序的性能。
*   使用`ng add`命令添加聚合填充:向角度应用添加聚合填充时，考虑使用`ng add`命令，该命令可帮助您选择和配置应用所需的聚合填充。
*   使用`browserlist`配置文件:`browserlist`配置文件允许您为您的应用指定目标平台，并相应地配置必要的 polyfills。通过使用`browserlist`文件，可以确保只包含目标平台所需的聚合填充。
*   使用`@angular/cli` `build`和`test`命令:`@angular/cli` `build`和`test`命令可以通过自动设置必要的编译器选项来帮助优化多填充应用程序。

通过遵循这些提示并最大限度地减少聚合填充的使用，您可以帮助减小束的大小并提高角度应用的性能。

总之，最大限度地减少多孔填料的使用是减小束尺寸和提高角度应用性能的重要一步。通过使用有针对性的多填充策略并利用像`ng add`和`browserlist`命令这样的工具，您可以帮助确保您的应用程序尽可能高效。

## 样本配置

首先，在 Angular 项目的根目录下创建一个`.browserlistrc`文件，并为您的应用程序指定目标平台:

```
# This configuration targets the last 2 versions of each browser
last 2 versions
```

接下来，更新您的`tsconfig.app.json`文件以包含`browserlist`配置:

```
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "outDir": "./out-tsc/app",
    "types": [
      "node"
    ],
    "downlevelIteration": true,
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
    "importHelpers": true,
    "baseUrl": "src",
    "paths": {
      "app/*": [
        "app/*"
      ],
      "environments/*": [
        "environments/*"
      ]
    }
  },
  "exclude": [
    "test.ts",
    "**/*.spec.ts"
  ],
  "angularCompilerOptions": {
    "fullTemplateTypeCheck": true,
    "strictInjectionParameters": true,
    "enableIvy": true,
    "preserveWhitespaces": false,
    "buildOptimizer": true,
    "sourceMap": true,
    "polyfills": "./polyfills.ts",
    "browserTarget": "app:build:production"
  }
}
```

注意`angularCompilerOptions`部分的`"polyfills": "./polyfills.ts"`和`"browserTarget": "app:build:production"`选项。这些选项告诉 Angular 使用`polyfills.ts`文件进行聚合填充，并在构建应用程序时以指定平台为目标。

最后，使用`ng build --prod`命令构建您的角度应用程序。这将使用`browserlist`配置为指定的目标平台优化您的应用程序，并仅包括必要的聚合填充。

# 结论

总之，优化角度束大小是提高应用程序性能的重要一步。通过遵循本文中概述的技巧，您可以帮助减少束的大小，并提高角度应用的速度和效率。

这篇文章的一些要点包括:

*   使用代码分割策略将您的应用程序分成更小、更易管理的块
*   使用树摇动方法从您的包中删除不必要的代码
*   优化依赖项，删除不必要或多余的库
*   使用捆绑优化器来分析和优化您的捆绑包
*   尽量减少多孔填料的使用，以减小管束尺寸

通过遵循这些最佳实践，您可以帮助确保您的 Angular 应用程序尽可能高效和高性能。

总的来说，优化角度束大小是提高应用性能和效率的重要一步。通过遵循本文中概述的技巧，您可以帮助确保您的应用程序以最佳状态运行。

**不要错过我即将推出的内容和技术指南:**

[](https://medium.com/@nicchong/subscribe) [## 每当 Nic Chong 发布时收到电子邮件。

### 每当 Nic Chong 发布时收到电子邮件。通过注册，您将创建一个中型帐户，如果您还没有…

medium.com](https://medium.com/@nicchong/subscribe) 

如果你有什么问题，我在这里帮忙，在评论区等你:)