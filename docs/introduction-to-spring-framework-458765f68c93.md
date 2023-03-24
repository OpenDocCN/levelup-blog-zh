# Spring 框架简介

> 原文：<https://levelup.gitconnected.com/introduction-to-spring-framework-458765f68c93>

Spring 框架是一个 Java 平台应用框架和反转控制容器。

![](img/980702a26adb8a0146f1d5558e4c3baf.png)

照片由[特雷西·亚当斯](https://unsplash.com/@tracycodes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/java?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

## 春天是什么？

Spring 框架是一个 Java 平台应用框架和反转控制容器。任何 Java 应用程序都可以使用该框架的核心功能，但是，在 Java EE(企业版)平台上构建 web 应用程序还有一些增强功能。作为企业 JavaBeans (EJB)架构的扩展，该框架在 Java 社区中变得越来越流行，尽管它没有规定任何特定的编程模型。Spring 框架是一个免费的开源框架，最初发布于 2002 年。

Spring 是一个简单的框架，有很多特性。它有时被称为框架的框架，因为它支持各种框架，包括 Struts、Hibernate、Tapestry、EJB、JSF 等等。从更广泛的意义上来说，框架可以被看作是一种结构，在这种结构中，我们发现了各种技术难题的解决方案。

Spring 框架中有各种模块提供各种服务。

1.  spring 核心容器是 Spring 的基础模块，它提供了 Spring 容器(BeanFactory 和 ApplicationContext)。
2.  面向方面编程(AOP)允许实现横切问题。
3.  Spring Roo 模块通过关注约定胜于配置，为基于 Spring 的企业应用程序提供了一个快速的应用程序开发解决方案。
4.  数据访问:使用 Java 数据库连接(JDBC)和对象关系映射在 Java 平台上处理关系数据库管理系统。
5.  消息传递:可配置的消息监听器对象注册，用于通过 Java 消息服务(JMS)从消息队列透明地使用消息，并改进了通过标准 JMS APIs 的消息发送
6.  模型-视图-控制器(MVC)是一个基于 HTTP 和 servlet 的框架，用于在线应用程序和 RESTful(表述性状态转移)Web 服务，为扩展和定制提供挂钩。

## 春天 vs Spring Boot

**Spring** 框架是应用最广泛的 Java 应用开发框架。Spring 框架的基本特性是依赖注入，也称为控制反转(IoC)。我们可以用 Spring 框架创建一个松散链接的应用程序。如果应用程序类型或属性定义得很清楚，那么最好利用它们。

**Spring Boot** 是一个弹簧框架模块。它使我们能够以最少的配置或无需配置来创建自包含的应用程序。如果我们想创建一个简单的基于 Spring 的应用程序或 RESTful 服务，我们应该使用它。

我们可以比较弹簧和弹簧靴的以下特点。

1.  **Spring Framework** 是一个广泛用于构建应用的 Java EE 框架。但是 **Spring Boot** 习惯于开发 REST APIs。
2.  Spring 的目标是简化 Java EE 开发，而 Spring Boot 的目标是缩短代码长度，并提供开发 Web 应用程序的最简单方法。
3.  **Spring Boot** 的主要特点是 ***自动配置*** 。它根据需求自动配置类。但是 **spring** 的首要特性是 ***依赖注入。***
4.  Spring 允许开发松散耦合的应用程序，有助于简化事情，而 Spring boot 有助于创建配置更少的独立应用程序。
5.  对比测试过程，为了测试 spring 应用程序，我们需要显式地设置服务器。但是 **Spring boot** 提供嵌入式服务器，比如 *Jetty* 和 *Tomcat* 。

## 控制反转(IOC)和依赖注入

这些是在编程代码中用来消除依赖性的设计模式。它们使得测试和维护代码变得容易。让我们看一个例子，代码如下。

在这个例子中，学生和地址之间存在依赖关系(紧耦合)。我们在控制反转场景中做了类似这样的事情:

作为 IOC 的结果，代码是松散连接的。如果我们的逻辑被移到不同的环境中，就不需要修改代码。

在 Spring 框架中，IOC 容器负责注入依赖项。我们以 XML 文件或注释的形式给出 IOC 容器元数据。

**依赖注入的优势**

*   使代码易于维护，因为它是松散连接的
*   使测试代码变得简单

## Spring 框架的优势

1.  **预定义模板**

Spring 框架包括 JDBC、Hibernate 和 JPA 等技术的模板。因此，没有必要编写大量代码。它隐藏了这些技术的基本步骤。以 JdbcTemplate 为例，您不必为处理异常、创建连接、构造语句、提交事务和关闭连接而创建代码。您只需要编写查询执行代码。因此，它节省了大量的 JDBC 代码。

**2。易于测试的应用程序**

依赖注入使得测试程序变得更加容易。尽管 EJB 或 Struts 应用程序需要服务器来运行，但是 Spring 框架不需要。

**3。轻量级**

由于它的 POJO 实现，Spring 框架相当轻便。Spring 框架不强迫程序员继承或实现任何类或接口。正是由于这个原因，它被描述为非侵入性的。

**4。松散耦合**

由于依赖注入，Spring 应用程序是松散耦合的。

**5。快速开发能力**

Spring Framework 的依赖注入功能，以及它对各种框架的兼容性，使得开发 JavaEE 应用程序变得轻而易举。

**6。一个有效的抽象**

JMS、JDBC、JPA 和 JTA 只是它抽象的 JavaEE 需求的一部分。

我将在以后的 [*文章*](/spring-framework-modules-and-architecture-abc8d4a53ee6) 中讨论 spring 模块和架构。你可以在 [**这里**](https://spring.io/) 获得更多关于官方 spring 框架文档的详细信息

编码快乐！！！