# 在无模式的世界中使用模式

> 原文：<https://levelup.gitconnected.com/working-with-schemas-in-a-schemaless-ish-world-d8e878d2ed94>

![](img/661114438335a96f51fccf2eaaa51271.png)

***TL；博士*** *:我写了一个 JS 库，当在不同的 JSON 结构之间转换和处理未记录的或不可靠的模式时，它让生活变得更容易。查看我的* *公司**[***github***](https://github.com/intelligogroup/object-to-schema-mapper)***。****

*Web 开发人员经常需要使用外部 API。有时这些 API 没有很好的文档记录，而其他 API 则根据不同的请求输入以不断变化的结构来响应。*

*当我们——后端开发人员——创建满足我们内部需求的客户端应用程序时，我们可以完全控制我们使用的模式，但是当我们走出去时，我们会遇到各种各样的野兽。*

*我有幸从不同的 API 提供者那里收集数据，只是为了处理它们并将其转换成一个内部模式。有时，甚至将额外的 API 作为源代码添加到生产代码中正在使用的已经活动的模式中。*

*   *当**不**担心第三方 API 甚至懒得记录它们的模式时，怎么可能在你的终端使用最终模式呢？*
*   *我如何从一个模式和另一个模式之间的硬编码和复杂转换中解脱出来？*
*   *我可以快速测试我对第三方 API 模式的假设吗？从 API 响应到我们内部模式的当前转换是否遗漏了什么？*

*这些不是理论问题。这是 Intelligo-group(我工作的公司)的一个严重问题，我已经编写了一个 JS 库，试图解决上面提到的问题。*

***本库分为三个主要部分:***

1.  *根据第三方响应自动创建模式。*
2.  *从一个 JSON 结构到另一个 JSON 结构的映射基于易于编写和人类可读的配置。*
3.  *测试—从模式创建(1)中创建样本数据，并应用映射(2)来检查结果是否令人满意。*

***以下部分是如何使用该库的示例:***

*为了这个例子，让我们假设我是一个电影爱好者，我想收集关于一部电影的所有需要的信息。为此，我使用了一个假想网站的假想 API，这是我搜索“复仇者联盟 3：无限战争”得到的回应:*

*来自假想 API 的响应*

*我想使用我的库并从响应中创建一个模式:*

1.  *让我们安装库:`npm i @intelligo.ai/object-to-schema`*

*2.并调用模式创建者:*

```
*import response from './response.json';
import { mapObjectToSchema } from '@intelligo.ai/object-to-schema';const schema = mapObjectToSchema(response);
console.log(JSON.stringify(schema, null, 2));*
```

*我们将得到的输出是:*

*mapObjectToSchema 函数的输出*

*这里我们可以看到，对于每个键，库都分配了一个`type`并增加了一个`example`，这在后面会很有用。此外，我们可以看到函数是如何处理数组的——它遍历数组的所有元素，并将它们简化为一个模式元素。*

***注意:虽然这个例子涉及一个非常扁平的对象，但是这个库可以处理任何数量的嵌套。查看** [**github 资源库**](https://github.com/intelligogroup/object-to-schema-mapper) **了解更多信息。***

*问题是，这个 API 不是我的主要数据来源，因为有几个来源，所以我有我正在使用的内部模式，看起来像这样:*

*内部模式*

*3.让我们为 mapper 函数编写一个配置文件，将响应转换为所需的模式。该配置被键入:*

*类型*

*让我们来分解一下配置:*

*`**source:**`你要变换的关键点的路径。*

*`**target**`:*

*   *`**path**`:类似于`source`参数，但是现在它表示映射器要创建的键的路径。*
*   *`**defaultValue**`:如果源对象中没有值，将使用默认值。*
*   *`**conditionalValue**` : `targetPathCondition` 是目标对象中的路径，将检查它是否有值。如果该值存在，`value`将被插入到指定的`**path**`中。*
*   *`**priority**`:您可以将多个源映射到同一个目标路径。通过使用`**priority**` 来控制当多个映射从源对象中检索数据时使用什么数据。*
*   *`**predefinedTransformations**`:允许您使用定义的函数以简单的方式转换数据。目前已有的函数:“toUpperCase”、“toLowerCase”、“titleCase”、“toDate”、“stringToArray”、“arrayToString”。arrayToString 仅用于`string[]`我们将在后面看到如何使用这些函数。*

*有了这些信息，我们可以在假想的 API 和内部模式之间构建自己的转换配置，并将函数`mapObject`应用于 API 响应和转换。*

***注意:我添加了 TypeScript 类型，因为这样更容易验证配置。在我们公司，我们已经开发了一个 web 应用程序来轻松构建这样的配置。***

*转换*

*   *您可以看到，有些转换很简单，并不真的需要这样的库，例如`title`转换只需要改变密钥。*
*   *最强大的用例是面对嵌套在数组中的数据。最初写这个库是因为我在 NPM 的生态系统中找不到任何合适的工具来实现这样的壮举。我们可以在`ratings`和`date.release`转换中看到这个例子，其中库访问数组中的每个元素并将其映射到其专用的目标路径。请注意，当源路径嵌套在数组中时，像`key[].key2[].key3[].key4`这意味着对象看起来像这样:*

```
*{
  "key": [
    "key2": [
      "key3": [ { "key4": <<primitive or object>> },
        { "key4": <<primitive or object>> }, // and so on ]
    ]
  ]
}*
```

*如果你想将`key3`的每个元素映射到另一个`target`路径，你必须寻址中间所有的嵌套数组。因此目标**将有**看起来像:`otherKey[].otherKey2[].otherKey3[].otherKey4`。如果你考虑一下，你就会明白这些规则是必须遵守的，因为中间的每个数组都可以有多个元素，库需要迭代它们，所以目标结构必须保持不变。*

*   *您可以在`isReleased`转换中看到条件值的例子。这里，我们没有`source`路径，因为该值是通过查看其他映射值生成的。只有当映射的`released`值存在时，我才将`isReleased`值设置为`true`。*
*   *需要知道的重要一点是，应用于对象的所有变换都是可选的。也就是说，如果一个源对象不满足一个或任何一个转换，就不会产生错误。这让我们能够处理可能返回部分信息的 API。*

*现在，让我们看看映射器函数的输出:*

*映射结果*

*如您所见，响应被成功地映射到我们的内部模式。*

*4.让我们回到模式创建后看到的那个`example`键(2)。假设您已经从一个 API 的结果中提取了模式。现在，在创建/更新映射配置(转换)时，您所要做的就是检索使用这个库创建的 API 模式，并使用一个从模式中提取样本对象的函数。这样，您不必再次调用 API 来获取原始响应对象:*

```
*import response from './response.json';
import { mapObjectToSchema, extractExampleFromSchema, } from '@intelligo.ai/object-to-schema';const schema = mapObjectToSchema(response); // or retrieve from DB
const almostOriginalObject = extractExampleFromSchema(schema);console.log(JSON.stringify(almostOriginalObject, null, 2));*
```

*您可以看到输出:*

*反向提取伪响应*

*如果您仔细观察，您可以看到原始响应和反向提取的响应之间的差异。数组被简化为单元素数组。这些单元素数组包含多元素原始数组中所有可能的键。*

*现在，您可以测试您在这个伪响应对象上的映射，并查看您的配置是否正确。*

*5.使用这个库，您可以做更多的事情。例如，我们在 [Intelligo-Group](https://www.linkedin.com/company/intelligo-group/) 创建了一个端点来更新特定 API 的模式，我们实际上获取了数百到数千个保存在我们系统中的原始响应，并通过模式创建器函数将它们传送出去，该函数实际上可以接受两个参数。这是函数签名:*

```
*function mapObjectToSchema(obj: SomeObj, schema: SomeObj = {}): SomeObj*
```

*第二个参数是一个已经存在的模式，它将被以第一个参数的形式接收的新的响应对象丰富。*

*此外，我们还创建了一个 UI 接口 web-app(目前还不是一个开源应用程序),以便轻松地在响应模式和我们的内部模式之间进行映射。它所做的只是以上面提到的`Transform[]`的格式输出配置。*

**感谢阅读，我希望你会像我们在*[*Intelligo-Group*](https://www.linkedin.com/company/intelligo-group/)*一样喜欢这个图书馆。**

**NPM:*[https://www . npmjs . com/package/@ intelligo . ai/object-to-schema](https://www.npmjs.com/package/@intelligo.ai/object-to-schema)*

**Github:*[https://github.com/intelligogroup/object-to-schema-mapper](https://github.com/intelligogroup/object-to-schema-mapper)*