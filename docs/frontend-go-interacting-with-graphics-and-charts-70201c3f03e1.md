# Frontend Go —与图形和图表交互

> 原文：<https://levelup.gitconnected.com/frontend-go-interacting-with-graphics-and-charts-70201c3f03e1>

![](img/30a5004ad49174ef4f5c1f1441e1f2a3.png)

由[卢卡斯·布拉塞克](https://unsplash.com/@goumbik?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在以前的文章中([入口点](https://medium.com/@sean_24982/experimenting-with-full-stack-development-in-go-part-1-setting-the-scene-963adb40b189))，我描述了如何在一个玩具应用程序的前端和后端都使用 Go。这最初的工作是基本的，我想深入一点。在这里，我报告了我使用 Go 获得(稍微)更复杂的前端操作的经验。

我想有一个基本的前端，可以显示图表内容；最初，我考虑使用简单的图形作为图表的基础，基于一些[观察，这可能比使用 D3](https://medium.com/@PepsRyuu/why-i-no-longer-use-d3-js-b8288f306c9a) 更容易。随后，我考虑了如何与功能丰富的 Javascript 图表库 Apache ECharts 进行交互。和之前的帖子一样，前端工作全部基于`[vecty](https://github.com/gopherjs/vecty)`。

**添加基于 SVG 的图表**

我首先检查了如何在`vecty`中显示 SVG 内容。`vecty`本身(目前)没有特定的 SVG 支持，但是我发现了一个相对较新的 Go 包——`[nathanhacks/svg](https://github.com/nathanhack/svg)`——它专注于在这个上下文中提供 SVG 支持。使用这个库，可以生成简单的折线图、饼图和条形图，如下面的视频所示。

由于该库仍然是新的，正在努力完善其功能和接口。该库基于 [SVG Web API](https://developer.mozilla.org/en-US/docs/Web/API/SVGElement) 提供了与 SVG 图形交互的基本原语；那里定义的概念和 API 调用非常直接地映射到库功能。SVG 模型的一个有趣的方面是可以灵活地定义显示坐标系的哪一部分，并且确实可以动态修改以提供不同的视觉效果，如运动和平移。这意味着原则上可以选择平面上的任意区域来放置和显示图形。这在显示饼图时很有用:最方便的工作是将平面的一部分集中在`(0, 0)`上，而不是将这个点放在`viewport`的左上方。

另一个在开始时并不明显的问题——主要是由于我缺乏 SVG 知识——是它有一个非常复杂的路径模型[,它被用作创建除了最简单的形状以外的所有形状的基础:例如，为了生成饼图的片段，有必要构造一个正确形状的填充路径——基本思想见下文。](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/d#Path_commands)

```
func renderSector() *vecty.HTML { openingAngle=initialPercentage*2*math.Pi
  segmentAngle=additionalPercentage*2*math.Pi p := path.MoveTo(0, 0) x = float64(rx) * math.Cos(openingAngle)
  y = -float64(ry) * math.Sin(openingAngle)
  p = p.LineTo(x, y) closingAngle = openingAngle + segmentAngle
  x = float64(rx) * math.Cos(closingAngle )
  y = -float64(ry) * math.Sin(closingAngle)
  p = p.Arc(rx, ry, 0.0, 0, 0, x, y) p = p.ClosePath() image := []svg.Component{
    attr.Class("svg-image"),
    attr.Width(300),
    attr.Height(300),
    attr.ViewBox(0, 0, 100, 100),
    attr.Stroke("red"),
    svgelem.Path(attr.Stroke("red"), attr.Fill("red"), attr.D(p)),
   } return elem.Div(
    vecty.Markup(
      vecty.Class("chart-panel"),
    ),
    svg.Render(image),
  )
}
```

**添加基于 Apache ECharts 的图表**

理解了 SVG 库的基本机制和设计之后，我意识到使用这种方法支持不同的图表类型需要相当大的努力。

接下来我研究了如何使用 ECharts，并考虑如何从`vecty`中使用它。考虑特定的图表集成很有意思，但更一般地理解 Go/Javascript 集成机制也很有意思，这样就可以从 Go 调用任意的 Javascript 功能。

全面的`go-echarts`库提供了一个很好的起点:`go-echarts`提供了在 Go 中创建许多不同图表类型的功能，并输出 HTML/Javascript 来可视化图表。关键的一点是它只支持静态图表:图表和相关数据在生成器代码中定义，静态 HTML/Javascript 被输出——它(还)没有提供一种方法使图表显示在通过 Go 动态控制的 HTML 页面中。

ECharts 提供了一个扩展的 JS API，它通过两个特定的调用控制图表交互的许多方面，这两个调用说明了许多关键功能:`echarts.init`在页面内的特定容器中创建图表的实例，`<chart>.setOptions`将图表参数化—包括图表类型、图表数据、图例等—并相应地显示它。这两个功能是`go-echarts`使用的。

要使用这些功能，首先需要创建一个可以插入图表的`div`；当`div`被插入页面时，上面提到的`echarts`函数被调用。由于`vecty` 的*存在的理由*是为了支持页面内容的动态控制，所以不能假定`div` 是事先生成的:`vecty` 应该在必要时向页面添加内容。这就产生了一个小问题:对应于`div`的 Javascript 对象需要提供给`echarts.init`函数，但是不能确定它是否存在于页面中。`vecty Components`可以处理这一点，提供一个`Mount()`调用作为接口的一部分，当`Component`在页面中呈现时调用该接口:对于本例中的每个图表类型——折线图、饼图和条形图，都创建了一个`Component`,当`div`被插入到页面中时，它具有调用`echarts`函数的功能。

在初始化`div`上的`echarts`后，有必要使用`setOptions`调用来配置图表。为`setOptions`调用提供正确的输入需要一些努力——在 Javascript 世界中，它需要一个有效的 Javascript 对象，但是`go-echarts`库没有提供将图表状态导出到这样一个对象的机制。因此，需要对`go-echarts`库进行修改。由于这些对象必须使用`syscall/js ValueOf`函数转换成 Javascript 实体，图表选项必须是特定的格式(`ValueOf`要求 Javascript 对象是`map[string]interface{}`而列表是`[]interface{}`):因此我为不同的图表类型添加了一个新函数`GenerateOptions()`，它将存储在`go-echarts`中的状态转换成可以动态传递给`echarts`的状态。值得注意的是，Go `[structs](https://github.com/fatih/structs)`包完成了大量的转换工作。

**演示**

演示非常简单，提供了显示(非常)基本的基于 SVG 的图表集或更复杂的电子图表集的选项；可以在两者之间动态切换。下面的短片展示了在一个干净的虚拟机中的安装和构建过程以及最终的输出。

显示安装过程和结果输出的视频

**结束语**

它没有回答所有关于 Go/JS 集成的问题:目前还不清楚 ECharts JS 库所做的页面修改是否会对 Go/WASM 应用程序产生负面影响，因为后者看不到这些变化。对于这个简单的演示，没有观察到负面影响，但是对于更复杂的应用程序，可能会出现这样的问题。

包含内容的 github repo 这里是[这里是](https://github.com/seanrmurphy/go-vecty-experiments)。

鸣谢:感谢 nathanhack 在这项工作的 SVG 方面提供的帮助。