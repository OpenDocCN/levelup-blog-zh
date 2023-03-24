# 给我看看 Java 的钱！

> 原文：<https://levelup.gitconnected.com/show-me-the-java-money-d3dcc3a75f8a>

## 深入了解 Java Money API

![](img/503d0d6b725ddb535c50eafb53ca0b83.png)

Java 是构建业务应用程序的一种非常成功的语言，部分原因是它不断扩大的基础包含了一些库，使得处理“人类”约定变得更加容易。日期和时间就是一个很好的例子。曾几何时，在大多数编程语言中，日期只是一个非常大的数字，用来度量七十年代某个时间点的秒数。这几乎不足以代表人类阴谋集团周围的时间，包括时区，12 小时与 24 小时时钟，月，周，周末，以及各种国际惯例。Java 8 版本通过提供一个综合的时间包解决了很多这样的问题，这个时间包包含了人类的日期/时间概念以及经常需要的复杂计算和转换。

金钱是另一个领域，人类习俗的非理性给程序员带来了很多头痛。Java Money API (JSR 354) 正试图缓解其中的一些问题。已经有几个教程展示了如何使用这个 API，所以我将重点演示如何在一个半真实的 Spring Boot 应用程序中使用它，而不是回顾基础知识。

# 构建应用程序

示例应用程序提供了一个 REST 端点来创建发票。后端为客户收取费用，并将其相加以创建发票。另外，费用是以不同的货币计算的。合同规定了发票中出现的货币。此外，合同规定了适用于总金额的可选折扣。示例应用程序可以在 GitHub 上找到:[https://github.com/algorythmist/java-money-spring-poc](https://github.com/algorythmist/java-money-spring-poc)。

## 实体模型和持久性

具有货币意识的实体(如费用)应该包含货币金额引用，而不是单独的金额和货币字段。这种封装将保证基于货币的计算的完整性:

正如注释所示，这是一个由关系数据库支持的 JPA 实体。假设 99%的情况下持久化机制是 Hibernate，我们需要一个自定义的`UserType`来告诉 Hibernate 如何将这个值与数据库相互转换。在这里可以找到这样一个自定义类型的示例实现:[https://github . com/algorythmist/Java-money-spring-POC/blob/master/src/main/Java/com/tecacet/money/repository/monetaryamountusertype . Java](https://github.com/algorythmist/java-money-spring-poc/blob/master/src/main/java/com/tecacet/money/repository/MonetaryAmountUserType.java)

## 货币计算

货币可以被实数乘除，但不能被其他货币对象乘除。相反，相同货币的钱可以相加和相减。尝试添加不同货币的货币会导致异常，如下测试所示:

Java Money API 并没有选择一个特定的类来实现`MonetaryAmount. MonetaryAmount.getNumber()`的 amount 部分，而是返回一个`NumberSupplier`,后者又返回一个可以转换为 Number 实现的`NumberValue`。例如，如果我们想用`BigDecimal`来表示数量，下面是提取它们的方法

在一个货币数字化的世界里，一美元(或其他货币)的任何部分都应该是可兑换的，就像在某些交易所购买股票的部分一样。令人惊讶的是，仍然有实物货币被兑换，所以任何美元交易都必须四舍五入到美分的数字，也就是说，两位小数。其他世界货币也是如此，只是有些货币可以四舍五入到 3 位，有些货币必须四舍五入到整数。

Java Money 知道每种货币的正确取整，并且能够适当地取整。例如，巴林第纳尔(BHD)的适当四舍五入为 3 位，因为每第纳尔等于 1000 菲尔。

## 汇率和供应商

货币偶尔会被创造和毁灭，但频率不高。另一方面，汇率在贪婪的投机市场的推动下不断变化。为了使汇率(或多或少)保持最新，Money API 必须链接到一个汇率提供者。

Moneta 实现附带了一个提供者列表，当请求汇率时，会按顺序联系这些提供者。

到这些提供者的连接需要相当长的时间来初始化，并且在运行测试时绝对应该避免，因为这将导致显著的速度下降(稍后将详细介绍测试)。我的猜测是，大多数应用程序将希望使用自己选择的提供者，而不是内置的提供者。好消息是实现一个定制的提供者非常容易。

下面是一个定制提供者的实现，它通过由 java-finance-api 提供的 Java 客户端使用了 [Grandtrunk](https://currencies.apps.grandtrunk.net/) 汇率 API:

## 把所有的放在一起

让我们结合这些概念来构建发票应用程序:

合同在第 11 行指定了发票的货币。在第 12 行，我们获得了该货币的 CurrencyConversion 实例。在第 13–16 行，我们对费用进行了汇总，确保将每笔费用都转换为联系货币(第 15 行)。

在第 18 行，我们做了一些货币算术来减去折扣率。最后，在第 19 行，我们将最终金额四舍五入到货币指定的正确小数位数。

## 测试

正如我之前提到的，默认的汇率提供程序需要很长时间来启动，应该在测试中避免。在任何情况下，使用真实的汇率提供商都不会产生可重复的结果，因为兑换率一直在变化。

幸运的是，正如我们所看到的，实现汇率提供程序非常容易，所以我们可以专门为测试创建一个:

为了强制测试使用这个提供者，我们可以创建一个特定于测试概要文件的配置:

# 最后的想法

Java Money API 为构建多货币应用程序提供了丰富的特性。有几个方面我不喜欢。一个是`CurrencyUnit` 不同于 Java 现有的`Currency` 类。技术原因很清楚:更多的属性是必要的，为了新的 API 而扩展`Currency` 是没有意义的。然而，我希望它们能更好地互操作。我还发现 API 的某些部分在语法上有点麻烦，但还不至于让人望而却步。

API 的通用性允许扩展到传统货币之外，它也可以用于加密货币。总的来说，这似乎是一个非常健壮的规范。我建议使用它而不是经历痛苦来开发一个本土的解决方案——当然，除非有一个非常好的理由这样做。

这个例子的源代码可以在 https://github.com/algorythmist/java-money-spring-poc 的[中找到。在](https://github.com/algorythmist/java-money-spring-poc)[https://github . com/JavaMoney/JSR 354-ri/blob/master/Moneta-core/src/main/asciidoc/user guide . adoc](https://github.com/JavaMoney/jsr354-ri/blob/master/moneta-core/src/main/asciidoc/userguide.adoc)中可以找到 Moneta 实现的全面指南