# 反应测试—开始

> 原文：<https://levelup.gitconnected.com/react-testing-getting-started-85ee8961fb78>

![](img/e0320a6250144a2dd59d885b0a113f83.png)

[绿色变色龙](https://unsplash.com/@craftedbygc?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

自动化测试对大多数应用程序来说都很重要。在本文中，我们将看看如何为 React 组件编写测试。

# 测试工具

Create React App 在项目中内置了测试文件和脚本。

# 首次测试

我们可以通过在 Create React App 项目中添加测试文件来编写我们的第一个测试。

如果我们有`App.js`:

```
import React from 'react';
import logo from './logo.svg';
import './App.css';function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
    </div>
  );
}export default App;
```

在`App.test.js`中，我们写道:

```
import React from 'react';
import { render } from '@testing-library/react';
import App from './App';test('renders learn react link', () => {
  const { getByText } = render(<App />);
  const linkElement = getByText(/learn react/i);
  expect(linkElement).toBeInTheDocument();
});
```

添加测试。

`render`函数呈现我们导入的`App`组件。

然后我们用一个正则表达式调用`getByText`来获得我们正在寻找的元素。

最后，我们调用`toBeInTheDocument`来检查`linkElement`是否存在。

# 数据提取

我们用 React 测试添加安装和拆卸代码。

此外，我们可以在测试代码中模拟任何 HTTP 请求，这样我们就可以独立运行我们的测试。

例如，如果我们有以下组件:

`Answer.js`

```
import React, { useState, useEffect } from "react";export default function Answer(props) {
  const [data, setData] = useState({}); async function fetchData() {
    const response = await fetch("https://yesno.wtf/api");
    setData(await response.json());
  } useEffect(() => {
    fetchData(props.id);
  }, []); return (
    <div>
      <p>{data.answer}</p>
    </div>
  );
}
```

然后我们必须模仿获取 API。

为此，我们可以写:

`Answer.test.js`

```
import React from "react";
import { render, unmountComponentAtNode } from "react-dom";
import { act } from "react-dom/test-utils";
import Answer from "./Answer";let container = null;
beforeEach(() => {
  container = document.createElement("div");
  document.body.appendChild(container);
});afterEach(() => {
  unmountComponentAtNode(container);
  container.remove();
  container = null;
});it("renders answer data", async () => {
  const fakeData = {
    answer: "yes",
    forced: false,
    image: "https://yesno.wtf/assets/yes/6-304e564038051dab8a5aa43156cdc20d.gif"
  };
  jest.spyOn(global, "fetch").mockImplementation(() =>
    Promise.resolve({
      json: () => Promise.resolve(fakeData)
    })
  ); await act(async () => {
    render(<Answer />, container);
  }); expect(container.querySelector("p").textContent)
    .toBe(fakeData.answer);
  global.fetch.mockRestore();
});
```

在上面的代码中，我们使用了`container`来安装我们的组件。

我们使用`beforeEach`钩子来创建容器元素，以便在其中安装我们的组件。

我们用`afterEach`钩子卸载我们的组件并移除容器。

然后在我们的`'render answer data'`测试中，我们用模拟响应数据创建了`fakeData`对象。

然后我们通过使用`jest.spyOn`方法模拟`Answer.js`中的`fetch`函数。

`global`是全局对象，`'fetch'`是`fetch`函数。

`mockImplementation`让我们用一个带有`json`方法的对象模拟`fetch`，该方法被设置为一个函数，该函数返回一个解析为假数据的承诺。

然后我们用`act`和`await`安装我们的`Answer`组件，让承诺得以解决。

然后我们检查`container`的`p`元素中有什么，以检查它是否有我们期望的内容。

最后，我们调用`global.fetch.mockRestore()`来清除模拟。

# 结论

我们可以用 Jest 和 React 的测试库添加测试和模拟任何数据获取代码。