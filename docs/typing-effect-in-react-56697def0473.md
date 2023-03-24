# React 中的打字效果

> 原文：<https://levelup.gitconnected.com/typing-effect-in-react-56697def0473>

今天，我们将学习如何在 React 中实现打字效果。

![](img/f53dc454d0c27b1c04435a726f7b32b1.png)

照片由[拉斐拉·比亚兹](https://unsplash.com/@rafaelabiazi?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在这里可以找到一个工作示例[。](https://abecus.github.io/portfolio/)

# **设置**

首先使用`npx create-react-app my-app`创建一个 starter react 应用程序，或者在此处遵循[中的步骤。](https://reactjs.org/docs/create-a-new-react-app.html)

转到 App.js，去掉所有多余的元素，在将要发生打字效果的地方添加一个`h1`标签。

现在使用`import {useEfect, useState} from "react";`添加额外的钩子

创建一个单词数组，一个接一个地输入。

现在，我们必须跟踪单词数组的索引，以便键入特定的单词，所以我们将使用 useState 钩子，因为我们还希望在运行时更新索引(在键入单词之后)。为此用途`const [index, setIndex] = useState(0);`

此外，我们将显示单词的子串，并且每次在输入效果中，子串的长度将会改变。让它先增加再减少。所以我们必须跟踪子串的长度，以及长度是增加还是减少。为此添加额外使用状态，

```
const [subIndex, setSubIndex] = useState(0);
const [reverse, setReverse] = useState(false);
```

此外，为直线打字行添加一个`useState`挂钩，以告知何时眨眼。现在完整的代码看起来像这样，

```
import React, { useState, useEffect } from "react";
const words = ["Developer", "Programmer"];export default function App() {
  const [index, setIndex] = useState(0);
  const [subIndex, setSubIndex] = useState(0);
  const [blink, setBlink] = useState(true); 
  const [reverse, setReverse] = useState(false);return (
  <>
    <h1>
      {`${words[index].substring(0, subIndex)}${blink ? "|" : ""}`}
    </h1>
  </>)
}
```

在上面的代码中，当 blink 为 true else " "时，h1 中的行显示单词数组中位于`index`的单词的长度为`subIndex`的子字符串，该子字符串与“|”连接。

现在我们必须添加逻辑来更新`index`、`subIndex`和`blink`。

# **添加逻辑**

我们将使用带有`setTimeout`函数的`useEffect`钩子来更新值。

```
export default function App() { *...
  // blinker* useEffect(() => {
    const timeout2 = setTimeout(() => {
    setBlink((prev) => !prev);
    }, 500);
    return () => clearTimeout(timeout2);
  }, [blink]);
  ...
return (
...
}
```

`useEffect`内的函数将每 0.5 秒切换一次闪烁值。有关为什么`setTimeout`函数必须以这种方式的更多信息，请阅读 [this](https://overreacted.io/making-setinterval-declarative-with-react-hooks/) 。

现在，我们将添加更改索引和子索引值的逻辑。

```
export default function App() {
 ...*// typeWriter* useEffect(() => {
  if (
    index === words.length - 1 &&
    subIndex === words[index].length
    ) {
      return;
    } if (
    subIndex === words[index].length + 1 &&
    index !== words.length - 1 &&
    !reverse
    ) {
      setReverse(true);
      return;
     }

  if (subIndex === 0 && reverse) {
    setReverse(false);
    setIndex((prev) => prev + 1);
    return;
     } const timeout = setTimeout(() => {
   setSubIndex((prev) => prev + (reverse ? -1 : 1));
   }, 200);

   return () => clearTimeout(timeout);
   }, [subIndex, index, reverse]);
 ...
}
```

里面的代码是这样的，

如果`index`等于“单词”数组的长度-1，并且`subIndex`等于最后一个单词的长度，那么不要做任何事情。我们已经打完了所有的单词。

如果`subIndex`是当前单词的长度+1(我们已经输入了该单词)，并且我们不在单词数组的最后一个单词(以确保我们不会开始减少最后一个单词的子串的长度)，reverse 的值为 false，那么我们将 reverse 的值设置为 true 并返回。

> 改变`reverse`、`index`和`subIndex`的值将重新渲染组件，并且`useEffect`将被再次调用，因为对其设置了依赖数组。

在下一个`if`语句中，我们检查`subIndex`是否为 0 并且`reverse`是否为真，然后我们增加`index`的值并且将`reverse`设置为假。

现在主要部分，我们设置 setTimeout 函数，如果`reverse`为假，则增加；如果`reverse`为真，则每 0.2 秒减少一，这将使它像打字一样。

在`setTimeout`函数中，超时时间对双方来说都很简单，但我们可以在输入单词时使其随机(反过来为假)，在删除单词时使其小(反过来为真)。为此，我们可以用更复杂的逻辑来代替时间 200`Math.max(reverse ? 75 : subIndex===words[index].legth ? 1000:150, parseInt(Math.random()*350)));`

就这样，我们现在有了一个打字机。

**完整代码**

感谢您的阅读，如果您发现本博客有任何问题或想提出建议，欢迎您的来信。