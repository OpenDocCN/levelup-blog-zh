# 角度建筑设计

> 原文：<https://levelup.gitconnected.com/angular-software-architecture-design-ddde5ca111df>

![](img/3bf7336f0d02b5b0ea4b326872f7c232.png)

照片由[达科塔·鲁斯](https://unsplash.com/@dakotaroosphotography?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 介绍

虽然客户第一眼看上去并不关心软件的架构，因为他唯一感兴趣的是功能和部署时间，但开发人员关心选择正确的内部架构，以便可以根据他的需求开发软件，这仍然符合他的利益。无论选择哪种软件架构，都会影响项目的开发**速度**和**可维护性**。

![](img/8634d47e32c719e8f978a2a1554e2f02.png)

为了决定应该在架构和质量上花费多少精力，您必须考虑开发的时间框架。如果应用程序的功能很少，并且应该在几周内完成，那么质量和架构就不是很重要。另一方面，当特性的数量很多时，架构是非常重要的，因为在某个时间点之后，低质量软件的最初快速开发将会大大减慢，而具有良好架构的软件的开发速度将保持一致。

在接下来的章节中，我将向你介绍你应该考虑的四种主要架构类型。从最低质量的架构开始，到最高质量的架构结束。

# 意大利面条怪物

![](img/ffdf5d5e65807a06ffeb120292bda769.png)

照片由 [Gianluca Gerardi](https://unsplash.com/@foodography?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

顾名思义，意大利面怪物只是没有任何边界的代码。没有特殊的项目结构，没有代码风格指南，没有重构。这意味着组件、服务、模型和特性可以混合在一起，甚至存在于同一个文件夹中。没有规则。一切都是为了快速创造一些东西，即使这意味着会很脏。

这样的代码很难阅读、扩展和维护。尽管它允许在开始时进行非常快速的开发，但在应用程序增长后不久，开发速度就会降低很多。

## 目标:

虽然，意大利面怪物听起来很可怕，但有时只是汇编代码而不考虑任何设计系统和界限是有意义的。例如，一些项目并不意味着长期存在和长期维护，在这种情况下，制作一个意大利面条怪兽是有意义的。创建原型也很有意义，因为**原型**并不意味着用于生产，它们的唯一目的是**证明一个概念**。

# 巨石柱

![](img/544be7f003fbbf4b9b2f05a45990a170.png)

[Zoltan·塔斯](https://unsplash.com/@zoltantasi?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

monolith 将所有代码保存在一个应用程序中，但通过使用良好的项目结构来划分界限。Angular 中最常见的项目结构如下:

*   **核心** *(这是主逻辑的命门所在；【主要是服务和模式)*
*   **特征** *(包含组件、管道等的每个特征的模块。)*
*   **共享** *(每个特征模块导入共享模块，共享模块包含公共组件、管道等。)*

功能被分割成模块，然后使用路由在应用程序中组装这些模块。跨许多功能模块共享的所有公共功能被分离到共享模块中，该模块由每个功能模块导入。例如，日期选择器或*表单模块*可以在共享模块中导入和导出。将组件的逻辑解耦到服务并将服务注入到组件中是有意义的，这样测试组件就更容易了。这些服务将放在核心文件夹中。

## 目标:

具有相当多特性的应用程序应该考虑实现一个具有良好项目结构的整体。

# 模量

![](img/4eb7c20b8493d36e18474f8950235a53.png)

照片由[王思然·哈德森](https://unsplash.com/@hudsoncrafted?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

从整个应用程序不是分布式的而是包含在一个应用程序中的意义上来说，modulith 类似于 monolith。关键区别在于，模块使用域驱动设计，并将特征提取到域中，这些特征由单独的角度库反映。通过使用这样的库，我们可以实施更严格的边界和更好的限制。

然后，这些角度库可以通过 npm 软件包管理器发布和使用。另一种更方便的管理这些库的方法是创建一个 monorepository，其中包含任意数量的应用程序和库。这样就不必发布任何内容，也更容易避免依赖冲突。

## 目标:

通过将特性拆分成库，具有许多特性的应用程序可以受益于在特性之间绘制更强的边界。这意味着更多的规则，如果**开发团队的规模足够大的话，这也是好事。**

# 微前端架构

![](img/7b849f8d8518f65727ca7e42251e470f.png)

由[卡尔·亨利 JR](https://unsplash.com/@workbycarl?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

一种现代的前端架构方法，随着后端微服务架构的兴起而兴起，就是所谓的微前端架构。它的主要目标是将功能分成更小的部分，可以独立部署。组件组被分成各自的应用程序，这意味着整个应用程序作为一个整体由许多独立的小应用程序组成。

在 webpack 的模块联盟的帮助下，应用程序外壳能够使用其他应用程序的块。应用程序外壳必须只知道微前端应用程序的公共域地址，然后从该公共域地址的入口点惰性加载所有块文件。

尽管这种方法非常复杂，但是独立部署的好处对于大型组织来说非常方便，因为这允许他们根据微前端将团队分开。然后可以在组织中更好地划分责任。

## 目标:

拥有**许多前端团队**的庞大组织需要**独立部署**单独的功能。例如，像亚马逊这样拥有成百上千开发人员的公司在开发一个单一应用程序时会遇到大问题，因为这意味着**合并地狱**。

# 结论

没有放之四海而皆准的架构。虽然，微前端对于庞大的组织来说可能是一个巨大的成功，但是根据需求的不同，它对于您的项目来说可能仍然是一个糟糕的选择。选择正确的架构是一门艺术，通过从过去的错误中学习，随着时间的推移，做出正确的决定会变得更加容易。

从一个在开始时不要太过火，但允许以后扩展的架构开始总是一个好主意。一个很好的例子是实现 **Facade** 设计模式，在数据层之间建立一个抽象层，这样你就可以从一个基本的基于 RxJs 主题的状态管理迁移到一个使用 NGRX 的更加增强的系统。好的架构是允许以后做决定，而不是一开始就做所有的决定。通常，提前知道所有事情是不可行的——尤其是在敏捷环境中。

最终，是您自己决定哪种架构最适合您的需求。

## 通过我的推荐链接加入 Medium:

如果你喜欢这篇博文，并想在 Medium 上无限阅读，如果你能使用我的推荐链接创建一个 Medium 订阅，我将不胜感激:

[](https://medium.com/@stefan.haas.privat/membership) [## 通过我的推荐链接-斯特凡·哈斯加入媒体

### 阅读斯特凡·哈斯的每一个故事(以及媒体上成千上万的其他作家)。您的会员费直接支持…

medium.com](https://medium.com/@stefan.haas.privat/membership) 

## 其他有趣的阅读:

[](/refactoring-angular-applications-be18a7ee65cb) [## 重构角度应用程序

### 重构是软件工程中最重要的技术之一，因为它是项目进行的唯一方式

levelup.gitconnected.com](/refactoring-angular-applications-be18a7ee65cb) [](/sustainable-future-proof-web-frontends-89ac5dd67dfb) [## 可持续的(经得起未来考验的)网络前端

### 在这一点上，它已经成为一个公认的迷因，每隔几个月就会弹出一个新的 JavaScript 框架…

levelup.gitconnected.com](/sustainable-future-proof-web-frontends-89ac5dd67dfb) [](/secure-frontend-authorization-67ae11953723) [## 使用令牌处理程序模式的 SPA 身份验证

### 为单页应用程序构建安全认证过程的现代方法。

levelup.gitconnected.com](/secure-frontend-authorization-67ae11953723) [](/your-first-angular-microfrontend-58950768a465) [## 角形微前端波导

### 这是一个关于如何创建一个简单的 angular 应用程序来使用另一个应用程序的模块的分步指南…

levelup.gitconnected.com](/your-first-angular-microfrontend-58950768a465)