# 反应测试—计时器和快照

> 原文：<https://levelup.gitconnected.com/react-testing-timers-and-snapshots-7e4d20ff1af9>

![](img/c580dd94a21641e23f619f1f37603a7d.png)

照片由[波格丹一世·拉帕多斯](https://unsplash.com/@bogdanlapadus?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

自动化测试对大多数应用程序来说都很重要。在本文中，我们将看看如何为 React 组件编写测试。

# 定时器

我们可以在 React 组件测试中模仿计时器。

例如，如果我们有:

```
import React, { useEffect } from "react";export default function Card({ onSelect }) {
  useEffect(() => {
    const timeoutID = setTimeout(() => {
      onSelect(null);
    }, 5000);
    return () => {
      clearTimeout(timeoutID);
    };
  }, [onSelect]); return (
    <>
      <button
        data-testid='button'
        onClick={() => onSelect(1)}
      >
        button
    </button>
    </>
  );
}
```

然后，我们可以通过编写以下代码来测试它:

`Card.test.js`

```
import React from "react";
import { render, unmountComponentAtNode } from "react-dom";
import { act } from "react-dom/test-utils";import Card from "./card";jest.useFakeTimers();let container = null;
beforeEach(() => {
  container = document.createElement("div");
  document.body.appendChild(container);
});afterEach(() => {
  unmountComponentAtNode(container);
  container.remove();
  container = null;
});it("should select null after timing out", () => {
  const onSelect = jest.fn();
  act(() => {
    render(<Card onSelect={onSelect} />, container);
  }); act(() => {
    jest.advanceTimersByTime(100);
  });
  expect(onSelect).not.toHaveBeenCalled(); act(() => {
    jest.advanceTimersByTime(5000);
  });
  expect(onSelect).toHaveBeenCalledWith(null);
});it("should clean up on being removed", () => {
  const onSelect = jest.fn();
  act(() => {
    render(<Card onSelect={onSelect} />, container);
  });
  act(() => {
    render(null, container);
  });
  act(() => {
    jest.advanceTimersByTime(5000);
  });
  expect(onSelect).not.toHaveBeenCalled();
});it("should accept selections", () => {
  const onSelect = jest.fn();
  act(() => {
    render(<Card onSelect={onSelect} />, container);
  });
  act(() => {
    container
      .querySelector("[data-testid='button']")
      .dispatchEvent(new MouseEvent("click", { bubbles: true }));
  });
  expect(onSelect).toHaveBeenCalledWith(1);
});
```

我们有通常的`beforeEach`和`afterEach`钩子来创建和移除容器，用于安装我们的测试组件。

在“超时后应选择 null”中，我们通过调用`jest.advanceTimerByTime`方法将计时器设置为我们想要的时间。

然后我们检查被模仿的`onSelect`函数用`toHaveBeenCalledWith`调用的是什么。

我们检查`onSelect`是否被`toHaveBeenCalled`调用。

在“应该在被移除时清理”测试中，我们确保`onSelect`在卸载后不会被调用。

在“应该接受选择”测试中，我们触发了按钮上的 click 事件。

然后我们检查`onSelect`是否被我们期望的参数调用。

# 快照测试

我们可以测试快照，它是在给定时间呈现的组件。

例如，如果我们有:

```
import React from "react";export default function Hello({ name }) {
  return (
    <>
      <p>hello {name}</p>
    </>
  );
}
```

然后我们可以添加下面的测试来测试组件:

`Hello.test.js`

```
import React from "react";
import { render, unmountComponentAtNode } from "react-dom";
import { act } from "react-dom/test-utils";
import pretty from "pretty";
import Hello from "./hello";let container = null;
beforeEach(() => {
  container = document.createElement("div");
  document.body.appendChild(container);
});afterEach(() => {
  unmountComponentAtNode(container);
  container.remove();
  container = null;
});it("should render a greeting", () => {
  act(() => {
    render(<Hello />, container);
  });
  expect(pretty(container.innerHTML)).toMatchInlineSnapshot(`"<p>hello </p>"`); act(() => {
    render(<Hello name="james" />, container);
  });
  expect(pretty(container.innerHTML)).toMatchInlineSnapshot(
    `"<p>hello james</p>"`
  );
});
```

我们有和以前一样的`beforeEach`和`afterEach`挂钩。

为了测试该组件，我们用`container.innerHTML`获得了呈现的 HTML。

然后我们用`toMatchInlineSnapshot`对照 HTML 代码检查它。

我们必须通过运行来安装`pretty`和`prettier`:

```
npm i pretty prettier --save-dev
```

获取`pretty`函数来呈现 HTML。

# 结论

我们可以模仿计时器，用渲染的 HTML 测试 React 组件。