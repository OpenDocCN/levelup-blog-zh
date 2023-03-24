# Java:模块、库和包之间的区别

> 原文：<https://levelup.gitconnected.com/java-what-is-the-difference-between-a-module-a-library-and-a-package-468aeae4e79>

## 库包含充满 Java 类的包的模块

## 从小到大

按照从小到大的顺序是:包->模块->库。

一个包包含许多类(。java 文件)，基本上是 Java 项目中的一个文件夹。

一个模块包含许多包(带有。java 文件)。模块基本上是一个单一用途的库。

一个库包含许多模块(一个模块通常被打包成一个。jar 文件)。一个库可以包含许多模块，但是它们通常都通过一些主题连接在一起。

## 包裹

包是组织一组相关类和接口的名称空间。从概念上讲，你可以把包想象成类似于你计算机上的不同文件夹。你可以把 HTML 页面放在一个文件夹中，图片放在另一个文件夹中，而脚本或应用程序放在另一个文件夹中。因为用 Java 编程语言编写的软件可以由数百个或数千个单独的类组成，所以通过将相关的类和接口放入包中来保持事物的有序是有意义的。[【4】](https://docs.oracle.com/javase/tutorial/java/concepts/package.html)

## 组件

模块是具有描述符文件的相关 Java 包和相关资源的集合，描述符文件包含关于该模块公开了哪些包/资源的信息、当前模块使用了哪些包以及一些其他信息。[5]*Java 模块*是一种打包机制，它使你能够将 Java 应用程序或 Java API 打包成一个单独的 Java 模块。一个 Java 模块被打包成一个*模块化 JAR 文件*。Java 模块可以指定它包含的哪些 Java 包应该对使用该模块的其他 Java 模块可见。一个 Java 模块还必须指定需要哪些其他的 Java 模块来完成它的工作。

Java 模块是通过 *Java 平台模块系统* (JPMS)在 Java 9 中新增的 [**特性。Java 平台模块系统有时也被称为 *Java Jigsaw* 或 *Project Jigsaw* ，这取决于你在哪里阅读。Jigsaw 是开发期间内部使用的项目名称。后来 Jigsaw 改名为 Java 平台模块系统。**](http://tutorials.jenkov.com/java/index.html#new-in-java-9)**[【1】](http://tutorials.jenkov.com/java/modules.html)**

## 图书馆

Java 库只是别人已经写好的类的集合。你下载这些类并告诉你的计算机，然后你可以在你的代码中使用这些类。这让您可以扩展 Java 的功能，并依赖其他人已经测试过的代码，而不是自己做所有的事情。[【2】](https://happycoding.io/tutorials/java/libraries)一个库是相关功能的*集合*，而一个模块只提供*单个*功能。这意味着，如果您的系统既有模块又有库，那么一个库通常会包含多个模块。例如，您可能有一个集合库，在一个库中包含一个`List`模块、一个`Set`模块和一个`Map`模块。[3]一个单独的库可能包含许多。jar 文件，而单个模块只是一个。jar 文件。]

如果你还没有加入 Medium，但你想加入，请点击这里。通过我的推荐链接注册 Medium，我将获得一小笔佣金。

# 参考

[1] Java 模块。[http://tutorials.jenkov.com/java/modules.html](http://tutorials.jenkov.com/java/modules.html)

[2]图书馆。[https://happycoding.io/tutorials/java/libraries](https://happycoding.io/tutorials/java/libraries)

[3]模块、库和框架的区别。[https://stack overflow . com/questions/4099975/difference-a-module-library-and-a-framework #:~:text = Also % 2C % 20a % 20 library % 20 is % 20a，将% 20 典型地% 20 包含% 20 多个% 20 模块](https://stackoverflow.com/questions/4099975/difference-between-a-module-library-and-a-framework#:~:text=Also%2C%20a%20library%20is%20a,will%20typically%20contain%20multiple%20modules)。

【4】什么是套餐？[https://docs . Oracle . com/javase/tutorial/Java/concepts/package . html](https://docs.oracle.com/javase/tutorial/java/concepts/package.html)

【5】“包”和“模块”有什么区别？。[https://stack overflow . com/questions/3680883/what-the-difference-of-package-and-module #:~:text = A % 20 module % 20 is % 20a % 20 collection，module % 20 and % 20 some % 20 other % 20 information](https://stackoverflow.com/questions/3680883/whats-the-difference-between-package-and-module#:~:text=A%20module%20is%20a%20collection,module%20and%20some%20other%20information)。