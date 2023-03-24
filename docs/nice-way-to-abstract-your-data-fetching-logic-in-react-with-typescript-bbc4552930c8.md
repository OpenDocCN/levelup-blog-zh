# 在 React with TypeScript 中抽象数据提取逻辑的好方法

> 原文：<https://levelup.gitconnected.com/nice-way-to-abstract-your-data-fetching-logic-in-react-with-typescript-bbc4552930c8>

## 如何规范、改造和分离 DTO 和 App 界面，以创造更好的开发者体验？

![](img/edde499ec5d3d5626cb23b1334064cb9.png)

React 中的数据获取

大多数 web 应用程序依赖于某种服务器端交互，REST 是目前应用最广泛的架构。然而，经常会收到来自 REST API 调用的数据过多或过少的臃肿响应。

膨胀的 API 响应的一些例子可以是例如这些:

*   [https://pokeapi.co/api/v2/pokemon/1](https://pokeapi.co/api/v2/pokemon/1)
*   http://3.212.60.101/wp-json/wp/v2/posts

我说臃肿是因为我们可能不需要响应中所有可用的字段。此外，我们可能希望改变字段的大小写，以遵循我们的应用程序中已有的模式，或者在流中计算新字段，或者添加新的默认值，甚至从其他端点获取新数据，以补充当前的数据。

在 React 中有多种方法可以做到这一点，但我将在这里提出一种具有特定逻辑的文件夹/文件结构，它似乎可以很好地处理这些情况。这些步骤是:

*   将提取逻辑隔离到服务文件夹中
*   在 API 响应和应用程序之间添加一个抽象层
*   将函数响应作为对象返回
*   正确断言具有区分联合的 TS 类型

假设我们想创建一个函数，通过 ID 获取口袋妖怪的详细信息。让我们首先用以下结构创建一个文件夹 services/pokemon:

```
|
|-services
|--pokemon
|---api.ts
|---utils.ts
|---typings.ts
|---getPokemonDetails.ts
|---index.ts
|
```

**/services:** 文件夹，包含应用程序的所有数据提取逻辑
**/pokemon:** 文件夹，包含与 pokemon
**相关的所有提取逻辑/api.ts:** 文件，包含用于 pokemon 相关提取逻辑的 api 实例
**/utils.ts:** 文件，包含用于 pokemon 相关提取逻辑的实用函数
**/get pokemon details . ts:**文件，包含用于提取 pokemon 的逻辑

我这样做是因为如果你有更多的服务，那么就更容易管理，但为了简单起见，我会把所有东西都添加到同一个文件中。

对于 api.ts 文件，您可以使用 Axios 创建一个实例并设置拦截器。

有趣的部分来了。对于 getPokemonDetails.ts 文件，我建议这样做:

具有正确类型的 getPokemonDetails 服务的数据提取逻辑

注意我是如何转换数据的。这与我所说的浮肿反应有关。口袋妖怪 api 返回了太多的数据，但对于这个应用程序，我只需要在界面口袋妖怪细节中声明的内容。如果您想输入您的 API 响应，我强烈建议，我建议使用术语 PokemonDetailsDTO。DTO 代表数据传输对象，是指从服务器传输/接收的数据。

另外，请注意实用函数 getIdFromUrl。这个 API 响应返回 Id，但是让我们假设它只返回 URL。这个实用函数将有助于创建这个计算字段，我们将在这一层而不是在 React 组件中这样做。我们希望将数据交付给已经烘焙好的组件，这样它就不需要知道任何关于逻辑或可能不匹配的缺省值的信息。

最后，注意函数响应是如何作为对象返回的。我发现这个图案很干净。您不需要在 React 组件中编写 try/catch 块，在这些方便的类型脚本模式的帮助下，您可以轻松地获得正确的数据类型。

您还可以构建一个自定义钩子来将逻辑与 React 组件隔离开来，就像这样，并且您可以在其上添加 SWR 或 ReactQuery 来进行缓存管理。

如果您对如何使用 ReactQuery 或 SWR 实现类似的模式感兴趣，其中您也可以有一个层来执行数据转换，我建议您看看这个模式:

关于如何使用 SWR 执行数据转换的建议

仅此而已。希望这种模式对你有意义，如果你有任何反馈，请告诉我。谢谢！