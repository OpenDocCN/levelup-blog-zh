# React 实用程序组件:构建 4 个 React 组件来改进任何应用程序

> 原文：<https://levelup.gitconnected.com/react-utility-components-building-4-react-components-that-improve-any-app-2fa0e4672e0f>

## 如何构建 4 个可以立即集成的 React 附加组件来解决应用中的常见问题

![](img/9d9c6dde43c0ed7861e731206456c637.png)

React 即使不是最受欢迎的 JavaScript 库，也是其中之一，但它不像其他框架那样自带助手(即 Vue.js 中的*指令*)。

我将向您展示如何构建 **4 个有用且可重用的 react 组件**来最大化您的编码效率。

我们将构建一个条件呈现组件、一个处理破损图像的组件、一个处理迭代的组件和一个表格组件。

# 条件呈现组件

`If`组件是具有`condition`和`otherwise`属性的功能组件。

`condition`属性是一个非常简单的条件语句。如果给定的条件为真，将返回一个预定义的[子属性](https://reactjs.org/docs/glossary.html#propschildren)，否则将呈现传入`otherwise`属性的任何值，或者什么都不呈现。

## 使用

```
<If condition={flag} otherwise="render that">
  render this...
</If>
```

## If.js

```
import React from 'react';
import Proptypes from 'prop-types';function If(props) {
  return props.condition ? props.children : props.otherwise;
}If.propsTypes = {
  condition: Proptypes.bool,
  otherwise: Proptypes.oneOfType([
      Proptypes.arrayOf(Proptypes.node),
      Proptypes.node
  ]),
  children: Proptypes.oneOfType([
    Proptypes.arrayOf(Proptypes.node),
    Proptypes.node
  ])
};If.defaultProps = {
  otherwise: null
};export default If;
```

> *我假设您已经在应用程序中安装了* `*prop-types*` *或其他类型的检查包。*

# 破碎的图像

`Image`组件用一个`fallback`属性(image)替换图像的破损`src`作为其默认占位符。

## 使用

```
<Image src={pathToBrokentImage} alt="..." />
```

## Image.js

```
import React from 'react';
import Proptypes from 'prop-types';function Image(props) {
  const { fallback, alt, ...rest } = props;
  const handleBrokenImage = (event) => event.target.src = fallback; return <img {...rest} alt={alt} onError={handleBrokenImage} />;
}Image.propTypes = {
  fallback: Proptypes.string,
  alt: Proptypes.string,
};Image.defaultProps = {
  alt: 'Default alt for a11y',
  fallback: 'path/to/default/image/or/placeholder'
};export default Image;
```

> 注意:我将创建一个简单的 arrow 函数作为 util，在接下来的两个组件中使用，为每个元素生成一个随机的[键](https://reactjs.org/docs/lists-and-keys.html#keys),因为我们将遍历元素的数据列表(以防止控制台中出现任何警告/错误日志)
> 
> `const getRandomKey = () => Math.random().toString(36).substr(2, 3);`

# 将数组映射到元素

`For`组件迭代接受数据数组的`of`属性。例如，这可能是字符串列表或对象列表。

## 使用

```
const data = ['...', '...', '...'];<For of={data} type='p' />const anotherData = [
  {
   label: '...',
   value: '...',
  }
  {
   label: '...',
   value: '...',
  }
  {
   label: '...',
   value: '...',
  }
];<For of={anotherData} type='li' parent='ul' iteratee='label' />
```

如果没有提供`iteratee`属性，组件将返回数组中每个对象的第一个键值。

## For.js

```
import React, { PureComponent, createElement } from 'react';
import Proptypes from 'prop-types';export default class For extends PureComponent {
  static propTypes = {
    of: Proptypes.array,
    type: Proptypes.string.isRequired,
    parent: Proptypes.string,
    iteratee: Proptypes.string,
  }; getIteratee = (item) => {
    return item[this.props.iteratee] || item[Object.keys(item)[0]];
  }; list = () => {
    const { of, type } = this.props;
    return of.map((item) => {
      const children = typeof item === 'object' ? this.getIteratee(item) : item;
      return createElement(type, {
        key: getRandomKey()
      }, children)
    })
  }; children = () => {
    const { parent } = this.props;
    return parent ? createElement(parent, null, this.list()) : this.list();
  }; render() {
    return this.props.of.length ? this.children() : null;
  }
}
```

# 数据表

一个基本的`Table`组件，呈现一个带有`headers`、`body`和`footer`的数据表。

## 使用

```
const data = {
  headers: ['...', '...'],
  body: [
    ['...', '...'],
    ['...', '...'],  
  ],
  footer: ['...', '...'],
};<Table {...data} />
```

## Table.js

> 你可以通过增加更多的选项来增加挑战性。比如各种表格布局等等。

```
import React from 'react';
import Proptypes from 'prop-types';export default class Table extends React.PureComponent {
  static propTypes = {
    header: Proptypes.array,
    body: Proptypes.array,
    footer: Proptypes.array,
  }; static defaultProps = {
    header: [],
    body: [],
    footer: [],
  }; static Cells = ({ data = [], cell = 'td' }) => data.length ? (
      data.map((d) => (
          cell === 'th' ?
              <th key={`th-${getRandomKey()}`}>{d}</th> :
              <td key={`td-${getRandomKey()}`}>{d}</td>
      ))
  ) : null; render() {
    const { header, body, footer, ...rest } = this.props;
    const bodyRows = body.map((row) => (
        <tr key={`th-${getRandomKey()}`}>
          <Table.Cells data={row} />
        </tr>
    )); return (
        <table {...rest}>
          {header.length ? (
              <thead>
                <tr>
                  <Table.Cells data={header} cell="th" />
                </tr>
              </thead>
          ) : null}
          {body.length ? <tbody>{bodyRows}</tbody> : null}
          {footer.length ? (
              <tfoot>
                <tr>
                  <Table.Cells data={footer} />
                </tr>
              </tfoot>
          ) : null}
        </table>
    )
  }
}
```

# 演示

我做了一个简单的应用程序来玩。从下面的演示中可以看出，它有几个部分。每个组件都有一个样本测试。请随意使用这些代码。