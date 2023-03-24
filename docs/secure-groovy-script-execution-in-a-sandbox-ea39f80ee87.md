# 在沙箱中安全执行 Groovy 脚本

> 原文：<https://levelup.gitconnected.com/secure-groovy-script-execution-in-a-sandbox-ea39f80ee87>

![](img/36eba8d33d0a612c380417ff399ebeb6.png)

图片由作者从[http://groovy-lang.org](http://groovy-lang.org)和[https://www.compart.com/en/unicode/U+1F512](https://www.compart.com/en/unicode/U+1F512)合成

我正在研究在沙箱中执行不可信的 Groovy 脚本。我从来都不是一个安全人员，所以这个过程是一次宝贵的学习经历。我将用这篇文章来总结我的发现。

我在 Capco.com[做顾问。非常感谢我的同事们，他们慷慨地发表了意见。此处表达的观点不一定反映雇主的观点。](https://www.capco.com)

# Java 沙盒

概括地说，Java 沙箱依赖于以下三个协同工作的组件:

1.  字节码检验器
2.  类加载器
3.  安全管理器及其策略

字节码验证器确保编译后的类文件是有效的，并且不会利用虚拟机。类装入器是分层创建的。如果您想象一个类装入器树，那么“叶子”类装入器只能在同一分支上访问它们的类和它们的父类。使用这种结构，您可以通过使用自定义类加载器在受限环境中执行代码。最后，安全管理器基于自定义策略文件提供运行时权限检查。

# 项目要求

众所周知，便利性和安全性之间是有权衡的。找到正确的平衡需要与所有关键利益相关者进行讨论和合作。在我们深入研究这篇文章之前，让我们假设这些是我们正在处理的事实:

1.  今天，Groovy 脚本是为非常特定的上下文编写的。他们有几百人。它们与主程序在同一个虚拟机中执行，并且可以从主程序中访问类。换句话说，他们是可信的。
2.  展望未来，该特定上下文可能会向不可信方开放。我们需要一个计划来保护环境，以便将来运行不受信任的 Groovy 脚本。

考虑到这些事实，我们决定使用安全管理器和各种 Groovy 编译器定制来关注运行时权限检查和可选的源代码级别检查。

# 保护 Groovy 脚本执行环境的步骤

## 步骤 1:安全管理器和策略文件

首先，我们需要安装一个安全管理器来执行运行时权限检查。如果 Groovy 脚本运行在同一个 VM 中，你不希望它调用`System.exit(0)`。安全管理器与策略文件协同工作。策略文件声明授予每个基本代码的权限。Codebase 基本上是一个描述代码从哪里加载的 URL。幸运的是，所有由 Groovy 解释器解释的 Groovy 脚本都有`file:/groovy/script`代码库或`file:/groovy/shell`代码库。因此，将所有权限授予正在运行的程序而不是 Groovy 的策略文件应该是这样的:

```
grant codeBase "file://<your jar file>" {
  permission java.security.AllPermission;
};grant codeBase "file:/groovy/shell" {
};grant codeBase "file:/groovy/script" {
};
```

有了策略文件后，我们可以通过添加以下命令行参数来应用它:

```
-Djava.security.manager -Djava.security.policy=<policy file>
```

## 步骤 2:管理策略违规

如果你是绿地开发项目中的少数幸运儿，就没有必要担心这一点。但是如果你像我一样，试图保护一个现有的环境，肯定会有例外。现有的 Groovy 脚本可能需要策略文件中没有授予的权限。

我建议您使用[access controller . doprivileged](https://docs.oracle.com/en/java/javase/15/docs/api/java.base/java/security/AccessController.html)调用，而不是更改策略文件来授予缺失的权限。该方法以“特权”方式执行代码块，这意味着没有权限检查。重要的是这段代码要尽可能的小。我提倡这种方法的原因是方法调用很容易识别，因此可以在不久的将来纠正。

接下来，我们将看看 Groovy 提供的帮助保护脚本执行的具体措施。

## 步骤 3: Groovy 绑定限制

Groovy 绑定是一种将变量传递给 Groovy 脚本的机制。如果 Groovy 脚本不可信，我们必须控制哪些内容可以传入 Groovy 脚本。这一点尤其正确，因为并非所有第三方库 API 都受到权限检查的保护。

## 步骤 4:应用 Groovy 脚本编译器配置

Groovy 脚本编译器也可以由不同的编译定制器定制。例如，`ImportCustomizer`可以限制 Groovy 脚本可以导入的内容，而`SecureASTCustomizer`试图限制 Groovy 脚本可以使用的结构。不幸的是，正如本文所述，这两种方法都可以很容易地解决:[https://KOH suke . org/2012/04/27/groovy-secureeastcustomizer-is-habital/](https://kohsuke.org/2012/04/27/groovy-secureastcustomizer-is-harmful/)(作者来自 Jenkins 的 Kohsuke Kawaguchi)。这是一篇理解这些定制器局限性的好文章。

最后，川口先生好心在 Github 上发布了 Groovy 沙盒项目:[https://github.com/jenkinsci/groovy-sandbox](https://github.com/jenkinsci/groovy-sandbox)和脚本安全插件(詹金斯专用):[https://github.com/jenkinsci/script-security-plugin](https://github.com/jenkinsci/script-security-plugin)。这两个项目绝对值得研究，以便有可能集成到您的项目中。

# 其他考虑

除了上述考虑之外，您还需要管理 Groovy 脚本可以消耗多少 CPU 和内存。如果这是您所关心的，您可以在不同于主程序的线程中执行 Groovy 脚本。如果有必要，可以使用一个监控线程来终止 Groovy 脚本线程。

在一个单独的容器中运行 Groovy 脚本也可以提供进一步的保护，但是它的操作成本更高，并且不容易调整到现有的实现中。

# 最后的想法

根据定义，执行不受信任的代码是一件危险的事情。保护执行环境的选项可能会受到现有实现的限制。没有两个项目是相同的。通过在这里总结我的发现，我希望你也能从中受益。

# 附言（同 postscript）；警官（police sergeant）

在这次练习中，我学到了很多东西。很难将它们融入文章的流程中。因此，我将把它们列在这里:

1.  Java 安全管理器是一个系统级设置。要在每个线程的基础上控制权限检查，您必须编写自己的安全管理器，并使用`ThreadLocal`来条件化权限检查。
2.  同样，自定义安全管理器可以与自定义类加载器一起使用，以提供更大的灵活性。
3.  Java 访问控制是基于代码库的，即代码是从哪里加载的。我发现在 JUnit 测试中运行 Groovy 脚本没有`file:/groovy/shell`或`file:/groovy/script`代码库。相反，它们继承了 JUnit 类的代码库。我花了无数令人沮丧的时间来调试本该正常工作的东西。
4.  类似地，在开发过程中，运行 Maven Spring Boot 插件的 Spring Boot 应用程序具有不同的代码基础。你应该同时授予`${user.dir}/target/classes`和`${user.home}/.m2/repository`权限
5.  策略可以通过编程方式添加，也可以使用策略文件添加。