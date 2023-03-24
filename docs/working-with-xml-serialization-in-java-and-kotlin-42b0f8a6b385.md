# 在 Java 和 Kotlin 中使用 XML 序列化

> 原文：<https://levelup.gitconnected.com/working-with-xml-serialization-in-java-and-kotlin-42b0f8a6b385>

![](img/6a175460a2fe2e07c6deaeae8a21f70e.png)

由[布鲁克·拉克](https://unsplash.com/@brookelark?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

在基于 Java/Kotlin 的应用程序中，您可能遇到过必须使用 XML 的情况。您需要操作 XML 并返回对请求的响应或写入文件。您可能有一个旧的应用程序，它仍然支持一些需要序列化 XML 字符串的遗留 API。使用 XML 的方法有很多。在本文中，我将分享一些使用 XML 的更好的方法。

## 先决条件

*   爪哇 LTS 17 岁以上
*   [可选] Kotlin 1.6+，几个例子将在 Kotlin
*   [可选]智能，您可以使用您喜欢的 IDE

## **设置**

我的编码示例也包括 Kotlin 示例。因此，我将创建一个也支持科特林的跳趾。选择什么样的框架完全取决于你自己。给定的样品也没有跳靴工作。对于这个演示，您可以从[这个链接](https://start.spring.io/#!type=maven-project&language=kotlin&platformVersion=2.7.0&packaging=jar&jvmVersion=17&groupId=dev.dechipher.xml&artifactId=xml-demo&name=xml-demo&description=Demo%20app%20for%20xml%20writing%20in%20Kotlin%20and%20Java&packageName=dev.dechipher.xml.xml-demo&dependencies=web)生成一个启动项目。只需下载 [xml-demo](https://start.spring.io/#!type=maven-project&language=kotlin&platformVersion=2.7.0&packaging=jar&jvmVersion=17&groupId=dev.dechipher.xml&artifactId=xml-demo&name=xml-demo&description=Demo%20app%20for%20xml%20writing%20in%20Kotlin%20and%20Java&packageName=dev.dechipher.xml.xml-demo&dependencies=web) zip 文件并解压到某个地方。通常，提取大约需要 1-2 分钟。下载完成后，您可以删除***xmldemoapplication . kt***或将其重命名为***XmlDemoApplication.java。***

![](img/ad8d2d7d381179f0efd03f3f8e2db9d4.png)

照片由 [Gia Oris](https://unsplash.com/@giabyte?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

让我们添加第一个代码。

**说明:**在上面的代码中，我刚刚用`@RequestMappping`创建了一个`@RestController`。

**输出:**尝试检索数据

```
curl http://localhost:8080/xml?name=Deepak<name>Deepak</name>
```

创建一个 Kotlin 文件**XML demkt . kt .**在 Kotlin 文件中添加下面的代码

**输出:**

```
curl http://localhost:8080/kt/xml?name=Deepak<name>Deepak</name>
```

## 1.使用文本块或字符串模板

在 Java 15 中，Java 引入了一个新特性[文本块](https://docs.oracle.com/en/java/javase/15/text-blocks/index.html)。如果使用 Java 15 及以上版本，可以使用 TextBlock 编写多行字符串。这是编写 XML 模板的良好开端。

TextBlock 面临的最大挑战是它不支持文本块中的模板。我的意思是你不能在 TextBlock 中使用变量和值。所以你必须使用像**格式化**这样的实用方法来给变量赋值。科特林的情况并非如此。让我们来看看科特林代码。

**请求:**

```
## Javacurl -X POST http://localhost:8080/xml -H 'Content-Type: application/json' -d '{    "age": 20,    "name": "Stefanie Sampson",    "company": "INTERFIND",    "email": "stefaniesampson@interfind.com",    "address": {      "street": "Coleman Street, 683",      "city": "Spelter",      "state": "Marshall Islands",      "zip": 3748    }  }'## Kotlin URL
http://localhost:8080/kt/xml
```

## 2.使用模板引擎

编写和操作模板字符串既耗时又冗长。你可以通过使用模板引擎来避免这种情况，比如[小胡子](https://github.com/spullara/mustache.java)或[百里香叶](https://www.thymeleaf.org/)。任何模板引擎的工作原理都是一样的。您需要首先解析模板并编译它。稍后，您可以使用编译后的对象来生成字符串值。
我将展示小胡子引擎中的工作示例。设置小胡子引擎比百里香简单得多。

**Mustache 模板:**在 resource 文件夹中创建一个`person-response.mustache`文件。在 Mustache 中，从资源文件夹中读取模板非常简单。

**科特林版本:**

**Java 版本:**

**请求:**

```
## Kotlin curl -X POST http://localhost:8080/kt/template -H 'Content-Type: application/json' -d '{    "age": 20,    "name": "Stefanie Sampson",    "company": "INTERFIND",    "email": "[stefaniesampson@interfind.com](mailto:stefaniesampson@interfind.com)",    "address": {      "street": "Coleman Street, 683",      "city": "Spelter",      "state": "Marshall Islands",      "zip": 3748    }  }'## Java URL
http://localhost:8080/template
```

您可能已经注意到，现在的代码看起来非常类似于 Java 和 Kotlin。使用模板引擎使代码更易读、更简单。然而，同时维护模板和 POJOs 可能是乏味和多余的。如果数据或模板发生任何变化，我们必须同时更改模板和 POJO。模板引擎解析整个模板，也会降低应用程序的性能。

## 3.使用 Jackson 序列化

您可能已经注意到 Springboot 提供了现成的 JSON 序列化。Springboot 内部使用 [FasterXml](https://github.com/FasterXML/jackson) Jackson 模块。Jackson 模块是一个非常方便的框架，可以与 JSON 和 XML 数据类型一起使用。我们可以轻松地配置 Springboot 应用程序来返回 XML 响应。为此，我们只需要在`@RequestMapping`注释中添加一个**生产**标签。要将 POJO 标记为 XML 响应，我们还需要用`@JacksonXmlRootElement`注释标记根类。

**注意:** Java 不需要任何额外的配置。但是，如果您使用的是 Kotlin 数据类。您需要在对象映射器中注册 Kotlin 模块。

**Java 示例:**

**科特林样本:**

如果没有使用 Springboot 框架，可以直接使用 XML mapper 将 POJO 转换成 XML 字符串。

```
// Javaperson.name = "[Updated] %s".formatted(person.name);
return new XmlMapper().writeValueAsString(person);// KotlinXmlMapper().writeValueAsString(p.copy(name = "[Updated] ${p.name}"))
```

**注意:**您应该创建 XmlMapper 的单个实例，以避免内存泄漏。

## 结论

这些是处理 XML 字符串的几种方法。如果您正在寻找一种更具创新性和声明性的方式来编写 XML。你可以看看 Kotlin DSL 库，比如 [kotlin-xml-builder](https://github.com/redundent/kotlin-xml-builder) 。与其他 XML 相比，我更喜欢使用 Jackson XML。因为它非常易于使用和理解。

## 源代码

您可以从我的要点页面[下载所有文件，使用 Java 和 Kotlin](https://gist.github.com/deepakshrma/b7ef7d9a9ccf94ff6150bd1ddd9cdd8b) 处理 XML 序列化和响应。