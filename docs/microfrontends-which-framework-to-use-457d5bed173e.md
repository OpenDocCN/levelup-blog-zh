# 微前端:使用哪个框架？

> 原文：<https://levelup.gitconnected.com/microfrontends-which-framework-to-use-457d5bed173e>

![](img/7acae40949ac53c70e4af2b9091c7a50.png)

在过去的几年里，我一直在帮助许多客户和公司迁移到微前端。我们总是从那个著名的问题开始，“ ***使用哪个框架？***

这个过程我已经经历过很多次了，但还是会停下来思考这个问题。挑战在于我们每隔几周就会得到新的框架。在过去，选择[服务器端组合](https://www.linkedin.com/pulse/micro-frontends-what-why-how-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0/#:~:text=types%20of%20Compositions%3A-,Server-Side%20Composition,-In%20this%20case)是唯一可用的方法。有了 NPM，我们开始有了[构建时合成](https://www.linkedin.com/pulse/micro-frontends-what-why-how-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0/#:~:text=integrate%20Micro-Frontends-,Build-Time%20integration,-This%20is%20what)。最近，随着 Webpack 的进步，我们开始在客户端上拥有[运行时合成。最著名的框架是](https://www.linkedin.com/pulse/micro-frontends-what-why-how-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0/#:~:text=Client-Side%20Composition)[单 SPA](https://www.linkedin.com/pulse/microfrontends-single-spa-cli-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0) 和[模块联盟](https://www.linkedin.com/pulse/micro-frontends-hands-on-example-using-react-webpack-rany)。(本文[中所有可用框架的列表](https://www.linkedin.com/pulse/micro-frontends-what-why-how-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0/#:~:text=What%20are%20different%20Frameworks%20available%20for%20Micro-Frontends))

如果你是微前端的新手，我建议你阅读下面的[文章](https://www.linkedin.com/pulse/micro-frontends-what-why-how-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0/):

[](https://www.linkedin.com/pulse/micro-frontends-what-why-how-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0/) [## 微前端:什么、为什么和如何

### 在我以前的文章(本文末尾和这里的链接)中，我亲自展示了什么是微前端以及如何…

www.linkedin.com](https://www.linkedin.com/pulse/micro-frontends-what-why-how-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0/) 

在 Zulily，我们一直在混合使用服务器端和构建时合成。最近，我们添加了运行时组合，以便能够将 SPA 应用程序分解为[微前端组件，从而拥有更多的自主权和域所有权](https://www.linkedin.com/pulse/micro-frontends-what-why-how-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0/#:~:text=%3D%3D%3D%3D%3D%3D%3D%3D%3D%3D%3D%3D%3D%3D%3D-,Why%20Micro%20Frontends,-%3F)。对于客户端运行时组合，我们有两个首选框架:[单 SPA](https://www.linkedin.com/pulse/microfrontends-single-spa-cli-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0) 和[模块联合](https://www.linkedin.com/pulse/micro-frontends-hands-on-example-using-react-webpack-rany)。我在之前的[文章](https://www.linkedin.com/pulse/micro-frontends-from-begining-expert-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0)中已经详细讨论了这两个框架。它们非常接近，但是模块联合会给你更多的自由，并将微前端转换成相同 SPA 上的组件。当然，两者都存在挑战。这两个框架的最大挑战是与创建-反应-应用(CRA)的兼容性。下面的文章将展示一些避开 CRA 的方法:

[](https://www.linkedin.com/pulse/microfrontends-integrating-create-react-app-cra-eject-rany) [## 微前端:集成一个创建-反应-应用程序(CRA)项目，没有弹出使用 CRACO…

### 什么是 CRACO: CRACO 代表创建-反应-应用程序配置覆盖。它被实现为一种简单的方法来覆盖…

www.linkedin.com](https://www.linkedin.com/pulse/microfrontends-integrating-create-react-app-cra-eject-rany) [](https://www.linkedin.com/pulse/microfrontends-create-react-app-without-eject-using-rany) [## 具有模块联合的微前端:使用 CRACO 创建-反应-应用而不弹出

### 每次我谈到使用 Webpack 5 Modulefederation 插件的微前端，create-react-app 的问题…

www.linkedin.com](https://www.linkedin.com/pulse/microfrontends-create-react-app-without-eject-using-rany) [](https://www.linkedin.com/pulse/microfrontends-single-spa-migration-converting-app-rany) [## 微前端单 SPA 迁移:使用…将 create-react-app 转换为微前端应用

### 在过去的一年中，将运行时微前端与 create-react-app (CRA)结合使用的挑战不断升级…

www.linkedin.com](https://www.linkedin.com/pulse/microfrontends-single-spa-migration-converting-app-rany) [](https://www.linkedin.com/pulse/how-upgrade-react-17-webpack-5-step-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0) [## 如何一步步升级到 React 17 和 Webpack 5

### 为了能够在我的组织中使用微前端，我必须将传统应用升级到 React 17 和 Webpack 5。当然，就像…

www.linkedin.com](https://www.linkedin.com/pulse/how-upgrade-react-17-webpack-5-step-rany-elhousieny-phd%E1%B4%AC%E1%B4%AE%E1%B4%B0)  [## 使用 CRACO 的 CRA 单 SPA 微前端

### 在本文中，我将解释如何使用 Single-SPA 框架在一个 React 项目上构建微前端

www.linkedin.com](https://www.linkedin.com/pulse/microfrontends-single-spa-cra-using-craco-rany-elhousieny-phd%25E1%25B4%25AC%25E1%25B4%25AE%25E1%25B4%25B0) 

虽然两者都有 CRA 兼容性问题，但模块联合有一个更大的问题。模块联合插件仅适用于 Webpack 5。CRA 有一个与 Webpack 5 的兼容性问题，它仍然处于 alpha 阶段，有许多失败和黑客让它工作(见 https://github.com/facebook/create-react-app/issues/9994[)。虽然模块联合很容易使用，但在现阶段使用 CRA 还是有风险的。如果我们考虑未来，我们可能会冒险，因为它最终会得到解决。然而，大多数公司不会为用 CRA 创建的新项目冒这个风险。这么多的黑客和解决错误，让它工作。](https://github.com/facebook/create-react-app/issues/9994)

因此，截至本文日期 2021 年 8 月，如果你的团队依赖 CRA，单温泉是首选。但是，如果您是 Webpack 的专家，并且能够独立于 CRA 配置站点，那么模块联合将是最佳选择。不幸的是，大多数开发者依赖 CRA。如果您依赖 CRA，单 SPA 仍然有风险，但更稳定，错误更少。

这里有一些文章可能会帮助你摆脱 CRA

[](https://ranyel.medium.com/freedom-from-create-react-app-how-to-create-react-apps-without-cra-27fadeb79c82) [## 从创建-反应-应用中解放出来(如何在没有 CRA 的情况下创建反应应用)

### 当我开始使用模块联盟迁移到微前端时，我面临着两个挑战

ranyel.medium.com](https://ranyel.medium.com/freedom-from-create-react-app-how-to-create-react-apps-without-cra-27fadeb79c82) [](https://www.linkedin.com/feed/update/urn:li:ugcPost:6837477108789456896?updateEntityUrn=urn%3Ali%3Afs_feedUpdate%3A%28*%2Curn%3Ali%3AugcPost%3A6837477108789456896%29) [## 兰尼·埃尔豪斯尼，PhDᴬᴮᴰ在 LinkedIn 上:创建 React 应用程序而不创建 react 应用程序

### 创建 react 应用程序而不创建 React 应用程序(CRA)...

www.linkedin.com](https://www.linkedin.com/feed/update/urn:li:ugcPost:6837477108789456896?updateEntityUrn=urn%3Ali%3Afs_feedUpdate%3A%28*%2Curn%3Ali%3AugcPost%3A6837477108789456896%29) 

# **新项目怎么样？**

对于新项目，我建议避开 CRA。虽然它节省了大量的配置，但它限制了您。学习 Webpack 并创建自己的配置。这是值得的。在这种情况下， ***模块联邦*** 将是微前端的最佳选择。对于遗留站点，使用服务器端或构建时合成将它们包含到新创建的微前端中。有关详细信息，请阅读以下文章:

[](https://ranyel.medium.com/freedom-from-create-react-app-how-to-create-react-apps-without-cra-27fadeb79c82) [## 从创建-反应-应用中解放出来(如何在没有 CRA 的情况下创建反应应用)

### 当我开始使用模块联盟迁移到微前端时，我面临着两个挑战

ranyel.medium.com](https://ranyel.medium.com/freedom-from-create-react-app-how-to-create-react-apps-without-cra-27fadeb79c82) [](https://www.linkedin.com/feed/update/urn:li:ugcPost:6837477108789456896?updateEntityUrn=urn%3Ali%3Afs_feedUpdate%3A%28*%2Curn%3Ali%3AugcPost%3A6837477108789456896%29) [## 兰尼·埃尔豪斯尼，PhDᴬᴮᴰ在 LinkedIn 上:创建 React 应用程序而不创建 react 应用程序

### 创建 react 应用程序而不创建 React 应用程序(CRA)...

www.linkedin.com](https://www.linkedin.com/feed/update/urn:li:ugcPost:6837477108789456896?updateEntityUrn=urn%3Ali%3Afs_feedUpdate%3A%28*%2Curn%3Ali%3AugcPost%3A6837477108789456896%29) [](https://ranyel.medium.com/react-from-beginner-to-expert-without-cra-3d4dbdcfa722) [## 没有 CRA，从初学者到专家的反应

### 为什么要反应

ranyel.medium.com](https://ranyel.medium.com/react-from-beginner-to-expert-without-cra-3d4dbdcfa722) 

====================

# 从哪里开始？

如果你决定避开 CRA，学习 Webpack，下面这篇文章会对你有很好的帮助:

[](https://ranyel.medium.com/react-from-beginner-to-expert-without-cra-3d4dbdcfa722) [## 没有 CRA，从初学者到专家的反应

### 为什么要反应

ranyel.medium.com](https://ranyel.medium.com/react-from-beginner-to-expert-without-cra-3d4dbdcfa722) [](https://www.linkedin.com/feed/update/urn:li:ugcPost:6837477108789456896?updateEntityUrn=urn%3Ali%3Afs_feedUpdate%3A%28*%2Curn%3Ali%3AugcPost%3A6837477108789456896%29) [## 兰尼·埃尔豪斯尼，PhDᴬᴮᴰ在 LinkedIn 上:创建 React 应用程序而不创建 react 应用程序

### 创建 react 应用程序而不创建 React 应用程序(CRA)...

www.linkedin.com](https://www.linkedin.com/feed/update/urn:li:ugcPost:6837477108789456896?updateEntityUrn=urn%3Ali%3Afs_feedUpdate%3A%28*%2Curn%3Ali%3AugcPost%3A6837477108789456896%29) [](https://javascript.plainenglish.io/micro-frontends-from-the-begining-to-expert-33d660a05bb5) [## 从初学者到专家的微前端

### 在这篇文章中，我将把之前所有的文章和视频以一种能帮助人们理解的方式进行整理…

javascript.plainenglish.io](https://javascript.plainenglish.io/micro-frontends-from-the-begining-to-expert-33d660a05bb5)