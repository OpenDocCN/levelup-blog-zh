# 将您的 Spring Boot 和 Angular 2+项目打包在一起

> 原文：<https://levelup.gitconnected.com/packaging-your-spring-boot-and-angular-2-projects-together-a13a9c5efdb7>

这篇文章展示了如何将前端和后端项目包装在一起。

![](img/a405ccb0f3934f22d50e6c4a5c523d0f.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [chuttersnap](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) 拍摄

## 重要的事情先来

首先，在使用这个解决方案之前，有必要给你一个背景。当您考虑本地项目时，这可能是有用的，例如，在本地项目中没有弹性(云架构最重要的好处之一)，您需要在单个容器或单个应用服务器中分发单个 WAR(或 JAR)文件。

解决方法并不简单。您不需要成为专家，但我假设您对 Maven、Spring Boot 和 Angular 有一些基本的了解，尤其是因为我将跳过项目的创建步骤。

我用的是 Java 11，Maven 3.6.1，Spring Boot 2.2.1，Node 10.16.0 和 Angular 8.2.14。

首先，我推荐你从我的 [GitHub](https://github.com/diegohordi/package-springboot-angular) 中克隆这个项目。

让我们把手弄脏吧！

## 主项目

主项目只是包含后端和前端项目作为模块，它的 POM 如下。

请注意，我们在这里将 Spring Boot 启动器作为父 POM。

## 前端项目

首先，您需要编辑 **package.json** 文件，并在“scripts”属性上放置一个新脚本，如第 7 行所示。

这个改变是必要的，因为我们将使用[前端 Maven 插件](https://github.com/eirslett/frontend-maven-plugin)来构建前端项目。

这个插件值得很多星！有了它，就可以做很多很酷的事情，比如在本地安装 Node.js 和 NPM 并运行脚本。因此，为了使用它，我们需要设置 de frontend 的 POM。所以一步一步来看。

在开始行中，我们设置父 POM(到主项目)并定义前端属性，例如节点和 NPM 的版本。

之后，我们如下设置前端 Maven 插件。

请注意，在第 13 行，我们设置了插件的工作目录——节点和 NPM 将被本地安装。最后，我们设置将在构建生命周期中执行的目标。

#1 执行安装 Node.js 和 NPM，#2 安装项目依赖项，最后但同样重要的是，我们运行 build-prod 脚本来构建包。

## **后端项目**

后端项目包含一个非常简单的 Spring Boot 项目。你需要注意的主要方面是，事实上我们正在使用 [Maven 资源插件](https://maven.apache.org/plugins/maven-resources-plugin/) 到来收集由前端项目构建的捆绑包和 [Spring Boot Maven 插件](https://docs.spring.io/spring-boot/docs/current/maven-plugin/index.html)来重新打包目标，以保持所有这些东西在一起。所以，我们一步步来看它的 POM。

在开始行中，我们设置父 POM(到主项目)并定义后端属性，就像我们在前端项目中所做的一样。

设置项目属性后，我们声明我们的依赖项，如下所示。

最后，我们设置插件来编译、收集资源并打包所有这些东西。

## **额外:角度路线问题**

就像我们需要在 Apache HTTP 服务器上使用重写模块，或者在 NGINX 上编写一些重写规则一样，当我们打包一个 Angular 项目，该项目在一个使用嵌入式 Tomcat 服务器的 Spring Boot 中使用路由器时，我们也需要处理它。关于如何解决这个问题，有很多解决方案，但我更喜欢编写一个过滤器来完成这项工作。它非常简单，易于更改，并且运行良好，没有任何外部依赖性。

## 创建包

要创建包，只需在项目的主目录下运行下面的命令。

```
mvn package
```

执行后，将在后端的项目目录中的/target 目录下生成一个可分发的 WAR 文件。

## 结论

在这篇文章中，我打算与你分享我们如何将 Angular 2+的前端和 Spring Boot 的后端打包在一个可分发的 WAR 文件中，只使用 Maven 插件，而不使用其他外部工具。

正如我在这篇文章的开头所说的，这个解决方案旨在当你必须处理基础设施限制时使用，所以，对于许多其他场景，我建议你把这些事情分开来。

希望这个帖子有用。

感谢您的阅读，如果您有任何问题，请联系我！=)

## 参考

[前端 Maven 插件](https://github.com/eirslett/frontend-maven-plugin)

[Maven 资源插件](https://maven.apache.org/plugins/maven-resources-plugin/)

[Spring Boot Maven 插件](https://docs.spring.io/spring-boot/docs/current/maven-plugin/index.html)