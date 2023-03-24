# Java 15 的新特性

> 原文：<https://levelup.gitconnected.com/every-feature-java-15-came-with-b94116373ee1>

## 权威指南

## JEP 的 Java 15 将帮助开发者

![](img/f70adba6e445b356518e16948a9e3262.png)

凯尔西·柯蒂斯在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# 介绍

正式发布的 Java 15 带来了一组非常好的 14 个特性。在这 14 个特性中，很少是添加的，也很少是从当前的 java 生态系统中删除/禁用的。下面是添加的功能列表

*   爱德华兹曲线数字签名算法
*   密封类(预览)
*   隐藏类
*   移除 Nashorn JavaScript 引擎
*   重新实现旧的 DatagramSocket API
*   禁用和反对有偏向的锁定
*   实例的模式匹配(第二次预览)
*   ZGC:一种可扩展的低延迟垃圾收集器
*   文本块
*   Shenandoah:一个低暂停时间的垃圾收集器
*   删除 Solaris 和 SPARC 端口
*   外部存储器访问 API(第二孵化器)
*   记录(第二次预览)
*   不赞成为删除而激活 RMI

在这篇博客中，我们将通过一些代码片段来简单介绍一下这些新特性是什么，以及如何使用它们。

# 爱德华兹曲线数字签名算法

![](img/86f1f3d52b63ef964a33673b46f058ae.png)

照片由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

Java 15 包含这个特性的原因之一是

*   与类似的签名 ECDSA 相比，由于其安全性和性能，它是一种流行的签名算法
*   缓存流行的加密库，如 OpenSSL 和 BoringSSL，它们已经支持 EdDSA。
*   目前 TLS 1.3 中允许的唯一签名

**代码片段**生成密钥对并对其进行签名

**代码片段**用于构造公钥的代码片段

更多阅读材料:[https://openjdk.java.net/jeps/339](https://openjdk.java.net/jeps/339)

# 密封类(预览)

![](img/5d276ad74b2a1ffbbabec8059bdbbac1.png)

[Natasya Chen](https://unsplash.com/@natasyachen?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

密封类的主要目标是为 Java 创建一种支持 [*代数数据类型*](https://en.wikipedia.org/wiki/Algebraic_data_type) 的方式。

当我们看 Java 类层次结构时，它允许通过继承来重用代码，但是《密封类》的作者 Brian Goetz 说，然而，这并不总是为了代码重用，而是为了模拟域中存在的各种可能性。

例如，如果名为`Shape`的类的作者可能希望只有特定的类可以扩展该形状，而不是每个类，因此`Shape`的作者可以编写代码给`Shape`的已知子类，而不需要编写代码来保护`Shape`的未知子类

“一个`sealed class`或`interface`只能由那些被允许这样做的类和接口来扩展或实现”——[https://openjdk.java.net/jeps/360](https://openjdk.java.net/jeps/360)

**例如:**

在上面的代码片段中，通过将修饰符`sealed`应用到声明中，类`Shape`被标记为密封类，并且`permits`子句指定了允许扩展密封类的类。这里只有`Circle, Rectange and Square`可以扩展`Shape`类。

**密封级+ JEP 375 模式匹配**

密封一个类的主要目的是让客户端代码推断所有允许的子类。传统上，推理子类的方法是使用 if-else 链，例如:

使用`permits`子句并让编译器知道允许的类可以用[模式匹配](https://cr.openjdk.java.net/~briangoetz/amber/pattern-match.html)扩展一个密封类，而不是用`if-else`检查一个密封类的实例，客户端代码将能够使用`switch`而不是使用 [JEP 375](https://cr.openjdk.java.net/~briangoetz/amber/pattern-match.html) 测试模式。这允许编译器检查模式是否详尽

**例如:**

更多阅读材料:[https://openjdk.java.net/jeps/360](https://openjdk.java.net/jeps/360)

# 隐藏类

![](img/b551762cc4855da77a4306114c2ad887.png)

米哈伊尔·瓦西里耶夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

隐藏类是针对 JVM 的，并不是为了以任何方式改变 java 语言。隐藏类的意图主要针对在运行时生成类并通过反射使用它们的框架。

当一个隐藏类不再可达时，它可以被卸载，或者它可以共享一个类装入器的生命周期，这样它只有在类装入器被垃圾收集时才被卸载。

**创建隐藏类的示例代码**

隐藏类创建方式的重要区别在于它在创建时获得的名称。**隐藏类不是匿名的。**类的名字可以通过`Class::getName`获取，这个名字有一个足够不寻常的形式，它有效地使这个类对所有其他类不可见。该名称由以下内容串联而成:

1.  由`ClassFile`结构中的`this_class`指定的内部形式的二进制名称(JVMS 4.2.1)，比如说`A/B/C`；
2.  `'.'`人物；和
3.  由 JVM 实现选择的非限定名(JVMS 4.2.2)。

例如，它可能看起来像`com.rockey.Test/0x0000000800b94440`

更多阅读材料:[https://openjdk.java.net/jeps/371](https://openjdk.java.net/jeps/371)

# 重新实现旧的 DatagramSocket API

![](img/7aba538e06fcf193d95bf4fc0b577035.png)

马库斯·温克勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

这个特性重新实现了`java.net.DatagramSocket`和`java.net.MulticastSocket`API，因为它们的实现可以追溯到 JDK 1.0，该版本混合了 Java 和 C 语言，对于作者来说很难维护。作者的想法是这些新的实现将很好地适应虚拟线程(这是作为[项目织机](https://openjdk.java.net/projects/loom)探索的)。这些重新实现是默认启用的，通过直接使用选择器提供者的平台默认实现(`sun.nio.ch.SelectorProviderImpl`和`sun.nio.ch.DatagramChannelImpl`)，为数据报和多播套接字提供不可中断的行为

更多阅读材料:[https://openjdk.java.net/jeps/373](https://openjdk.java.net/jeps/373)

# 实例的模式匹配(第二次预览)

![](img/1b58c5189ca78b792653d3625f695da8.png)

[劳丽莎·迪肯](https://unsplash.com/@laurisamisa?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

有了这个 JEP，Java 可能会开始采用*模式匹配*就像从 Haskell 到 C#的许多语言一样，因为其简洁和安全而采用了模式匹配。模式匹配允许简洁地表达目标的期望“形状”(*模式*)

这是 Java 编程语言的一个增强，为`instanceof`操作符提供了*模式匹配*。[模式匹配](https://cr.openjdk.java.net/~briangoetz/amber/pattern-match.html)允许程序中的公共逻辑，即从对象中有条件地提取组件，更简洁、更安全地表达。

这背后的主要驱动因素几乎每个程序都有一种逻辑，这种逻辑结合了测试表达式是否具有某种类型的结构，然后有条件地提取其状态的组成部分以供进一步处理。

**示例:铸造习语的实例**(不使用模式)

这里发生了三件事，所以我们可以使用字符串值:

*   一个测试(`obj`是不是一个`String`？)
*   转换(将`obj`转换为`String`)和
*   新局部变量的声明(`s`)

它是乏味的；没有必要同时进行类型测试和强制转换。但最重要的是，这种重复为错误悄悄潜入程序提供了机会。为了使用模式简化上面的代码，我们首先需要了解什么是模式“一个*模式*是(1)一个可以应用于目标的*谓词*和(2)一组*绑定变量*的组合，只有谓词成功应用于目标时，才能从目标中提取这些变量。”

上面的代码现在可以写成

**示例:简化上述代码**(s 的范围限于 if 块)

如果`obj`是`String`的一个实例，那么它被转换为`String`，并被赋给绑定变量`s`。绑定变量在`if`语句的真块范围内，而不在`if`语句的假块范围内，但是变量的这个范围依赖于包含表达式和语句的语义。例如，在下面的代码中，将根据条件评估将 s 的范围扩展到 else 块

**示例:(s 的范围扩展到 else 块)**

附加阅读材料:[https://openjdk.java.net/jeps/375](https://openjdk.java.net/jeps/375)

# ZGC:一种可扩展的低延迟垃圾收集器

![](img/46d5fbe0b541cc58c10d66ef73dcae8d.png)

照片由[将](https://unsplash.com/@elevatebeer?utm_source=medium&utm_medium=referral)提升到 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上

这个 JEP 本质上把 Z 垃圾收集器从一个实验性的特性变成了一个产品特性。在 Java 11 JDK 中引入这一 JEP 后，社区对此给予了积极的反馈，作者增加了许多特性和增强。哪些是

*   并发类别卸载
*   取消提交未使用的内存( [JEP 351](https://openjdk.java.net/jeps/351) )
*   最大堆大小从 4TB 增加到 16TB
*   最小堆大小减少到 8MB
*   `-XX:SoftMaxHeapSize`
*   支持 JFR 泄漏分析器
*   支持类数据共享
*   有限且不连续的地址空间
*   支持将堆放在 NVRAM 上
*   提高 NUMA 意识
*   多线程堆预接触

此外，现在支持所有常用的平台:

*   Linux/x86_64 ( [JEP 333](https://openjdk.java.net/jeps/333) )
*   Linux/aarch64 ( [8214527](https://bugs.openjdk.java.net/browse/JDK-8214527) )
*   视窗( [JEP 365](https://openjdk.java.net/jeps/365) )
*   macOS ( [JEP 364](https://openjdk.java.net/jeps/364) )

通过设置启用 ZGC

```
-XX:+UnlockExperimentalVMOptions -XX:+UseZGC
```

# 文本块

![](img/5ffd3b927c7cf13ac446ab609d310970.png)

由 [Gustave Robinot](https://unsplash.com/@gustaverobs?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

这个 JEP 将允许在 Java 语言中添加文本块。文本块是多行字符串文字，它避免了转义序列的必要性，并以可预测的方式自动格式化字符串。不仅如此，还向 string 添加了新的方法来支持文本块

*   `String::stripIndent()`:用于去除文本块内容中附带的空白
*   `String::translateEscapes()`:用于翻译转义序列
*   `String::formatted(Object... args)`:简化文本块中的值替换

# HTML 示例

*使用“一维”字符串文字*

*使用“二维”文本块*

SQL 示例

*使用“一维”字符串文字*

*使用“二维”文本块*

# Shenandoah:一个低暂停时间的垃圾收集器

这个 JEP 从根本上改变了谢南多厄 Garabage 收藏家从实验功能到产品功能

启用这个垃圾收集命令使用的是`java -XX:+UseShenandoahGC`

# 删除 Solaris 和 SPARC 端口

此 JEP 删除了对 Solaris/SPARC、Solaris/x64 和 Linux/SPARC 端口的源代码和构建支持。放弃对 Solaris 和 SPARC 端口支持的主要原因是，OpenJDK 社区的贡献者可以加快新特性的开发，推动平台向前发展

# 外部存储器访问 API(第二孵化器)

![](img/e288f4588b26771d5ed9b6095e172d89.png)

照片由 [timJ](https://unsplash.com/@the_roaming_platypus?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这个特性将引入一个 API，允许 Java 程序安全有效地访问 Java 堆之外的外部内存。

很多 Java 程序访问外来内存，比如 [Ignite](https://apacheignite.readme.io/v1.0/docs/off-heap-memory) 、 [mapDB](http://www.mapdb.org/) 、 [memcached](https://github.com/dustin/java-memcached-client) 、 [Lucene](https://lucene.apache.org/) ，Netty 的 [ByteBuf](https://netty.io/wiki/using-as-a-generic-library.html) API。使用 15 版之前的 Java API 访问外部存储器有三种方法，它们是

*   [字节缓冲 API](https://docs.oracle.com/en/java/javase/14/docs/api/java.base/java/nio/ByteBuffer.html)
*   [不安全的 API](https://hg.openjdk.java.net/jdk/jdk/file/tip/src/jdk.unsupported/share/classes/sun/misc/Unsafe.java)
*   使用 JNI

但是当访问外部存储器时，有一个两难的问题:如果他们应该选择一个安全但有限(可能效率较低)的路径，如`ByteBuffer` API，或者他们应该放弃安全保证，拥抱危险和不受支持的`Unsafe` API，所以这个 JEP 旨在通过引入三个主要的抽象:`MemorySegment`、`MemoryAddress`和`MemoryLayout`，为外部存储器访问提供安全、受支持和高效的 API 解决方案:

*   `MemorySegment`用给定的空间和时间界限对连续的存储区域进行建模。
*   一个`MemoryAddress`模拟一个地址。通常有两种类型的地址:一个*检查过的*地址是给定内存段内的偏移量，而一个*未检查过的*地址是其空间和时间界限未知的地址，就像从本机代码中不安全地获得的内存地址一样。
*   `MemoryLayout`是内存段内容的程序描述。
*   创建本机内存段的示例代码片段，这将创建一个 100 字节的内存缓冲区

```
try (MemorySegment segment = MemorySegment.allocateNative(100)) {
   ...
}
```

**示例代码:**

补充阅读:[https://openjdk.java.net/jeps/383](https://openjdk.java.net/jeps/383)

# 记录(第二次预览)

记录首先是在 Java 14 中引入的，这是 JEP 引入的一个特性，支持密封类型、本地记录、记录上的注释。记录是充当不可变数据的透明载体的类。记录可以看作是*名义元组*。

对`records`特性的主要动机是基于“Java 太冗长”或“太多礼节”的抱怨。对于一些值来说，仅仅是不可变的*数据载体*的类可以作为一个很好的例子来说明这一点，正确地编写一个数据载体类需要大量低价值的、重复的、容易出错的代码:构造函数、访问函数、`equals`、`hashCode`、`toString`等等

**示例:**(记录前)

省略一些方法，比如`equals`，会导致对象比较过程中的错误行为，并且难以调试。

记录旨在解决其中一些问题。它们是 java 语言中的一种新的类，其目的是声明一小组变量将被视为一种新的实体。一条记录声明它的*状态*——一组变量——并提交给一个与该状态匹配的 API

**示例:**(使用记录)

```
record Message(String message, long dimensionl) { }
```

**记录解剖**

`record`指定名称、标题和正文。标题列出了记录的*组成部分*，它们是构成记录状态的变量

例子

```
record Point(int x, int y) { }
```

这里的名字是`Point`

表头是`x`和`y`

主体是花括号中的内容

`record`默认带有四个标准成员

*   `public`与组件具有相同名称和返回类型的访问器方法，以及与组件具有相同类型的`private` `final`字段
*   标准构造函数
*   `equals`和`hashCode`方法
*   `toString`

## 记录和密封类型

记录和密封类型的组合有时被称为 [*代数数据类型*](https://en.wikipedia.org/wiki/Algebraic_data_type) 。

记录允许表示*产品类型*，封存类型允许表示*汇总类型*

**例如:**

# 不赞成为删除而激活 RMI

因为分布式系统基于 web 技术已经有很长时间了。关于穿越防火墙、过滤请求、认证和安全性的问题都在 web 服务领域得到了解决。资源的惰性实例化由负载平衡器、编排和容器来处理。这些机制都不存在于分布式系统的 RMI 激活模型中，Java 团队没有发现任何使用 RMI 激活的新应用程序。

RMI 激活在 [Java 8](https://docs.oracle.com/javase/8/docs/api/java/rmi/activation/package-summary.html#package.description) 中是可选的，在 Java 15 中被删除了

# 禁用和反对有偏向的锁定

这是偏向锁定的遗留同步优化，维护成本很高，因此 JDK 团队默认禁用它。

Hashtable 和 Vector 等传统集合 API 利用了这些性能提升，但在当前趋势下，大多数较新的应用程序通常使用非同步集合，如`HashMap`和`ArrayList`，甚至是 Java 5 中引入的更高性能的并发数据结构，用于多线程场景

有了这个 JEP，HotSpot 启动时将不再启用偏置锁定，除非在命令行上设置了`-XX:+UseBiasedLocking`。

更多阅读材料:[https://openjdk.java.net/jeps/374](https://openjdk.java.net/jeps/374)

# 移除 Nashorn JavaScript 引擎

这是从 Java 中移除的，它不应该影响方式`javax.script` API

更多阅读材料:[https://openjdk.java.net/jeps/372](https://openjdk.java.net/jeps/372)