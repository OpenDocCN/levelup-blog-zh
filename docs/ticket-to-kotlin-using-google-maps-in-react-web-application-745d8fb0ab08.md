# Kotlin 的门票——在 React web 应用程序中使用谷歌地图

> 原文：<https://levelup.gitconnected.com/ticket-to-kotlin-using-google-maps-in-react-web-application-745d8fb0ab08>

![](img/5185763f05a7378eff2ee8e863801b92.png)

西蒙·米加吉在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我的网页版[游戏乘车券](https://github.com/Kiryushin-Andrey/TicketToRide)需要的第一件东西(见[上一篇文章](https://medium.com/@kiryushin.andrey/ticket-to-kotlin-building-an-online-board-game-8ac8466fe142)的故事开头)是一张画路线的地图。在可以滚动和放大缩小的真实地图上玩游戏似乎是个好主意，所以我转向了谷歌地图。将谷歌地图嵌入网站是一个简单且有据可查的过程。首先，注册谷歌云平台账户并获得一个 API 密匙——完成这个的步骤在[这里](https://developers.google.com/maps/gmp-get-started)有详细描述。然后在脚本查询字符串中嵌入一个带有这个 API 键的脚本标记(静态或动态)，并创建一个地图实例，提供初始地图中心位置、缩放和目标元素来包含地图。[这个来自谷歌的教程](https://developers.google.com/maps/documentation/javascript/tutorial)详细描述了你入门需要的一切。

为了玩这个游戏，我必须在地图上画出城市标记和路线。谷歌地图 JavaScript API 允许不同的选项来实现这一点。您可以在这里找到这些选项[的概述。基本上，您可以在基本的开箱即用的基础上构建，如标记、形状和信息窗口，或者创建自己的自定义叠加。基本原语易于使用，非常适合普通任务。它们也可以在一定程度上进行定制——您可以使用任意的 SVG 图像作为标记，将您自己的 HTML 内容放入“简介”窗口，或者设置线条或形状的颜色和笔触。但是有些事情你不能用基本的原语来做。另一方面，自定义覆盖允许您在地图上呈现任意的 HTML 元素，但是您要对创建和定位这些元素的每个方面负责。特别是，您需要注意将纬度\经度坐标转换成像素。Google Maps API 为这种转换提供了帮助方法，但是必须正确使用它们来设置所有相关元素的位置和大小。](https://developers.google.com/maps/documentation/javascript/overlays)

谷歌地图一次显示几个覆盖图，在引擎盖下，基本的图元也驻留在它们自己的覆盖图中，所以你可以根据需要混合和匹配这些方法。这就是我的游戏的结局。我正在用简单的折线绘制路线，自定义它们的颜色和笔画。但是我选择了一个 [google-map-react](https://github.com/google-map-react/google-map-react) 库，而不是内置的城市标记，在内部，这个库使用一个定制的覆盖图来呈现地图的子组件。

# 谷歌地图的包装组件

![](img/f3ded5e7d4e945766ae296a5fc79007d.png)

[自由股](https://unsplash.com/@freestocks?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

您可以直接使用 Google Maps JavaScript API，或者依赖于您选择的框架的一些包装组件。包装组件的好处在于，它们将地图实例公开为 React、Vue 或 Angular 组件，您可以使用属性对其进行设置，并以熟悉的方式在应用程序中使用。GitHub 上有多个组件为所有主要的网络框架包装谷歌地图。但是在选择包装组件时，有许多因素需要考虑。我建议你先熟悉一下 Google Maps API 上的[第一手文档](https://developers.google.com/maps/documentation/javascript/)，决定你需要哪些特性，然后再对现有的包装器组件做一个概述，特别注意它们的 TypeScript 定义。因此，您将能够做出更好的选择，而不会在不能满足您需求的组件上浪费时间。

如果您喜欢静态类型，那么 TypeScript 声明非常重要(如果您不喜欢，为什么要编写 Kotlin 呢？).检查包装组件的 npm 包或附带的`@types/<component>` npm 包中的`index.d.ts`文件，看看这些定义有多完整，每个文件有多少个`any`。您总是可以自己为 JS 代码构建 Kotlin 包装器。但是探索一个组件并同时为它编写 TypeScript 定义是困难的。您可能会想到依赖组件文档的捷径，但是当文档与实现不完全匹配时，或者当作者确实使用了一些难以用静态类型化方式表达的巧妙技巧时，这种方法可能会导致运行时错误。理清这些差异可能会让您对 TypeScript 定义语法和组件本身有一个非常复杂的了解，所以在为第三方组件编写自己的类型定义之前，请确保这是您想要的。看加里·伯恩哈特的《T4》很有趣；处理这种事情大多不是。

我已经把 React 作为我的乘车票游戏的前端，所以这里是我找到的谷歌地图的三个 React 包装库的简要概述。他们的名字并不太有创意，有时会忘记我现在正在处理哪个组件:)

## [react-google-maps](https://github.com/tomchentw/react-google-maps)

这是谷歌地图 JavaScript API 的一个非常薄的包装。它负责将地图及其部分(标记、形状等)作为 React 组件公开，仅此而已。这个库并没有吹嘘任何一步一步的教程或示例，而是希望您非常熟悉 Google Maps JavaScript API 本身(这是一个好主意)。同时，这个库为谷歌地图功能的一个非常广泛的子集提供了包装器——仅举几个例子，如 [KML 层](https://developers.google.com/maps/documentation/javascript/kmllayer)、[热图层](https://developers.google.com/maps/documentation/javascript/heatmaplayer)、[绘图管理器](https://developers.google.com/maps/documentation/javascript/drawinglayer)和 g [圆形覆盖图](https://developers.google.com/maps/documentation/javascript/groundoverlays)。TypeScript 定义已经存在并且非常好，所以使用 Kotlin 的这个库将是轻而易举的事情(每 663 行`index.d.ts`文件只有 3 个`any`,太棒了！).

使用 Kotlin 的这个库有一个小问题，因为它使用了几个高阶组件来呈现地图实例，而 Kotlin React 包装器文档并不清楚如何在 Kotlin 中使用这些组件。诀窍是修改 [Dukat](https://github.com/Kotlin/dukat) 生成的 Kotlin 定义来使用`HOC`函数，如下所示:

```
@JsName("default")
**external val** withGoogleMap : HOC<GoogleMapProps, WithGoogleMapProps>
```

参见 GitHub 上的这个[线程](https://github.com/JetBrains/kotlin-wrappers/issues/13)以获得关于处理 Kotlin 的高阶 React 组件的更详细讨论，并阅读我的下一个故事以获得关于 [Dukat](https://github.com/Kotlin/dukat) 工具的介绍和我的使用经验。

## [谷歌地图反应](https://github.com/fullstackreact/google-maps-react)

这个库是和一篇详细的描述如何构建这样一个包装器组件的博客文章一起构建的。该组件本身涵盖了谷歌地图的最基本功能——渲染地图本身，并在其上绘制标记、线条和简单的形状。不支持数据可视化或叠加等高级功能。

尽管所支持的特性对于我的用例来说已经足够了，但是这个组件没有提供像样的 TypeScript 定义。定义文件[存在于](https://github.com/fullstackreact/google-maps-react/blob/master/index.d.ts)中，但是它只是为除了地图本身之外的所有组件属性抛出了`any`，所以它对任何静态类型的代码库几乎没有用处。尽管如此，如果您正在考虑编写自己的包装器组件，这个组件及其附带的博客文章是一个很好的起点。

## [谷歌地图反应](https://github.com/google-map-react/google-map-react)

这个库是我用来买我的[票来玩](https://github.com/Kiryushin-Andrey/TicketToRide)游戏的。这个库的杀手锏是它允许你渲染任意的 React 组件，这些组件依赖于由纬度/经度对指定的地图上的某个点。该库不是通过自定义标记来实现的，而是通过在[自定义覆盖图](https://developers.google.com/maps/documentation/javascript/customoverlays)中渲染地图的所有子组件来实现的。这个功能工作得很好，非常方便我的游戏在地图上绘制城市，根据游戏状态用不同的图标表示它们，并用工具提示装饰它们。

该库还提供了大量漂亮的示例，但是要注意，存在所谓的新示例和旧示例，并且相对于库代码库的当前状态，并不是所有的示例都是最新的。在撰写本文时，新的示例页面被标记为进行中，但看起来中途被放弃了一段时间。

除了将成熟的 React 组件呈现为地图上的标记这一独特功能之外，该库没有为其他 Google Maps 功能提供任何包装器。但是它允许轻松访问底层的地图对象，这样您就可以通过直接使用 Google Maps API 和这个对象来实现您需要的一切。这是我在游戏中采用的方法。我在 map 组件中嵌套了两种不同类型的组件。[其中一个](https://github.com/Kiryushin-Andrey/TicketToRide/blob/master/src/jsMain/kotlin/ticketToRide/components/MapCityMarker.kt)负责渲染一个城市标记。它只是一个常规的 React 组件，生成虚拟 DOM 树的某个部分，对它所呈现的地图一无所知。包含地图的组件负责在地图的指定点呈现该元素。[另一个组件](https://github.com/Kiryushin-Andrey/TicketToRide/blob/master/src/jsMain/kotlin/ticketToRide/components/MapSegmentComponent.kt)为地图上的一条路线呈现一条[折线](https://developers.google.com/maps/documentation/javascript/shapes#polylines)——但与城市标记组件不同，它完全知道地图实例，并与之交互来呈现这条线。因此，该组件的`render`方法初始化`Polyline`实例，并将其附加到包含它的 map，但不对父组件的虚拟 DOM 做任何贡献。

至于这个库的 TypeScript 定义，它们在[中有明确的类型化](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/google-map-react)，但很难称之为完整——许多属性和函数参数都留有`any`类型。但是我只使用了组件公共接口的一小部分(实例化地图)，现有的 TypeScript 定义对我来说已经足够了。我用特定的类型替换了几个`any`，调整了生成的声明，使它们可以很好地与 [Kotlin React wrapper](https://github.com/JetBrains/kotlin-wrappers/tree/master/kotlin-react) 库配合使用，一切顺利。

在本系列的下一篇文章[中，我分享了我使用 Kotlin 的 JavaScript 库的经历——如果您对更多细节感兴趣，请继续阅读。](https://medium.com/@kiryushin.andrey/ticket-to-kotlin-using-dukat-to-bridge-kotlin-with-the-js-world-431a2458b95c)