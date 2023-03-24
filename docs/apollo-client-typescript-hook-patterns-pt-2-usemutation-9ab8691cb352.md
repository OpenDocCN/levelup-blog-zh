# Apollo 客户端+ Typescript 钩子模式(pt。2):使用突变

> 原文：<https://levelup.gitconnected.com/apollo-client-typescript-hook-patterns-pt-2-usemutation-9ab8691cb352>

![](img/45fa023ad15a7f20766719f107708c16.png)

照片由[德鲁·法威尔](https://unsplash.com/@drewfarwell?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

本系列其他部分:
**use query:**[https://level up . git connected . com/Apollo-client-typescript-hook-patterns-33353 b 6315 a 1](/apollo-client-typescript-hook-patterns-33353b6315a1)

这个钩子很有力。在`useMutation`和 [Apollo](https://medium.com/u/9360eb7d79eb?source=post_page-----9ab8691cb352--------------------------------) 客户端稍微刷新一下。当你发送一个突变时，你正在修改你的服务器/客户机上的数据。变异将返回一个新的数据集。如果您返回正确的主键(默认为`id`)，Apollo 客户端将自动用这些新数据更新您的本地状态/缓存。这种模式目标是将修改逻辑存储在一个地方。

如果你还没有看过第一部分的文件夹结构，请先检查一下。在这一部分中，我们将钩子存储在一个`/graphql`文件夹中组件的旁边。

# UseTableSizeMutation:

在这个例子中，我将使用一个名为`setScoresheetTableFormat`的变异，它有两个输入 id 和格式

```
import gql from 'graphql-tag';
import { useMutation } from '@apollo/client';export default () => {
const [TableSize] = useMutation(gql`
  mutation TableSizeMutation($id: ID!, $format: TableFactor) {
   setScoresheetTableFormat(id: $id, format: $format) {
     scoresheet {
       id
       tableFactor
     }
    }
   }`
);return (id: string, format: TableFactor) => TableSize({ variables: { id, format } });
```

*   我们创建一个新文件来保存我们的钩子。
*   导出返回变异函数的默认函数。这是从`useMutation.`返回的数组中的第一个参数，第二个参数是突变的`data/loading/options`对象，但是在这种情况下我们不需要它。
*   有两种方法可以插入你对突变的观点。如上图所示的方式；从钩子返回的函数接受参数。
    另一种方法是提供钩子实例化的参数。这将看起来像:`const mutate = useTableSizeMutation(id,format)`

现在要使用这个变异，我们要做的就是实例化它并调用它。

```
import useTableSizeMutation from './graphql/useTableSizeMutation'export const MyComponent = ({id})=>{
 const setTableSize = useTableSizeMutation();return <Button onPress={()=>setTableSize(id, '7Foot')} />}
```

# 你可以停在这里

如果您在这里停下来，您会很好，并且已经成功地解耦了显示/数据逻辑。但是让我们更进一步。

**打字稿是我们的朋友！如果你还没有，我强烈建议你去看看阿波罗代码工具([https://github.com/apollographql/apollo-tooling)](https://github.com/apollographql/apollo-tooling)。这些将自动生成您的 TS 类型，以匹配您的查询和变化。运行 codegen 之后，我们可以导入我们生成的类型，并将它们应用到变异/**

```
import gql from 'graphql-tag';
import { useMutation } from '@apollo/client';
import { TableSizeMutation, TableSizeMutationVariables } from './__generated__/TableSizeMutation';
import { TableFactor } from '@root/__generated__/apolloGlobals';export default () => {
const [TableSize] = useMutation<TableSizeMutation, TableSizeMutationVariables>(gql`
  mutation TableSizeMutation($id: ID!, $format: TableFactor) {
   setScoresheetTableFormat(id: $id, format: $format) {
    scoresheet {
     id
     tableFactor
    }
   }
  }`
);return (id: string, format: TableFactor) => TableSize({ variables: { id, format } });
};
```

*   默认情况下，我们的类型被转储到钩子旁边的一个`__generated__`文件夹中。
*   我们导入我们的突变输入类型(TableSizeMutationVariables)和突变响应类型(TableSizeMutation)。这些是附在`useMutation`钩`useMutation<TableSizeMutation,TableSizeMutationVariables>()`上的
*   我们使用 GQL ENUM 作为分配给 format 参数的输入。这个枚举被命名为`TableFactor`，也将由 codegen 生成。鉴于它是一个全局 codegen 把它放到一个不同的文件夹。项目根/_ _ 生成了 __/apolloGlobals。一旦我们导入它，我们可以把它附加到钩子参数上，ts 将检查输入。

修改我们之前的用法来合并 TS，我们将导入相同的枚举并将其作为参数传递给`setTableSize`函数。

```
import useTableSizeMutation from './graphql/useTableSizeMutation'
import { TableFactor } from '@root/__generated__/apolloGlobals';export const MyComponent = ({id})=>{
 const setTableSize = useTableSizeMutation();return <Button onPress={()=>setTableSize(id, TableFactor.SEVEN_FOOT)} />}
```

# 但是等等，还有更多！

我们可以在这个钩子上扩展很多。在下一个版本中，我将讨论在我们的钩子中添加乐观 UI，以预测应用程序将如何响应并消除加载状态。