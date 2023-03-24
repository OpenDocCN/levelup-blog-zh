# 打字稿—用 AJV 验证

> 原文：<https://levelup.gitconnected.com/typescript-validation-with-ajv-1b70a76dd678>

![](img/0476361de81304214a3a9dbbd425ee14.png)

我们都遇到过需要验证用户提交的数据的情况，但是最简单、最可靠、最省事的验证方法是什么呢？

这就是 AJV(另一个 JSON 验证器)出现的地方！AJV 可以使用 JSON 模式来验证对象，这也使它变得非常简单。

# 有人说 AJV 吗？

让我们创建一个简单的服务，该服务具有一个注入的 AJV 实例，该实例具有验证一些用户数据的功能。

实例化 AJV 相对简单。构造函数接受一个可选的配置，出于这个简短指南的目的，我们将省略它。

```
import * as Ajv from 'ajv'const ajv = new Ajv()
```

如果您想将它注入到您正在创建的自定义验证器类中，您可以使用`Ajv`接口在构造函数中键入属性。

```
import { Ajv } from 'ajv'class Foo {
  constructor(protected ajv: Ajv) {
  }
}
```

如果我们要导入我们想要使用的模式，我们可以非常简单地导入 JSON 对象。在本文的后面，我们将探索一个简单的 JSON 模式。确保在您的`tsconfig.json`中您首先启用了以下属性`"resolveJsonModule": true`。

```
import * as requestSchema from '../foo/bar/requestSchema.json'
```

现在我们准备创建一个基本函数，它将接受用户提交的数据和模式作为参数，并根据给定的模式验证数据。

```
public validate(schema, data) {
  return this.ajv.validate(schema, data)
}
```

AJV 上公开的`validate()`函数将返回一个布尔值，表明根据给定的模式，数据是否有效。如果数据验证失败，您可以调用公开的函数`errorsText()`来获取验证数据时发生的错误的相关消息。

让我们看一个基本的实现，当失败的验证发生时，我们如何处理它。如你所见，我们注入的`ajv`对象上有错误。

```
public validate(schema, data) {
  const isValid = this.ajv.validate(schema, data) if (!isValid) {
    const errorMessages = this.ajv.errorsText()
    throw new ***Error***(`Validation Error. ${errorMessages}`)
  } return data
}
```

值得注意的是，公开的函数`errorsText()`返回一个字符串，错误以逗号分隔。就我个人而言，我会创建一个自定义的命名错误，它接受错误消息作为参数，并在这里抛出命名错误，而不是一般错误，但这只是我的想法。

**注意**:你也可以访问`ajv`对象的`errors`属性，它是一个错误对象的数组，包含了更多关于失败验证的信息。如果你愿意，你可以尝试一下。

# **稍微高级一点的**

让我们假设我们可能需要多次重用这个函数，并且我们希望返回一个特定的类型，那么我们如何轻松地完成这个任务呢？

一个字: ***仿制药*** ！

```
public validate<T>(schema: object, data: T): T {
  const isValid = this.ajv.validate(schema, data)

  if (!isValid) {
    const errorMessages = this.ajv.errorsText()
    throw new ***Error***(`Validation Error. ${errorMessages}`)
  }

  return data
}
```

我们可以简单地允许`T`的类型在这里被用作类型定义。该函数将返回输入的相同数据或抛出一个错误(在这种情况下，返回类型被认为是`never`)。

最终调用和利用这个函数变得非常容易。调用函数，其中`SOME_TYPE`是我们期望用户数据符合的对象的类型定义。

```
import * as schema from '../some/schema.json'
import * as userData from '../some/user/data.json'

const ajv = new Ajv()
const foo = new Foo(ajv)

const validData: SOME_TYPE = foo.validate<SOME_TYPE>(schema, userData)
```

![](img/697d62a7542954c6333e2480c74e1536.png)

# 你觉得这些模式怎么样？

现在让我们进入事物的模式方面！考虑下面这个非常简单的对象，它包含一些我们可能需要的用户数据。

```
{
  "name": "John",
  "age": 43,
  "gender": "male",
  "phoneNumber": "0728903254",
  "internationalCode": "+27"
}
```

如你所见，有一些关于一个叫约翰的绅士的简单数据。

我们假设传入的`name`肯定应该是字符串。JSON 模式如何定义这一点？

```
{
  "name": {
    "$id": "#/properties/name",
    "type": "string"
  }
}
```

毫不费力且相对简洁。同样，如果我们假设提供的年龄也是一个数字，我们可以将类型定义为`integer`。

```
{
  "age": {
    "$id": "#/properties/age",
    "type": "integer"
  }
}
```

这很好，但是如果我们需要验证数据是否符合比基本类型更具体的模式呢？

让我们看看上面的对象中的国际拨号代码或`internationalCode`。

[你可以在这里阅读更多关于国际拨号码的信息←](https://www.internationalcitizens.com/international-calling-codes/)

对于这个属性，我们可能想要包含一些更加复杂的验证需求。也就是说，我们应该检查代码前面是否有一个`+`,并且代码是一个 1 到 4 位数的数字。不要烦恼！模式已经覆盖了你！

```
{
  "internationalCode": {
    "$id": "#/properties/internationalCode",
    "type": "string",
    "pattern": "^\\+[0-9]{1,4}$"
  }
}
```

您可以看到，我们现在正在确保传入的请求符合指定的模式。有用！

为了便于练习，我们假设传入的请求可能只符合两种有效的性别，即男性和女性。我们可以引入一个枚举列表作为验证需求的一部分。

```
{
  "gender": {
    "$id": "#/properties/gender",
    "type": "string",
    "enum": [
      "male",
      "female"
    ]
  }
}
```

现在，我们的数据突然被更加彻底地验证，不仅验证了遗产的存在，还验证了遗产的内容！

如果我们想让属性成为可选的或必需的，我们该怎么做呢？

```
{
  "required": [
    "name"
  ]
}
```

现在`name`属性将是必需的，其余的属性是可选的。如果`name`属性不存在，验证器将返回一个错误消息，指出这个属性是必需的。

如果我们现在将所有这些片段组合成一个有效的模式，它实际上会是什么样子呢？

```
{
  "required": [
    "name"
  ],
  "properties": {
    "name": {
      "$id": "#/properties/name",
      "type": "string"
    },
    "age": {
      "$id": "#/properties/age",
      "type": "integer"
    },
    "gender": {
      "$id": "#/properties/gender",
      "type": "string",
      "enum": [
        "male",
        "female"
      ]
    },
    "phoneNumber": {
      "$id": "#/properties/phoneNumber",
      "type": "string"
    },
    "internationalCode": {
      "$id": "#/properties/internationalCode",
      "type": "string",
      "pattern": "^\\+[0-9]{1,3}$"
    }
  },
  "$id": "http://example.org/root.json#",
  "type": "object",
  "definitions": {},
  "$schema": "http://json-schema.org/draft-07/schema#"
}
```

验证用户输入非常重要。AJV 和 JSON 模式使得实现非常有效和简单的验证机制变得相当容易。我强烈推荐使用 AJV。

![](img/120f91c031cdfb228bb302fb41a5a45e.png)

# 有用的工具？

您可能认为创建一个模式可能会很棘手并且很乏味…这是错误的。这非常简单，几乎没有任何不便。有一个在线工具允许你输入一个普通的 JSON 对象，它会自动为你生成一个模式来验证这个对象。然后你可以随意调整它。

例如，您可能不希望所有属性都是必需的。有些属性可能需要符合某种模式，甚至是枚举列表的一部分。

[BrijPad — JSON 模式生成器](https://techbrij.com/brijpad/#json)
[免费在线 JSON 到 JSON 模式转换器](https://www.liquid-technologies.com/online-json-to-schema-converter)

[JSON 模式](https://json-schema.org/specification.html)
[模式示例](https://code.tutsplus.com/tutorials/validating-data-with-json-schema-part-1--cms-25343)

感谢你阅读这篇文章，我希望你学到了一些新的东西。如果你有任何意见，请随意写在下面的评论区！

再见了。