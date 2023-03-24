# 不要盲目地实现可序列化接口

> 原文：<https://levelup.gitconnected.com/do-not-blindly-implement-the-serializable-interface-12b596d49687>

## 消除常见误解的指南

![](img/d9e79cf6b2690fa8a785c88a090e90e3.png)

[本·怀特](https://unsplash.com/@benwhitephotography?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

> "没有理由在你编写的任何新系统中使用 Java 序列化."——约书亚·布洛赫。

1997 年，Java 中就有了序列化。其中一个众所周知的机制是通过网络发送对象的状态。虽然听起来很方便，但弊远远大于利。

幸运的是，现在有了我们称之为跨平台结构化数据表示的替代方案。虽然有些人称之为序列化系统，但我们避免使用这个术语，以免与 Java 序列化混淆。

在本文中，我们将讨论序列化。接下来，我们将仔细看看它的一个缺点。最后，我们将快速浏览备选方案，以澄清一个常见的误解。

# **什么是序列化？**

简单来说，**序列化就是把一个对象变成一个字节流。给定这个字节流，我们可以做几件事:通过网络发送它，将它存储在数据库中，保存到文件中，等等。一旦一切就绪，我们总是可以反序列化这个字节流，将其转换回对象。**

为了进行演示，让我们尝试模拟将对象列表序列化到一个文件中。之后，使用反序列化，让我们将其转换回对象列表。

首先，让我们创建一个`Person`类并实现`Serializable`接口。

接下来，让我们快速创建一个`PersonSerializationUtil`:

最后，让我们创建一个简单的测试来模拟 person 类型列表的序列化和将其字节流反序列化回对象列表。

正如我们所看到的，我们已经成功地使用文件执行了序列化和反序列化。如前所述，有了字节流，我们还可以通过网络发送它或将其存储在数据库中。不管怎样，序列化和反序列化的过程是相似的。

# **使用序列化的缺点**

使一个类可序列化确实不费吹灰之力。相比之下，它更有理由保持谨慎，因为其长期成本往往是有害的。话虽如此，让我们来看看实现`Serializable`的主要成本之一。

> 实现`Serializable`降低了更改类实现的灵活性。

引入更改可能会导致反序列化时出现`InvalidClassException`。

再次使用我们之前创建的`Person`类进行模拟，让我们尝试添加一个`middleName`字段:

现在，让我们试着反序列化前一个文件中的字节流:

在控制台中运行测试时，我们可以看到在运行时遇到了 InvalidClassException:

```
org.apache.commons.lang3.SerializationException: java.io.InvalidClassException: com.emyasa.Person; local class incompatible: ...
```

另一方面，一般来说，盲目地反序列化数据是危险的，可能会导致安全问题。

# **电子监管的替代方案**

让我们来看看两个领先的*跨平台结构化数据表示* : *JSON* 和*协议缓冲区*。如前所述，这些有时也被认为是*序列化系统*。这通常会导致一个常见的误解，即暗示需要序列化*数据传输对象*。

相比之下，*跨平台结构化数据表示*和 *Java 序列化*是相互独立的。例如，使用 JSON 就足以进行浏览器-服务器通信。因此，在这个用例中，不需要对*数据传输对象*进行 Java 序列化。

像往常一样，这篇文章的完整源代码可以在 [Github](https://github.com/emyasa/medium-articles/tree/master/core-concepts) 上找到。

如果您正在寻找另一篇与 Java 相关的有趣文章，请随意查看:

[](/never-swallow-exceptions-5e01c6bb5b8a) [## 永远不要吞下异常

### 避免不必要的头痛——无论是象征性的还是字面上的

levelup.gitconnected.com](/never-swallow-exceptions-5e01c6bb5b8a)