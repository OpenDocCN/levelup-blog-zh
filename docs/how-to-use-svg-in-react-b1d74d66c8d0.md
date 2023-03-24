# 如何在 React 中使用 SVG

> 原文：<https://levelup.gitconnected.com/how-to-use-svg-in-react-b1d74d66c8d0>

从 SVG 中制作可重用 React 组件的简单方法，不需要复杂的工具——只需 React、ReactDOM 和 JSX。

![](img/1246e3c4fa921aa79109dda43bc050ee.png)

图片由[玛格达·瓦克斯伯格](https://www.linkedin.com/in/magda-waksberg-986754180/?originalSubdomain=pl)拍摄

您正在 React 中工作，刚刚获得一个 SVG，并希望在一个项目中使用它。

你如何接近它？让它成为一个组件！

有什么好处？您将能够在不同的 React 组件中重用 SVG，并且还可以控制它的属性。而且，很容易。

所以让我们开始吧。

# 我们在建造什么？

我们将构建一个小的 SVG 组件。我们将能够控制它的大小、颜色和渐变颜色。最终的效果只是两个文件——一个标准 HTML 文件和一个 SVG React 组件。

我们将使用尽可能简单的工具——React、ReactDOM 和 JSX。不需要使用任何样板文件或花哨的打包程序(除了 JSX 解析)。

# 设置项目

要设置这个项目，我们只需要一个 HTML 和一个 SVG 来转换成一个组件。

HTML:

还有我们正在使用的 [SVG](https://gist.github.com/sadamiak/685420b86375768ed3a4eedca1d9daa1#file-icon-svg) 。请记住，您甚至不必将 SVG 图标添加到项目中；您可以使用在线工具将其转换为 React 组件。

我们的目标是用灵活的 SVG 组件创建`component.js`文件。

# 将 SVG 转换为组件

您可以使用像 [svg2jsx](https://svg2jsx.com/) 这样的在线工具将 SVG 转换为组件，或者使用 [SVGR](https://react-svgr.com/) ，它可以用作节点库、CLI 工具、webpack 插件，甚至是 Visual Studio 代码扩展。

因为是一次性的，所以我使用了 svg2jsx。

您选择的工具应该返回一个简洁的 React 组件。到目前为止，一切顺利；我们已经有了一个工作的 SVG 组件。现在是时候让它更灵活了。

我们将让组件接受三个道具:`size`、`color`和`gradientColor`。我们还会添加一些漂亮的默认值，以防有人忘记设置道具。

最后两行将呈现三个图标；你不需要担心他们。

所以我们从 SVG 中得到了一个图标组件。它接受三个道具，工作正常。或者几乎没事。问题是每个图标都有相同的渐变颜色，即使它们有不同的`gradientColor`值。

# 固定渐变

渐变有什么问题？如果你仔细观察，你会发现渐变定义在第 12 行和第 22 行之间。并且梯度具有 ID:

```
<linearGradient id=”linearGradient-1">
```

该 ID 稍后用于第 30 行:

```
<path fill=”url(#linearGradient-1)”>
```

所以在我们的代码中，React 创建了三个图标组件。并且每个都有自己的 ID 相同的`linearGradient`定义。但是由于 id 必须是唯一的，所以只使用第一个`linearGradient`。所有其他图标都被忽略，所以每个图标都会获得最先定义的渐变。

为了解决这个问题，我们将在渐变中添加唯一的 id。每种颜色至少有一个唯一的 ID。为此，我们可以使用十六进制颜色作为 ID。最终代码:

现在我们已经有了一个由 SVG 构成的完全工作的、灵活的组件。