# 用 Mantle TestKit 测试 WordPress

> 原文：<https://levelup.gitconnected.com/testing-wordpress-with-mantle-testkit-a2c42d3b3a8a>

## 对 WordPress 核心测试套件的改进，向后兼容。

![](img/beaf6009c30a1234f4568464e83e2e08.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如果你从事专业的 WordPress 开发已经有一段时间了，你可能会遇到 WordPress 测试框架。这是用于测试 WordPress 插件和主题的标准代码。

设置和使用也很痛苦。

对我来说，最大的痛点之一是，如果我使用 WordPress 框架，我完全无法模仿核心 WordPress 类和函数。这意味着我要么需要[使用像 Brain Monkey](https://blog.devgenius.io/wp-mock-or-brain-monkey-which-is-the-better-mocking-library-for-wordpress-48fd89f321fd) 这样的模仿库，放弃集成测试，要么我需要为每个项目维护两个独立的 PHPUnit 配置。

有了 [Mantle TestKit](https://mantle.alley.co/testing/testkit.html) ，情况就不再是这样了。

# 什么是地幔测试套件？

[地幔测试套件](https://mantle.alley.co/testing/testkit.html)是整个[地幔](https://mantle.alley.co/)项目的一小部分。Mantle 将 Laravel 的灵活性和 WordPress 的易用性结合在一起。

正如创作者所描述的:

> Mantle 认为企业级的 WordPress 开发是可能的，应该有一个简单而令人愉快的语法。

# 如何在项目中安装 Mantle TestKit

Mantle TestKit 应该作为大多数测试的替代工具。如果你已经花时间升级你的 WordPress 测试来使用 PHPUnit 9。修改客户项目时，我遇到的唯一两个问题是。

## 前期工作

为插件或主题设置 PHPUnit 超出了本文的范围，但是有大量的[信息](https://make.wordpress.org/core/handbook/testing/automated-testing/phpunit/#workflow-2-setting-up-the-composer-environment) [out](https://tj.ie/getting-started-with-wordpress-and-unit-testing/#setup) 可供参考。其中一些已经过时了，但是如果你对这个主题感兴趣，我相信你完全有能力把它们放在一起。

谁知道呢，也许我以后会跟进一篇关于它的文章。

## 断言数组子集

PHPUnit 8 移除了断言`assertArraySubset`,我转换的客户端项目中的许多测试都在使用这个断言。最初，我试图重新组织测试用例来删除这些调用，但是最终我选择了简单地添加一个 composer 包来填充断言。

```
composer require --dev dms/phpunit-arraysubset-assert
```

从那里，我能够向我的测试添加以下行:

```
use DMS\PHPUnitExtensions\ArraySubset\ArraySubsetAsserts;class Test_Class extends Test_Case {
    use ArraySubsetAsserts;
    ...
}
```

突然，我又有了有效的断言。

我选择只将它添加到已经使用断言的测试中，因为我希望将来不再使用它，但是您也可以轻松地将它添加到一个定制的测试用例中，并从那里扩展您的测试。

*喜欢这篇文章吗？* [*成为会员*](https://travisweston.com/membership)*[*订阅*](https://travisweston.com/subscribe) *获取更多类似这样的精彩内容！**

## *设置项目以使用 Mantle TestKit*

*默认情况下，Mantle TestKit 作为 WordPress 测试框架的替代物，只是速度更快。因此，让我们看看将现有的 WordPress 插件从 WordPress 测试转换到 Mantle TestKit 的步骤。*

*我们要做的第一件事是安装 TestKit。*

```
*composer require --dev mantle-framework/testkit*
```

*测试套件现已安装！*

*现在，我倾向于在`wp-content`级别安装我的 composer，但是在哪里安装并不重要。无论我们在哪里做，我们只需要知道`wordpress-autoload.php`文件在哪里。这可以在我们的供应商目录中找到。*

*让我们创建一个全新的`bootstrap.php`文件来开始这项工作。*

*真的就这么简单。*

*最后，因为我们不再使用 WordPress 的测试框架，我们不再能够访问`\WP_UnitTestCase`，所以我们需要将我们的测试用例转换到新的 Mantle 测试用例。*

*在我们开始之前，让我们花一点时间来谈谈集成和单元测试。*

## *集成与单元测试*

*尽管测试用例的名字，WordPress 实际上并不包括任何形式的单元测试。这么说可能会有争议，但这是真的。你在 WordPress 测试框架中写的每一个测试都是一个集成测试。为什么？因为它需要你与 WordPress 核心代码库集成才能运行。*

*Mantle TestKit 允许我们做的一件事是创建真正的单元测试，并在与集成测试相同的执行中运行它们。我们可以通过扩展两个不同的测试用例来做到这一点。*

*第一个是`Mantle\TestKit\Integration_Test_Case`，顾名思义，是我们的集成测试。这个测试用例将处理安装 WordPress 核心，设置数据库，以及我们执行集成测试所需的一切。*

*当然，如果你想对情况有更多的控制，你总是可以扩展掉主`Mantle\TestKit\Test_Case`类。这仍然是一个集成测试，但是它要求您在执行之前手动执行 Mantle 安装过程。*

*第二个是`Mantle\TestKit\Unit_Test_Case`,它自动强制我们所有的单元测试在一个单独的进程中运行，而不传递任何全局变量，因为安装集成测试可能会使全局范围变得混乱。*

*作为一个最佳实践，您应该创建您自己的定制测试用例，并从这些父测试用例扩展到您自己的测试，因为您可能需要在基础之上定制您的环境。*

## *配置集成测试*

*每个 WordPress 测试都有一个共同点，那就是在安装后执行回调。这也是 Mantle 提供给我们的。如果您正在扩展上述的`Integration_Test_Case`，那么您所需要做的就是向您的引导程序添加一个名为`mantle_after_wordpress_install`的函数。*

*如果您计划从根`Test_Case`类扩展，那么您可以简单地[将这个函数作为闭包](https://mantle.alley.co/testing/testkit.html#adjusting-unit-test-bootstrap)传递给`\Mantle\install()`函数。出于本教程的目的，我假设您正在使用集成测试用例。*

## *配置单元测试*

*如果你想要单元测试，那么需要两件事。首先，您的集成测试必须使用`Integration_Test_Case`类，或者您的定制实现必须在`setUpBeforeClass`挂钩期间安装 Mantle。无论哪种方式，你都不能从你的引导中运行`Mantle\install()`——否则我们将会以一个混乱的全局范围结束，不管我们喜欢与否。*

*第二件事是你绝对必须使用`Unit_Test_Case`——或者复制功能。如果你要这么做，为什么还要使用 Mantle 呢？*

*对于我的定制单元测试类，我使用 Brain Monkey 并在最初配置所有的东西。*

*你会注意到，除了扩展 Mantle Unit_Test_Case 类之外，这只是一个如何为 Brain Monkey 配置测试用例的例子。那是因为这就是全部。*

# *我们完了*

*实际上，一旦您的测试扩展了您的新定制类，并且您已经解决了自动加载的任何错误，这就应该是让您的测试工作所需的全部了。*

*现在，如果您和我一样，已经在两个单独的 PHPUnit 配置文件中运行了这些文件，那么最后一步就是将它们合并到一个文件中。这就像把`testsuite`块从一个复制到另一个一样简单，并且确保你正在加载你的引导程序。*

*让我知道你是否转换到 Mantle TestKit，以及你如何喜欢它。*

*如果你有兴趣投稿，你可以在 Github 上找到整个 [Mantle 项目，文档可以在](https://github.com/alleyinteractive/mantle-framework)[Mantle 网站](https://mantle.alley.co/)上找到。*

*你喜欢这篇文章吗？ [*成为会员*](https://travisweston.com/membership) *阅读我写的所有东西——以及媒体上的所有内容。**

**已经是会员了？* [*订阅得到通知*](https://travisweston.com/subscribe) *我放新文章的时候。**

**全披露:Mantle 是由我工作的 WordPress 社* [*巷子*](https://alley.co/) *创建的。我们目前正在招聘，那么为什么不* [*申请与我*](https://alley.co/careers/) *一起工作呢！**