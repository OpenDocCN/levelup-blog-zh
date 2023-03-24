# 关于 Spring Boot 档案管理的建议

> 原文：<https://levelup.gitconnected.com/spring-profiles-suggestion-1090b750d181>

![](img/811357e4de30b83d2170391a235c1713.png)

照片由[德里克故事](https://unsplash.com/@derekstory?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/train?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 介绍

Spring Boot 通过 Spring 所谓的配置文件为管理不同的环境提供了开箱即用的支持；开发、试运行、测试、生产等。通常，每个概要文件会从共享的`application.properties`文件加载共享的变量和配置，然后从特定于环境的文件加载，例如`application-dev.properties`。

# 问题

通常情况下，非生产环境。开发、暂存、测试、共享配置仅在日志记录级别等方面与生产有所不同；通常，您会在开发、试运行和测试时将日志记录设置为 debug，但会将其限制为生产信息。这可能会给我们留下跨多个文件的重复(比如复制和粘贴)配置，重复的代码通常是潜在改进的标志。

# 我们能做什么

考虑到环境配置是如何在 Spring Boot 中加载的，人们可能会将这样的配置保存在共享的`application.properties`中，然后在生产环境`application-prod.properties`中覆盖它们。这确实解决了重复的问题，但它让“共享的”`application.properties`处于一种奇怪的状态，它拥有所有共享的配置，但不是真的；其中一些配置将被覆盖。更重要的是，这种方法有将这些配置意外“泄露”到生产中的风险。

![](img/8a84b0adf8c6bae51a6c19f14a444cb5.png)

Foter.com[的照片](https://foter.com/re5/11623b)

# 这个建议

虽然前面的解决方案是可行的，而且有人可能认为这是值得鼓励的(否则，为什么 Spring profiles 会以这种方式工作)，但是一种更干净的方法是引入一个`non-prod` profile，它被加载到每个非生产环境中。这种方法实现了以下目标:

1-为所有环境中的*共享属性保留真正的`application.properties`文件。*

2-明确显示用于非生产开发或测试目的的配置。

3-最小化重复配置的数量。
当需要为 Docker orchestrators(如 Kubernetes、Elastic Beanstalk 等)提供环境变量时，这种优势在部署管道的下游尤其明显

# 细节

对于`dev`、`staging`和`prod`剖面，如何准确地实现这一建议的细节可以概括为:

1-创建以下属性文件:
*`application.properties`
*`application-non-prod.properties`
*`application-dev.properties`
*`application-staging.properties`
*`application-prod.properties`

2-在非生产配置文件中，即`application-dev.properties`和`application-staging.properties`中添加此行:`spring.profiles.include=non-prod`。这告诉 Spring 将`non-prod`“profile”包含到活动概要列表中，这使得`application-non-prod.properties`中的所有属性对应用程序可用。

3-遵循这些惯例:
*如果一个属性将在所有配置文件中保持其值→将其添加到`application.properties`。
*如果一个属性只有不同的生产值→将其添加到`application-non-prod.propertie`和`application-prod.properties`。
*如果属性更改了每个配置文件的值→将其添加到所有配置文件；`applicatio-dev.properties`、`application-staging.properties`和`application-prod.properties`。

![](img/4f229f2988db291187f6ad6fe734011c.png)

Foter.com[上的照片](https://foter.com/re5/11623b)

# 一个示例实现

为了创建一个最小的应用程序来测试这个设置，我们可以从一个至少具有`spring-boot-web-starter`依赖项的空 Spring Boot 应用程序开始，并将以下内容添加到我们的配置文件中:

示例 1:应用程序.属性

```
prop1=This is a shared value across all profiles for prop1.
```

示例 2:应用程序-非产品属性

```
prop2=Non-production-specific value for prop2.
```

示例 3:应用程序开发属性

```
spring.profiles.include=non-prod

prop3=Development-specific value for prop3.
```

示例 4:应用程序暂存.属性

```
spring.profiles.include=non-prod

prop3=Staging-specific value for prop3.
```

示例 5:应用产品属性

```
prop2=Production-specific value for prop2.

prop3=Production-specific value for prop3.
```

一旦这些文件都设置好了，添加一个端点`/properties`，它接受一个`key`，并从环境中返回它的`value`，以便于测试:

样本 6:PropertyController.java

```
@RestController
@RequestMapping("/properties")
public class PropertyController {

    private final Environment environment;

    @Autowired
    public PropertyController(
            Environment environment) {

        this.environment = environment;
    }

    @GetMapping
    public String getProperty(@NotNull String key) {

        return environment.getProperty(key);
    }
}
```

# 测试`dev`轮廓

## 在开发模式下启动应用程序

要在将`dev`概要文件设置为活动的情况下启动应用程序，我们可以使用以下命令:

```
mvn spring-boot:run -Dspring-boot.run.profiles=dev
```

## 验证 Spring 的日志

一旦命令运行，Spring 应该显示`dev`(我们设置的概要文件)和`non-prod`(dev 包含的概要文件)作为活动概要文件:

```
.s.s.SpringProfilesSuggestionApplication : The following profiles are active: dev,non-prod
```

假设应用程序在默认的`8080`端口上本地运行，我们可以验证属性是否按预期加载。

## 验证`prop1`值

发出 curl 命令

```
curl localhost:8080/properties?key=prop1
```

应该返回:

```
This is a shared value across all profiles for prop1.
```

这将验证来自`application.properties`的共享属性是否被正确加载。

## 验证`prop2`值

并发出命令:

```
curl localhost:8080/properties?key=prop2
```

应该返回:

```
Non-production-specific value for prop2
```

它验证了来自`application-non-prod.properties`的非生产属性是否被正确加载，这就是这个设置的全部内容。

## 验证`prop3`值

发出命令时:

```
curl localhost:8080/properties?key=prop3
```

应该返回:

```
Development-specific value for prop3
```

这验证了来自`application-dev.properties`的特定概要文件属性被正确加载。

# 测试产品配置文件

测试`prod`曲线遵循与`dev`曲线完全相同的路径。这一节可能会变得单调，但我想放下它的完整性。

## 在生产模式下启动应用程序

使用`prod`配置文件启动应用程序:

```
mvn spring-boot:run -Dspring-boot.run.profiles=prod
```

## 验证 Spring 的日志

Spring 应该显示日志:

```
.s.s.SpringProfilesSuggestionApplication : The following profiles are active: prod
```

## 验证`prop1`值

发出命令:

```
curl localhost:8080/properties?key=prop1
```

应该返回:

```
This is a shared value across all profiles for prop1
```

## 验证`prop2`值

发出命令时:

```
curl localhost:8080/properties?key=prop2
```

应该返回:

```
Production-specific value for prop2
```

## 验证`prop3`值

并发出命令:

```
curl localhost:8080/properties?key=prop3
```

也应该返回:

```
Production-specific value for prop3
```

![](img/af83776cf6aa6004fcc4e3ae140ed03b.png)

Foter.com[的照片](https://foter.com/re5/11623b)

# 代码

如果你想轻松试驾，我已经在 GitHub 上做了同样的设置:[https://github.com/sayadi/spring-profiles-suggestion](https://github.com/sayadi/spring-profiles-suggestion)。