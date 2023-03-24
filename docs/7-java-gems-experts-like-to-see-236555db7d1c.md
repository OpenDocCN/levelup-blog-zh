# 7 Java Gems 专家喜欢看的

> 原文：<https://levelup.gitconnected.com/7-java-gems-experts-like-to-see-236555db7d1c>

## 这里有 7 个 Java 瑰宝，可以帮助有经验的开发人员交付优秀的代码

![](img/c941e8ff550db165f97c5976fa8f5f65.png)

Lexica.art

经验丰富的 Java 开发人员现在应该知道这 7 种 Java 宝石了。

所以任何有经验的开发人员都应该尝试一下。让我们开始吧。

# 1.意识

***你可以使用这个图书馆来等待物品。咄。***

***那么这有什么用例呢？你需要在哪里等待什么？***

***我的一个用例是测试卡夫卡出版。***

您可以发送消息、处理和提交相关数据。但是你怎么知道某个东西是否被写了呢？ Kafka 发布了一条消息，但是你不能马上检查数据是否被提交到数据库中。

你不能做这样的事:

```
kafkaTemplate.send("topic", new MessagePayload("123"));
assertTrue(dbService.getMessage("123").isPresent());
```

这就是你需要意识的原因。您可以在一定的超时时间内等待结果。

# 2.原始消息中的字段掩码

网飞利用了这一特性。你可以在这里阅读两部分和[在这里](https://netflixtechblog.com/practical-api-design-at-netflix-part-2-protobuf-fieldmask-for-mutation-operations-2e75e1d230e4)。

原始信息只是故事的一部分。而有了 [protobuf-java](https://mvnrepository.com/artifact/com.google.protobuf/protobuf-java-util) 和 [protobuf-java-util](https://mvnrepository.com/artifact/com.google.protobuf/protobuf-java) 这样的库，可以做很多事情。

您可以将它们用作 even REST API 的[有效负载，或者在需要时创建一个](https://www.baeldung.com/spring-rest-api-with-protocol-buffers) [gRPC 网关](https://github.com/grpc-ecosystem/grpc-gateway)代理。此外，您可以创建 [grpc-web](https://github.com/grpc/grpc-web) 客户端并调用 grpc 服务。但是 proto 的主要目的是 gRPC 服务。

回到野外面具。您可以使用字段掩码组合消息，并从请求中过滤出您需要的内容。

这在某种意义上确实类似于 GraphQL 查询。但重要的是，您只发送字段掩码中定义的内容。

***这个功能还有什么好处？***

您将删除多余的 dto。因为您可以发送字段掩码并获得部分消息，所以您不需要 dto。更少的代码意味着更好的维护。

# 3.测试容器

另一个用 Java 旋转测试容器的很棒的库。

所有需要做的就是编写 [Java 代码来旋转容器](https://github.com/testcontainers/testcontainers-java/blob/main/examples/spring-boot/src/test/java/com/example/AbstractIntegrationTest.java)。

```
// from testcontainers docs
@Testcontainers
class MixedLifecycleTests { // will be started before and stopped after each test method
    @Container
    private PostgreSQLContainer postgresqlContainer = new PostgreSQLContainer()
        .withDatabaseName("foo")
        .withUsername("foo")
        .withPassword("secret").start(); @Test
    void test() { assertThat(postgresqlContainer.isRunning()).isTrue();
    }
}
```

测试容器使得集成测试更加容易。短暂的，所以你不会得到任何古怪的测试。

# 4.Mapstruct

如果你需要自定义的映射器，这是可以选择的库。Mapstruct 从定义中生成所有映射器。定义是开放的，可以扩展或定制。

***关于 mapstruct 有什么帮助？*** 映射嵌套结构。您还可以将嵌套的 proto 消息映射到 POJOs。或者您可以继承一个相反的配置，只编写映射的一面。

下面是一个将 proto 消息映射到 POJOs 的例子。`TargetDTO`是 POJO，`SourceDto`是 proto 消息。

您还可以使用 mapstruct 自定义如何生成映射器。例如，对于原型消息，您可以通过简单的配置使用加法运算。

你会得到下面的代码。

```
if ( dto.getSecondLevelDTOList() != null ) {  
    for ( SecondLevelDTO secondLevelDTOList : dto.getSecondLevelDTOList() ) {  
        firstLevelDto.addDto( secondLevelProtoMapper.map( secondLevelDTOList ) );  
    }  
}
```

# 5.巴泽尔

Bazel 是一个用 Java 构建的构建系统。

用对了更快。此外，您可以在同一个项目中使用多种语言，Bazel 也会处理这个问题。

***还可以将 Bazel 与 Guice 结合使用，用于更轻量级的项目。***

此外，如果使用微服务，Bazel 是正确的选择。您可以限制可见性，拥有更细粒度的构建结构，并创建自己的构建规则。

Bazel 使用[远程缓存](https://bazel.build/docs/remote-caching)。输出可以被重用、缓存，并进行更快的构建。

***对于某些球队来说，巴泽尔未必是正确的选择。***

远程缓存对 Kubernetes 团队来说是一种威慑。他们每次都需要新鲜的产品。即便如此，对于常见的用例来说，这种方法非常有效。

您还可以使用 Starlark 和元编程创建一个宏来编写构建规则。这不仅有助于代码重用，还能生成定制的构建规则。

Bazel 使构建更加通用、可配置和透明。

# 6.来自番石榴的 TreeRangeMap

如果你需要你的树形图的键是区间，那么 [TreeRangeMap](https://javadoc.io/doc/com.google.guava/guava/28.1-jre/com/google/common/collect/TreeRangeMap.html) 就是你要去的类。

您还可以支持 TreeMap 提供的一切，排序键和键操作。

那么 TreeRangeMap 有哪些好处呢？

*   您可以按间隔搜索某个范围
*   您可以为特定时间间隔添加新值
*   添加重叠时间范围时，TreeRangeMap 会拆分间隔
*   用[子 RangeMap](https://javadoc.io/doc/com.google.guava/guava/28.1-jre/com/google/common/collect/TreeRangeMap.html#subRangeMap-com.google.common.collect.Range-) 可以得到某个区间的 RangeMap 切片

在这个结构[上的大多数操作或者是常数或者是对数时间复杂度。](https://stackoverflow.com/a/20290550/5999670)所以所有的添加、放置等。都是在对数时间内完成的。但更重要的是，如果你在处理时间范围，这是正确的结构。

# 7.JUnit 5 和参数化测试

随着 JUnit5 的出现，参数化测试变得更好。

我们现在可以使用像`@CsvSource`和`@ValueSource`这样的结构。这些可以满足我们大部分的测试需求。此外，您可以定义代码中的每个参数，并配置自定义测试显示名称。

对于自定义名称，我们可以使用`[@DisplayName](https://junit.org/junit5/docs/5.5.0/api/org/junit/jupiter/api/DisplayNameGeneration.html)`或`[@DisplayNameGeneration](https://junit.org/junit5/docs/5.5.0/api/org/junit/jupiter/api/DisplayNameGeneration.html)`。虽然 DisplayName 已经足够了。但是也支持生成自定义显示名称。

Mike 回顾了进度以及 6 月 5 日带来了什么[这些例子](https://github.com/mikemybytes/the-unknowns-of-junit5/tree/e67db691e877481efa0f5f156270b3f8506ca79d/src/test/java/com/mikemybytes/junit5/pricing)。

下面是第一个例子:

下面是 JUnit5 对前面代码块的处理:

JUnit5 改进的另一件事是异常测试。

以前你会有`expected`注解。但是你不能测试这个方法抛出了什么异常。或者是否需要测试更多的异常。

***新版本的 JUnit 使用 assertThrows***

您可以在调用可执行文件后捕获异常。自定义异常(第一个参数)确认预期的异常类型。可执行文件第二个参数抛出异常。错误消息，第三个参数，如果异常没有发生，则打印。