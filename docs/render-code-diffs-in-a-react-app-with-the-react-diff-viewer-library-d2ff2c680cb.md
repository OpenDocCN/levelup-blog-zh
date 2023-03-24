# 使用 React 比较查看器库在 React 应用程序中呈现代码差异

> 原文：<https://levelup.gitconnected.com/render-code-diffs-in-a-react-app-with-the-react-diff-viewer-library-d2ff2c680cb>

![](img/98b38e3bddf2357497d8f3663b1bdab3.png)

凯文·Ku 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

为了在 React 应用程序中更容易地呈现代码差异，我们可以使用 React Diff Viewer 库。

# 装置

我们可以通过运行以下命令来安装该软件包:

```
npm i react-diff-viewer
```

或者:

```
yarn add react-diff-viewer
```

# 渲染差异

为了在 React 应用程序中呈现差异，我们可以使用`ReactDiffViewer`组件。

例如，我们可以写:

```
import React from "react";
import ReactDiffViewer from "react-diff-viewer";const oldCode = `
const a = 10
const b = 10
const c = () => console.log('foo')

if(a > 10) {
  console.log('bar')
}

console.log('done')
`;
const newCode = `
const a = 10
const boo = 10

if(a === 10) {
  console.log('bar')
}
`;export default function App() {
  return (
    <div className="App">
      <ReactDiffViewer oldValue={oldCode} newValue={newCode} splitView={true} />
    </div>
  );
}
```

我们将旧代码串传入`oldValue`属性，将新代码传入`newValue`属性。

同样，我们可以用`hideLineNumers`道具隐藏行号。

`splitView`并排显示新旧代码。

# 比较方法

我们可以用`compareMethod`道具改变比较方法:

```
import React from "react";
import ReactDiffViewer from "react-diff-viewer";
import Prism from "prismjs";const oldCode = `
const a = 10
const b = 10
const c = () => console.log('foo')

if(a > 10) {
  console.log('bar')
}

console.log('done')
`;
const newCode = `
const a = 10
const boo = 10

if(a === 10) {
  console.log('bar')
}
`;export default function App() {
  return (
    <div className="App">
      <ReactDiffViewer
        oldValue={oldCode}
        newValue={newCode}
        splitView={true}
        compareMethod="diffWords"
      />
    </div>
  );
}
```

# 式样

我们可以通过传入`styles`道具来改变样式:

```
import React from "react";
import ReactDiffViewer from "react-diff-viewer";
import Prism from "prismjs";const oldCode = `
const a = 10
const b = 10
const c = () => console.log('foo')

if(a > 10) {
  console.log('bar')
}

console.log('done')
`;
const newCode = `
const a = 10
const boo = 10

if(a === 10) {
  console.log('bar')
}
`;const newStyles = {
  variables: {
    dark: {
      highlightBackground: "#fefed5",
      highlightGutterBackground: "#ffcd3c"
    }
  },
  line: {
    padding: "10px 2px",
    "&:hover": {
      background: "purple"
    }
  }
};export default function App() {
  return (
    <div className="App">
      <ReactDiffViewer
        oldValue={oldCode}
        newValue={newCode}
        splitView={true}
        styles={newStyles}
      />
    </div>
  );
}
```

我们将其设置为具有特定属性的对象来设置样式。

`highlightBackground`设置高亮显示的背景颜色。

`highlightGutterBackground`设置装订线的颜色。

`line`是每一行的样式。

我们可以用`&:hover`属性改变一行的悬停样式。

# 结论

我们可以使用 React Diff Viewer 组件将代码比较视图添加到 React 应用程序中。