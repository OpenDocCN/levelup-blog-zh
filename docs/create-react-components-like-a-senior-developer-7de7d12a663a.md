# 像高级开发人员一样创建 React 组件

> 原文：<https://levelup.gitconnected.com/create-react-components-like-a-senior-developer-7de7d12a663a>

![](img/957121b6b24f71ccdb4e0731e87f1fb4.png)

在 React 中创建可重用的组件可以大大提高代码库的效率和可维护性。通过构建可以在整个应用程序中轻松重用的组件，您可以避免重复，并确保在一个位置对组件所做的更改会反映到使用它的所有位置。

下面是一些在 React 中创建可用组件的技巧。

# 保持简单

一个组件应该只负责一个特定的任务或功能。避免创建试图做太多事情的复杂组件。

例如，如果您有一个处理登录的组件，那么让该组件同时处理给定产品的商店位置就没有意义了。

# 使用道具和上下文

通过 props 向组件传递数据，可以使组件更加灵活和可重用。这允许您在不同的上下文中使用相同的组件和不同的数据。

1.  下面是一个显示项目列表的可重用 React 组件的示例:

```
import React from 'react';

const List = (props) => {
  return (
    <ul>
      {props.items.map((item) => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
};

export default List;
```

该组件接受一个名为`items`的道具，它是一个具有`id`和`name`属性的对象数组。然后它遍历`items`数组，并使用`name`属性显示列表中的每一项。

要使用这个组件，您可以将它导入到您的组件中，并像这样传入`items` prop:

```
const MyComponent = () => {

  const items = [
    { id: 1, name: 'Item 1' },
    { id: 2, name: 'Item 2' },
    { id: 3, name: 'Item 3' },
  ];

  return (
      <div>
        <List items={items} />
      </div>
    );
  };
}

export default MyComponent;
```

然后，这个组件可以在整个应用程序的多个地方使用，通过简单地传入不同的`item`道具就可以显示不同的列表。

# 使用 Typescript 定义属性

React 中函数组件中的默认属性已被弃用，建议从现在开始在定义属性类型时使用 TypeScript。

[了解更多关于使用 TypeScript 定义 prop 类型的信息，并通过阅读本文做出反应。](https://medium.com/@christopherclemmons/code-like-a-pro-define-types-in-react-components-using-typescript-de3ae7be6223)

TypeScript 是一种流行的编程语言，它为 JavaScript 添加了可选的静态类型。它可以在 React 项目中使用，通过在错误到达浏览器之前捕捉错误来改善开发体验。在 TypeScript 中创建 React 组件时，可以使用接口定义属性和状态的类型。

下面是一个使用 TypeScript 定义属性类型的例子。

```
import React from 'react';

interface Props {
  name: string;
  age: number;
  onClick: (event: React.MouseEvent<HTMLButtonElement>) => void;
}

const MyComponent: React.FC<Props> = ({ name, age, onClick }) => {
  return (
    <div>
      <p>Name: {name}</p>
      <p>Age: {age}</p>
      <button onClick={onClick}>Click me</button>
    </div>
  );
}

export default MyComponent;
```

在这个例子中，组件`MyComponent`接受三个道具:`name`、`age`和`onClick`。`Props`界面定义了每个道具的类型。`name`是字符串，`age`是数字，`onClick`是以`React.MouseEvent<HTMLButtonElement>`为参数并返回 void 的函数。该组件使用这些属性来呈现一些文本和一个按钮。

当使用这个组件时，您必须以一种尊重接口中定义的类型的方式来传递 props。

```
<MyComponent
  name='John Doe'
  age={20}
  onClick={(event) => {console.log('clicked')}}
/>
```

该组件也可以用状态来编写，在这种情况下，状态的类型将在另一个接口中定义，并作为第二个类型参数传递给`React.FC`组件，如下所示:

```
interface State {
  count: number;
}

const MyComponent: React.FC<Props, State> = ({ name, age, onClick }, state) => {
  return (
    <div>
      <p>Name: {name}</p>
      <p>Age: {age}</p>
      <p>Count: {state.count}</p>
      <button onClick={onClick}>Click me</button>
    </div>
  );
}
```

通过这种方式，您可以清楚地了解组件的属性和状态的类型，并在开发过程的早期发现任何错误。

# 测试您的组件

请务必彻底测试您的组件，以确保它按预期工作并且易于使用。我会推荐使用 [JEST](https://jestjs.io/) ，这是一个优秀的 React 测试框架。用 Jest 测试 React 组件有几个好处，包括:

## 提高代码质量

通过为 React 组件编写和运行测试，您可以在开发过程的早期发现并修复错误，这有助于提高代码的整体质量。

## 增强的可靠性

通过编写覆盖不同场景和边缘案例的测试，您可以确保您的 React 组件是可靠的，并且在不同的条件下正常工作。

## 简化调试

如果测试失败，Jest 会提供详细的错误消息，帮助您快速识别和修复问题。

## 改善协作

通过编写测试，您可以为如何使用 React 组件提供清晰的文档，这对从事同一项目的团队成员很有帮助。

## 提高速度和效率

Jest 具有快照测试和并行测试执行等特性，这有助于加快测试过程，使其更加高效。

# 最后的想法

总之，创建可重用的 React 组件需要对这些实践和业务需求有很好的理解。请记住，要始终考虑组件的可维护性和可伸缩性，因为它们可能会在多个项目中使用，并且可能会被团队中的其他开发人员使用。通过实践和对细节的关注，您可以创建高质量的 React 组件，这对任何项目来说都是宝贵的资产。