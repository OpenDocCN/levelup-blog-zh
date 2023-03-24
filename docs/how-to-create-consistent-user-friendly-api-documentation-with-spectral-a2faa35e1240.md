# 如何使用 Spectral 创建一致且用户友好的 API

> 原文：<https://levelup.gitconnected.com/how-to-create-consistent-user-friendly-api-documentation-with-spectral-a2faa35e1240>

![](img/809a8c89bbee50be88eb3fd601ca8260.png)

[约书亚·钟](https://unsplash.com/@joshuachun?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在编写代码的上下文中，风格指南详细说明了应该遵守的约定和风格。开源项目和组织拥有自己的风格指南——或者采用一个，例如[谷歌 Java 风格指南](https://google.github.io/styleguide/javaguide.html)，这是很常见的。风格指南通常作为 CI 流程的一部分使用自动化工具来实施，比如如果你使用的是 Ruby，那么就使用 [Rubocop](https://rubocop.org/) 。

在这篇文章中，我将向您介绍 [Spectral](https://github.com/stoplightio/spectral) ，这是一个由 [Stoplight](https://stoplight.io/) 开发的开源工具，它允许您为您的 API 实现一个风格指南(并且，如果您使用的是 [OpenAPI](https://github.com/OAI/OpenAPI-Specification) ，请确保您的 API 符合 OpenAPI 规范！)

# 为什么风格指南很重要

样式指南对于确保一致性和可读性非常重要。在 API 的例子中，如果没有风格指南，你可能会在一个组织中有不同的 API 风格——或者更糟，在同一个 API 中！

如果你的 API 中有不一致的地方，你的客户的用户体验也会受到影响——想象一下，来自同一个组织的混合了 snake case 和 camel case 的 API，这几乎肯定会导致你的 API 的客户。

在 OpenAPI 的情况下，您可以从 OpenAPI 模式自动生成客户端库，但是如果您的模式不符合规范，这将不起作用。我曾在一些公司工作过，在我们不得不尝试使用文档或生成客户机库之前，没有人知道我们的模式是无效的——那就太晚了。

# 介绍光谱

Spectral 是一个 JSON 和 YAML linter，它允许您定义一组针对 API 模式执行的规则。OpenAPI 2 & 3(以前的 Swagger)自带林挺，允许您根据官方的 OpenAPI 规范立即验证您的模式。

如果您没有使用 OpenAPI，或者如果您想在 OpenAPI 规范之上添加额外的规则，那么 Spectral 也允许您这样做。

## 入门指南

Spectral 是一个节点包，因此安装非常简单，只需运行以下命令:

```
npm install -g @stoplight/spectral
```

就这样，现在您可以针对任何 JSON 或 YAML 模式定义运行林挺了！

## 验证 OpenAPI 模式

为了进行试验，我定义了一个伪 OpenAPI 模式供您使用:

```
spectral lint [https://gist.githubusercontent.com/apeacock1991/ab0207884782f4dad2e96f6409eb6d06/raw/76857f1062ecb52db62473aab77f56763d98cbaa/openapi_example.yml](https://gist.githubusercontent.com/apeacock1991/ab0207884782f4dad2e96f6409eb6d06/raw/76857f1062ecb52db62473aab77f56763d98cbaa/openapi_example.yml)
```

如果在本地运行，您将看到以下输出:

```
OpenAPI 3.x detectedhttps://gist.githubusercontent.com/apeacock1991/ab0207884782f4dad2e96f6409eb6d06/raw/76857f1062ecb52db62473aab77f56763d98cbaa/openapi_example.yml1:1  warning  oas3-api-servers
OpenAPI `servers` must be present and non-empty array.1:1  warning  openapi-tags
OpenAPI object should have non-empty `tags` array.2:6  warning  info-contact
Info object should contain `contact` object.8:9  warning  operation-description
Operation `description` must be present and non-empty string.8:9  warning  operation-operationId
Operation should have an `operationId`.8:9  warning  operation-tags
Operation should have non-empty `tags` array. **✖ 6 problems (0 errors, 6 warnings, 0 infos, 0 hints)**
```

结果分为四类:错误、警告、信息和提示。只有错误会导致返回非零的退出状态(在 Bash 中表示失败)。一般来说，你的目标应该是没有错误或警告。

Spectral 足够聪明，可以检测一个文件是否在使用 OpenAPI，如果是，它会自动运行它的 OpenAPI 验证规则。

**定义自定义规则集**

默认的 OpenAPI 规则集对于任何使用 OpenAPI 的组织来说都是一个很好的补充，但是如果你不使用 OpenAPI，或者想用你自己的规则来丰富它们呢？

这就是 Spectral 的亮点，因为您可以定义自定义规则集，在一个规则集中有无限多的规则。规则集使用简单的格式定义:

```
rules:
  open-api-version-3:
    description: APIs should support OpenAPI V3
    given: $.openapi
    severity: error
    then:
      function: pattern
      functionOptions:
        match: '^3'
  snake-case-for-paths:
    description: Paths must use snake_case
    given: $.paths
    severity: error
    then:
      field: '@key'
      function: pattern
      functionOptions:
        match: '^[a-z_\/{}]*$'
```

该规则集包含两条规则。第一个验证只使用了 OpenAPI 版本 3，第二个定义了 snake case 必须用于路径——我们需要确保允许使用花括号，以便可以在路径中定义参数(例如`/pets/{type}`)

如果正则表达式不能覆盖您的用例，您可以使用 JavaScript 扩展核心函数。关于定义自定义规则集的更多信息，包括 JavaScript，请查看官方文档。

## 参考

*   [光谱](https://github.com/stoplightio/spectral/)
*   [OpenAPI](https://www.openapis.org/)