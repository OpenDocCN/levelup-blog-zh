# 编写干净的 React 代码

> 原文：<https://levelup.gitconnected.com/writing-clean-react-code-74b42f9cc70c>

![](img/4fb442c4b6d9f616f0a0e2b52b404eb6.png)

由[万基·金](https://unsplash.com/@kimdonkey?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

React 是一个用于创建应用程序的流行库。虽然库让我们的生活变得更容易，但编写糟糕的代码却很容易让我们的生活变得更艰难。

在这篇文章中，我们将看看如何编写干净的 React 代码，这样我们的生活最终会更轻松。

# 使反应元件尽可能短

组件应该尽可能的短，这样我们就不会有阅读和重构代码的困难。

易读代码易于阅读和理解，易于维护。

React 组件可以轻松扩展到数百甚至数千行代码。这对任何阅读的人来说都将是一场噩梦，而且对如此大的组件进行修改会更加困难。

因此，我们应该将组件重构为尽可能小的部分。如果我们有逻辑，我们应该把它们放到帮助函数中。

帮助功能可以重用，这就更好了。

例如，我们可以这样做:

```
import React, { useState, useEffect } from "react";const getPerson = async () => {
  const res = await fetch("https://api.agify.io?name=michael");
  return res.json();
};const Person = () => {
  const [person, setPerson] = useState({});
  const getData = async () => {
    const p = await getPerson();
    setPerson(p);
  };
  useEffect(() => {
    getData();
  }, []); return <p>{person.name}</p>;
};export default function App() {
  return (
    <div className="App">
      <Person />
    </div>
  );
}
```

在上面的代码中，我们有一个`getPerson`助手函数，它从一个 API 获取数据，并返回一个我们可以在组件内部使用的承诺。

然后在`Person`中，我们调用`getData`函数中的`getPerson`帮助函数，这样我们可以将响应数据设置为组件内部的状态并显示出来。

然后在`App`组件内部使用`Person`组件。这样，组件只有几行长，内部没有很多逻辑。

每个组件中的行数也很少。

# 处于同一抽象层次的组件应该在一起

同级别的职能应该在一起。这也适用于 React 组件。

例如，如果我们有一个文本编辑器应用程序，它有一个菜单和一个文本编辑器，那么它们应该一起呈现，因为它们都是根级别的 UI 元素。

例如，我们可以编写下面的代码来一起呈现它们:

```
import React from "react";const Menu = () => {
  return (
    <div>
      <button>Save</button>
      <button>Close</button>
    </div>
  );
};const TextEditor = () => {
  return <textarea />;
};export default function App() {
  return (
    <div className="App">
      <Menu />
      <TextEditor />
    </div>
  );
}
```

在上面的代码中，我们用`Menu`和`TextEditor`组件将我们的文本编辑器分成两个组件。然后我们在`App`中把他们两个列入同一级别。

这就是我们想要的，因为它们都是根级别的 UI 组件，所以它们应该放在一起放在顶部。

如果我们不这样做，那么我们的大脑会感到困惑，因为如果它们都应该处于最高水平，那么为什么有些组件处于不同的水平呢？

![](img/abf82286121a0dcd5973f132b0dd5d1a.png)

照片由 [averie woodard](https://unsplash.com/@averieclaire?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 把道具的数量减到最少

函数应该有尽可能少的参数，以使它们易于阅读，并减少在传递参数时出错的机会，如以错误的顺序传递参数或传递错误数据类型的参数等，

同样，我们应该通过减少传入的道具数量来清理 React 组件。

例如，不写:

```
import React from "react";const Greeting = ({ greeting, firstName, lastName }) => {
  return (
    <div>
      {greeting}, {firstName} {lastName}
    </div>
  );
};export default function App() {
  return (
    <div className="App">
      <Greeting greeting="hello" firstName="jane" lastName="smith" />
    </div>
  );
}
```

当`Greeting`组件采用 3 个道具时，我们可以将它们组合如下:

```
import React from "react";const Greeting = ({ greeting, person: { firstName, lastName } }) => {
  return (
    <div>
      {greeting}, {firstName} {lastName}
    </div>
  );
};export default function App() {
  return (
    <div className="App">
      <Greeting
        greeting="hello"
        person={{ firstName: "jane", lastName: "smith" }}
      />
    </div>
  );
}
```

在上面的代码中，我们通过将`firstName`和`lastName`组合成`person`道具，将道具的数量减少到 2 个。这减少了我们大脑的认知负荷，因为我们只需要看两个道具。

在`Greeting`，我们仍然有和以前一样的破坏操作。唯一的区别是我们必须析构`person`道具，而不是顶层的`firstName`和`lastName`。

# 结论

小部件好。它们更容易阅读和修改。

此外，我们应该减少组件所用的道具数量，使其更易于阅读。

最后，具有相同抽象级别的组件应该在相同的级别上呈现。