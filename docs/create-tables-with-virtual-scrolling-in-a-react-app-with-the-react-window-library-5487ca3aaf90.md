# 使用 react 窗口库在 React 应用程序中创建带有虚拟滚动的表格

> 原文：<https://levelup.gitconnected.com/create-tables-with-virtual-scrolling-in-a-react-app-with-the-react-window-library-5487ca3aaf90>

![](img/9bbef9ed55a6d313216c3b9784bb6ad2.png)

[基思·米斯纳](https://unsplash.com/@keithmisner?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我们将了解如何使用 react-window 库创建表格。

# 装置

我们可以通过运行以下命令来安装该软件包:

```
yarn add react-window
```

或者

```
npm install --save react-window
```

# 创建表

我们可以通过编写以下内容来创建一个简单的表:

`App.js`

```
import React from "react";
import { FixedSizeList as List } from "react-window";
import AutoSizer from "react-virtualized-auto-sizer";
import "./styles.css";const Row = ({ index, style }) => (
  <div className={index % 2 ? "ListItemOdd" : "ListItemEven"} style={style}>
    Row {index}
  </div>
);const Table = () => (
  <AutoSizer>
    {({ height, width }) => (
      <List
        className="List"
        height={height}
        itemCount={1000}
        itemSize={35}
        width={width}
      >
        {Row}
      </List>
    )}
  </AutoSizer>
);export default function App() {
  return (
    <div className="App" style={{ height: 300 }}>
      <Table />
    </div>
  );
}
```

`style.css`

```
.List {
  border: 1px solid yellow;
}.ListItemEven,
.ListItemOdd {
  display: flex;
  align-items: center;
  justify-content: center;
}.ListItemEven {
  background-color: yellow;
}
```

我们创建了`Row`组件，并将它用作行。它将`index`和`style`道具分别与条目索引和样式关联。

`Table`组件使用来自`react-virtualized-auto-sizer`的`AutoSizer`组件来适当地调整列表及其项目的大小。

`className`是列表的类名。

`height`是列表的高度，由`AutoSizer`自动计算。

`itemCount`有行数。

`itemSize`有每次要加载的项目数。

`width`具有行的宽度，这也是自动计算的。

将`Row`组件添加为`List`的子组件以显示它。

然后我们在`App`中设置 div 的`height`来显示列表。

# 创建表格

我们可以使用`Grid`组件创建一个表格。

例如，我们可以写:

```
import React, { forwardRef } from "react";
import { FixedSizeGrid as Grid } from "react-window";
import "./styles.css";
const GUTTER_SIZE = 5;
const COLUMN_WIDTH = 100;
const ROW_HEIGHT = 35;const Cell = ({ columnIndex, rowIndex, style }) => (
  <div
    className="GridItem"
    style={{
      ...style,
      left: style.left + GUTTER_SIZE,
      top: style.top + GUTTER_SIZE,
      width: style.width - GUTTER_SIZE,
      height: style.height - GUTTER_SIZE
    }}
  >
    row {rowIndex}, col {columnIndex}
  </div>
);const Table = () => (
  <Grid
    className="Grid"
    columnCount={20}
    columnWidth={COLUMN_WIDTH + GUTTER_SIZE}
    height={300}
    innerElementType={innerElementType}
    rowCount={100}
    rowHeight={ROW_HEIGHT + GUTTER_SIZE}
    width={400}
  >
    {Cell}
  </Grid>
);const innerElementType = forwardRef(({ style, ...rest }, ref) => (
  <div
    ref={ref}
    style={{
      ...style,
      paddingLeft: GUTTER_SIZE,
      paddingTop: GUTTER_SIZE
    }}
    {...rest}
  />
));export default function App() {
  return (
    <div className="App" style={{ height: 500 }}>
      <Table />
    </div>
  );
}
```

我们通过获取`coumnIndex`、`rowIndex`和`style`来为表格创建`Cell`组件。`style`被传递给`style`道具。

此外，我们通过将`GUTTER_SIZE`添加到`left`和`top`并从`width`和`height`中减去它来制作一个檐槽。

我们显示`rowIndex`和`columnIndex`作为内容。

`Cell`用于`Grid`组件。

我们设置`columnCount`来设置要显示的列数。

`columnWidth`设置列的宽度。

`height`有桌子的高度。

`innerElementType`是对单元格容器所用网格的给定样式的组件的引用。

`rowCount`有行数。

`rowHeight`有行的高度。

`width`拥有桌子的宽度。

然后我们在我们的`App`组件中使用`Table`组件。

现在我们应该会看到一个 400 像素乘 300 像素的表格。

# 结论

我们可以用 react-window 组件创建一个表格，方法是将计算的维度设置为单元格和表格容器。