# 梅文的天才

> 原文：<https://levelup.gitconnected.com/the-genius-of-maven-7d47e4f240e7>

## 这场悄无声息的革命颠覆了建筑世界

![](img/eabd79c0c443f66656fdcd3df81fdadb.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=63022) 的[维基图片](https://pixabay.com/users/wikiimages-1897/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=63022)

C 语言最棒的一点是它引入了 Make，这是第一个通用构建工具之一。Make 作为一个转换工具，用一小组命令将一种文件类型转换成另一种文件类型。给定一个目标，Make 可以通过一组转换逆向工作，确定构建目标所需的所有命令，然后根据需要执行它们。

但是马克并不聪明。您需要指定构建您的产品所需的所有转换，即使大多数转换都是琐碎的。您需要拼出制作最终产品的所有文件，或者以某种方式组织它们，以便可以使用通配符来推断它们。这导致了许多样板文件和伪标准，它们可能会从一个项目偏离到另一个项目。理解构建系统可能变得和理解代码本身一样困难。

Apache Ant 与此类似，它是一套构建产品的规则，形成了构建最终成为产品的子目标所必需的命令和子命令树。它引入了“任务”的概念，这是预定义的构建指令，旨在跨平台移植。但是它仍然需要大量的样板文件，Ant 构建文件可能会长达数千行，难以理解。

‘Maven’这个名字意味着某个特定领域的专家，这也是 Apache Maven 的本意。与复杂的 Makefiles 和 Ant 构建文件不同，Maven‘POM’文件可能非常短。

```
<?xml version="1.0" encoding="UTF-8"?>
<project ae ky" href="http://maven.apache.org/POM/4.0.0" rel="noopener ugc nofollow" target="_blank">http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="[http://www.w3.org/2001/XMLSchema-instance](http://www.w3.org/2001/XMLSchema-instance)"
  xsi:schemaLocation="[http://maven.apache.org/POM/4.0.0](http://maven.apache.org/POM/4.0.0)
  [http://maven.apache.org/maven-v4_0_0.xsd](http://maven.apache.org/maven-v4_0_0.xsd)"> <modelVersion>4.0.0</modelVersion> <groupId>net.kamradtfamily</groupId>
  <artifactId>product-name</artifactId>
  <version>1.0-SNAPSHOT</version>
</project>
```

构建一个项目只需要 7 行 XML。当然，要把它缩小到这个尺寸，它得做很多假设。最大的假设是您正在从一个 java 文件创建一个 jar 文件。为了在另一种表达式语言中工作，你需要包含一个该语言的构建插件。

这种“约定优于配置”的方法在 Java 编程世界中引起了一场革命，但很少被注意到。项目的目录结构和构建顺序开始变得标准化。因为单元测试是 Maven 惯例的一部分，所以单元测试开始变得无处不在。因为 Maven 非常固执己见，而且大多数 Java 开发人员都熟悉 Maven 的观点，所以一个新的 Java 开发人员可能会关注一个不熟悉的项目，并很快开始变得富有成效。

对 Maven 最大的抱怨之一是它太死板，而像 Ant 这样的系统要灵活得多。但是这种严格性也意味着其他程序可以很容易地解析和理解 Maven Java 项目。Apache NetBeans 不需要其他项目文件来导入新的 Java 项目。IntelliJ 的 IDE 仍然需要自己的项目文件，但是可以很容易地从现有的 Maven 项目中构建它们。Jenkins 等其他构建工具可以阅读 Maven 项目，并从中推断出很多东西。

最后一句包含一个关键词“其他构建工具”。Maven 不是我们的构建工具吗？不完全是。Maven 是用 java 文件制作 jar 文件的专家，但是当你需要做更多的工作时，你就开始遇到麻烦了。当您试图将整个 CI/CD 过程放入 Maven 时，您将得到类似于那些数千行 Ant 和 Make 文件的东西。不要那样做。让詹金斯接手，做它擅长的事情。

## 触手可及的开源

Maven 引发的另一场革命是 Java 开源革命。通过严格的依赖项管理和一个中央存储库，开源项目可以在其中存放它们的 jar 文件，包含外部依赖项通常就像在 POM 文件中添加五行 XML 一样简单。像 Spring 这样的框架、数据库驱动程序、JSON 解析器和其他文件格式都可以在项目中使用。

不仅你可以很容易地包含你最喜欢的库，你最喜欢的库也可以以可控的方式引入新的版本。如果创建了新版本的依赖项，您的项目不会中断，因为您的项目指定了保证保持不变的特定版本。您将在方便您的项目的时候升级到新版本。

开源库也能够指定它们自己的依赖项，称为传递性依赖项。这意味着作为应用程序开发人员，您只需要指定您想要的库，而不必了解所有需要的附加库。这对于像 Spring 这样的无所不包的框架特别有帮助，因为它们有大量的可传递依赖。但是，如果两个库共享一个公共的依赖项，但版本不同，也会带来麻烦。

## 内部代码共享

我最喜欢的兰迪主义之一是“共享代码很难，但不共享代码更难”。任何在拥有大量代码库的代码商店工作过的人都会面临这种困境。对于像 Go 这样缺乏依赖管理的编程语言来说，mono-repo 通常是代码共享的唯一选择。但是单一回购也有自己的问题。人们听说过类似 Twitter 向外部员工邮寄硬盘的恐怖故事，因为 git 克隆操作需要几天时间。Mono-repos 也是谷歌选择的道路，但谷歌有数百名非常聪明的程序员可以解决这个问题。他们的解决方案是手工制作的版本控制系统和许多外部工具。大多数代码商店没有这样的奢侈品。

Maven 引入了镜像存储库的概念，在这里你可以拥有你自己的 Maven central 的克隆，以及你想要在你的团队之间共享的所有代码(参见我的文章[让 Maven Central 休息一下](/giving-maven-central-a-break-e36978174712))。但这并不容易。为了让共享有效，你*必须*版本化你的库。Maven 有一个‘release’插件，据说可以帮助你制作发布版本，但是在 Maven 社区中这有点像是一个[笑话](https://axelfontaine.com/blog/dead-burried.html)。底线是，发布稳定的 jar 版本仍然不是一个简单的过程。

Maven 还有许多其他的好想法，我认为它们从未真正实现过。构建周期包括一个大部分未使用的“站点”构建，该构建生成一个 HTML 站点，其中包含诸如依赖项、JavaDocs、单元测试甚至代码覆盖率之类的报告。共享代码的一个主要障碍是缺乏文档，站点应该已经解决了这个问题，但是它使用起来太麻烦了，并且经常在应该是简单的 POM 文件上添加数百行。许多报告在面对多模块构建时也会出错。

回顾使用 Maven 的 15 年，我可以看到这场革命。但是新的开发者认为 Maven 的革命性是事实。在开源社区的全力支持下，标准的项目结构和强大的依赖性管理将项目管理的复杂性降低了整整一个数量级。我向 Maven 的发明者 Jason van Zyl 致敬。