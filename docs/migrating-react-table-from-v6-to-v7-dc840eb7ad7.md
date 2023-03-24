# 将 React 表从版本 6 迁移到版本 7

> 原文：<https://levelup.gitconnected.com/migrating-react-table-from-v6-to-v7-dc840eb7ad7>

![](img/9c5e8f1faf8d230b02cf64a63105703b.png)

React Table 是 React 的一个轻量级、快速且可扩展的 datagrid 包。最新版本即 v7 是一个无头工具，这意味着它不呈现或提供任何实际的 UI 元素。您可以构建和设计强大的数据网格体验，同时保持对标记和样式的 100%控制。以前的版本(即 v6)是一个表格组件，您不能 100%控制表格标记和样式。最新版本使它更容易集成到任何现有的主题或现有的表格标记中。但是对于那些正在使用 v6 并希望迁移到 v7 的人来说，本指南将帮助您保持相同的风格和功能。

由于两个版本之间的差异是巨大的，我建议你安装`[react-table-6](https://www.npmjs.com/package/react-table-6)`和`react-table`包(如果你有一个大的代码库或者复杂的用例)并且一个一个地迁移。要升级到最新版本，您必须安装 React v16.8 或更高版本。

这是你使用版本 ***6 时的样子。**** :

反应表 v6 示例

而迁移到**版本 *7 之后。**** 它会是这样的:

反应表 v7 示例

没有改变用户界面/UX，这是我们想要的，但有代码或我们如何渲染它的变化。

# 我们开始吧

*   **安装 React 表用**[**NPM**](https://npmjs.com/)**或** [**纱**](https://yarnpkg.com/)

NPM

```
npm install react-table --save
```

或者纱线

```
yarn add react-table
```

*   **创建一个新组件来渲染 React 表**

如你所知，与旧版本不同，你负责在新版本的 React Table 中呈现包括标记和样式在内的 UI。在上面的渲染代码中，我使用了`react-table-6`正在使用的精确结构(包括类名和标记)。这将确保没有视觉变化。

`useTable`是构建 React 表时需要使用的主要钩子。传递给`useTable`的选项按照提供的顺序被提供给其后的每个插件挂钩，最终产生一个最终的`instance`对象，您可以用它来构建您的表格 UI 并与表格的状态进行交互。在上面的组件中，我使用的是`[flex](https://react-table.tanstack.com/docs/api/useFlexLayout)` [](https://react-table.tanstack.com/docs/api/useFlexLayout)布局，您可以将其更改为`[block](https://react-table.tanstack.com/docs/api/useBlockLayout)`或`[absolute](https://react-table.tanstack.com/docs/api/useAbsoluteLayout)`布局。

*   **改变自定义** `**Cell**` **组件**

在 v6 中，它看起来是这样的:

```
Cell: row => <span className='number'>{row.original.value}</span>
```

更改为:

```
Cell: ({row}) => <span className='number'>{row.original.value}</span>
//You need to use the object destructuring for the props
```

*   **使用块布局、灵活布局或绝对布局挂钩**

`[useBlockLayout](https://react-table.tanstack.com/docs/api/useBlockLayout)`是一个插件钩子，它增加了对标题和单元格的支持，这些标题和单元格被渲染为带有显式`width`的`inline-block` `div`(或其他非表格元素)。类似于`useAbsoluteLayout`钩子，如果您需要虚拟化行和单元格以提高性能，这将非常有用。

`[useAbsoluteLayout](https://react-table.tanstack.com/docs/api/useAbsoluteLayout)` 是一个插件钩子，它增加了对标题和单元格的支持，这些标题和单元格被呈现为具有显式`width`的绝对定位`div` s(或其他非表格元素)。类似于`useBlockLayout`钩子，如果您需要虚拟化行和单元格以提高性能，这将非常有用。

`[useFlexLayout](https://react-table.tanstack.com/docs/api/useFlexLayout)`是一个插件钩子，增加了对标题和单元格渲染为`inline-block` `div`(或其他非表格元素)的支持，其中`width`用作 flex-basis 和 flex-grow。当实现虚拟化和可调整大小的表时，这个钩子变得很有用，这些表还必须能够扩展以填充所有可用空间。

*   **添加**

这对于添加全局列属性很有用。比如你可以把一列的`minWidth`加到一定的 px 宽。请记住，特定于列的属性将覆盖该对象中的属性。如果你在列中定义了`minWidth`，那将被作为第一选择。

*   **添加可调整大小的挂钩**

`[useResizeColumns](https://react-table.tanstack.com/docs/api/useResizeColumns)`是一个插件钩子，当布局使用非表格元素时，增加了对调整标题和单元格大小的支持，例如`useBlockLayout`、`useAbsoluteLayout`和`useGridLayout`钩子。它甚至支持调整列组的大小。这需要添加样式来显示调整图标大小，这已经在上述组件中完成。

*   **添加加载叠加**

如果使用 API 从服务器获取数据，可以显示加载覆盖图，直到在 React 表中填充完数据。上面的组件包含类似于 v6 的加载属性、标记和样式。

*   **改** `**getTdProps**`

在 v6 中，它看起来是这样的:

```
getTdProps={(state, rowInfo, column, instance) => { return { onClick:(e, handleOriginal) =>{
    ...
  }
  ...
 }      

}
```

将此更改为:

```
getTdProps={({column, row}) => {

   return {
    onClick: (e) => {
     ...         
    } ...     
   };
  }
}
```

参数被改变。

*   **展开行(子组件)**

在 v6 中，如果您添加一个`SubComponent` props，它将很容易地为所有根级别行添加一个扩展级别。它还为行扩展开关添加了一个单独的列。

在 v7 中，您需要自己添加列。要做到这一点，您可以在 toggler 后面追加或前置 tog gler。

```
//This will add the expander column at the beginning
columns.unshift({
            Header: () => null, 
            id: 'expander', 
            resizable: false,
            className: "text-center",
            Cell: ({ row }) => (

                <div {...row.getToggleRowExpandedProps()} title="Click here to see more information" className="rt-td rt-expandable p-0">
                    <div className={classnames("rt-expander", row.isExpanded ? "-open": "")}>•</div>
                </div>

            )
        })//You can use push if you want this column at the end
```

您还需要添加渲染标记或逻辑，即如果行被展开，则显示`SubComponent`，否则不显示。

```
{ row.isExpanded ? ( SubComponent({ row }) ) : null }
```

希望以上步骤能帮助你开始将 React Table 升级到最新版本 7。*.因为这个库有大量的功能，所以还有很多东西需要考虑。如果您在迁移过程中需要帮助，请在下面留言。