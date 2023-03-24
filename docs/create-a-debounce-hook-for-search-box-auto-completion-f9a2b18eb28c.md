# 为搜索框自动完成创建一个反跳挂钩

> 原文：<https://levelup.gitconnected.com/create-a-debounce-hook-for-search-box-auto-completion-f9a2b18eb28c>

![](img/567b1f3abac09a5d0e432abca1106826.png)

照片由[阿诺德·弗朗西斯卡](https://unsplash.com/@clark_fransa?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

常见的情况是，我们必须为网站上的项目实现搜索功能。自动补全是改善用户搜索体验的一个重要特性。

我们知道，每当搜索框的输入发生变化时，我们可以利用 React 中的 useEffect 钩子来调用 fetch API。但是，只要用户键入一个字符，就会触发 API 调用。当要搜索的数据集越来越大，或者用户的互联网连接速度很慢时，这可能会导致一些性能问题。为了防止如此频繁地碰到 API 端点，我们可以为我们的自动完成特性实现一个去抖功能。

用于进行查询的去抖功能是减慢查询过程的一种方式，并且将仅在用户停止键入的特定时间量之后发出请求。这个想法是用这个钩子包装我们想要跟踪的状态，并在等待一段时间后才设置状态。之后，在调用 API 的 useEffect 钩子中，我们将使用钩子返回的状态进行 API 调用。

这是钩子的样子:

```
import { useState, useEffect } from 'react';const useDebounce = (value, timeout = 500) => {
    const [state, setState] = useState(value); useEffect(() => {
        const handler = setTimeout(() => setState(value), timeout); return () => clearTimeout(handler);
    }, [value, timeout]); return state;
}export default useDebounce;
```

想法是使用传入的值(查询文本)作为初始状态，并监听值的变化。每当值改变时，该函数的 useEffect 钩子将被触发，并在一定时间后尝试 setState(我们定义的 setTimeout)。但是，如果用户继续输入，useEffect 挂钩将再次被触发，useEffect(返回语句)的清理将首先运行，以清除之前的超时处理程序。这就是为什么状态只会在用户停止输入后返回。

这就是我们如何在 fetch 调用 useEffect 钩子中使用这个钩子(我只展示了最少量的代码来演示):

```
import React, { useState, useEffect } from 'react';const [query, setQuery] = useState("");
const debouncedQuery = useDebounce(query, 500);useEffect(() => {
            const fetchResults = () => {
                fetch(`https://api.someapi.com/search?q=${debouncedQuery}`)
                .then(result => result.json())
                .then(data => {
                    // parsed the data and set the options
                })
                .catch(err => console.error(err))
            }
            fetchResults();
    }, [debouncedQuery]);
```

点击链接查看现场演示:[现场演示](https://cat-breedy.vercel.app/)

最近 React 18 推出了一个新的钩子——`useDeferredValue`。它接受一个值并推迟它的更新。这个钩子的好处是我们没有为去抖设置一个硬编码的固定时间，一旦其他工作完成，它就会返回。

```
const deferredValue = useDeferredValue(value);
```

你可以在 React 官方文档中查看这个钩子:[链接](https://reactjs.org/docs/hooks-reference.html#usedeferredvalue)。

如果你想看更多的网络开发或软件工程相关的内容，请关注我。干杯！