# 使用@data-ui/histogram 库向 React 应用程序添加直方图

> 原文：<https://levelup.gitconnected.com/add-a-histogram-to-a-react-app-with-the-data-ui-histogram-library-adccd14b3196>

![](img/fc1b67fda88cf0ce0a4e625595b7d7e3.png)

塞尔吉奥·阿尔维斯·桑托斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我们将看看如何使用这个库向 React 应用程序添加直方图。

# 装置

我们可以通过运行以下命令来安装它:

```
npm i @data-ui/histogram
```

# 添加直方图

我们可以通过编写以下内容来添加直方图:

```
import React from "react";
import {
  Histogram,
  DensitySeries,
  BarSeries,
  withParentSize,
  XAxis,
  YAxis
} from "[@data](http://twitter.com/data)-ui/histogram";const ResponsiveHistogram = withParentSize(
  ({ parentWidth, parentHeight, ...rest }) => (
    <Histogram width={parentWidth} height={parentHeight} {...rest} />
  )
);const rawData = Array(100).fill().map(Math.random);export default function App() {
  return (
    <div className="App" style={{ height: 300 }}>
      <ResponsiveHistogram
        ariaLabel="histogram"
        orientation="vertical"
        cumulative={false}
        normalized={true}
        binCount={25}
        valueAccessor={(datum) => datum}
        binType="numeric"
        renderTooltip={({ event, datum, data, color }) => (
          <div>
            <strong style={{ color }}>
              {datum.bin0} to {datum.bin1}
            </strong>
            <div>
              <strong>count </strong>
              {datum.count}
            </div>
            <div>
              <strong>cumulative </strong>
              {datum.cumulative}
            </div>
            <div>
              <strong>density </strong>
              {datum.density}
            </div>
          </div>
        )}
      >
        <BarSeries animated rawData={rawData} />
        <XAxis />
        <YAxis />
      </ResponsiveHistogram>
    </div>
  );
}
```

我们创建了`ResponsiveHistorgram`组件，使直方图符合父维度。

这是通过调用`withParentSize`函数来渲染带有父对象高度的直方图来实现的。

在`App`组件中，我们通过传入一些道具来使用`ResponsiveHistogram`组件。

`orientation`具有直方图的方向性。

`cumulative`表示直方图是否是累积的。

`normalized`设置直方图是否标准化为总数的一部分。

`binCount`是要使用的箱子数量的近似值。

`renderTooltip`是一个道具，其功能是让我们从我们悬停的栏中获得一个条目，并在工具提示中呈现它们。

`bin0`有 bin 名。

`count`已经清点了箱中的物品。

`cumulative`具有从最左边的箱子到当前箱子的累计项目数。

`density`有密度。

条形用`BarSeries`组件呈现。

`animated`加载直方图时动画显示直方图。

`rawData`有原始数据。

`XAxis`添加 x 轴。`YAxis`添加 y 轴。

此外，我们需要指定直方图容器的高度，以便它适合这个高度。

其他属性包括`stroke`来改变条的颜色。

`strokeWidth`改变条形的宽度。

# `DensitySeries`

`DensitySeries`组件让我们画出概率密度函数的估计值。

例如，我们可以写:

```
import React from "react";
import {
  Histogram,
  DensitySeries,
  BarSeries,
  withParentSize,
  XAxis,
  YAxis
} from "@data-ui/histogram";const ResponsiveHistogram = withParentSize(
  ({ parentWidth, parentHeight, ...rest }) => (
    <Histogram width={parentWidth} height={parentHeight} {...rest} />
  )
);const rawData = Array(100).fill().map(Math.random);export default function App() {
  return (
    <div className="App" style={{ height: 300 }}>
      <ResponsiveHistogram
        ariaLabel="histogram"
        orientation="vertical"
        cumulative={false}
        normalized={true}
        binCount={25}
        valueAccessor={(datum) => datum}
        binType="numeric"
      >
        <DensitySeries animated rawData={rawData} />
        <XAxis />
        <YAxis />
      </ResponsiveHistogram>
    </div>
  );
}
```

添加密度系列以添加直方图。

# 结论

我们可以使用@data-ui/histogram 库轻松地将直方图添加到 React 应用程序中。