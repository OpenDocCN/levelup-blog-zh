# 用 React 挂钩获取 API

> 原文：<https://levelup.gitconnected.com/fetch-an-api-with-react-hooks-79d509a052a0>

在我之前的博文[更好的](/clean-components-with-react-hooks-4cfef201176e#is-it-safe-to-omit-functions-from-the-list-of-dependencies) [解决方案](https://reactjs.org/docs/hooks-faq.html#what-can-i-do-if-my-effect-dependencies-change-too-often)来避免过于频繁的重新运行效果。此外，不要忘记 React 将运行`useEffect`推迟到浏览器完成绘制之后，所以做额外的工作不是问题。"

我们将空数组传递到`useEffect`中，以确保效果运行一次，并防止组件永久重新渲染。函数组件的 return 语句通过调用格式`nasaData.title`显示属性。

![](img/ea69386afb8e62e8e64a20aa3413c76e.png)

美国宇航局每日天文图片 API 渲染的数据

这两个组件现在都可以成功地获取 NASA 的 API，并呈现标题、图像、摄影师和日期。要探索代码或使用这些组件，请访问这里的 [GitHub 库](https://github.com/PrestonElliott/React-Hooks)！

![](img/e9b0575bb6df894176f3afbe9714272f.png)

[来源](https://unsplash.com/photos/Lwe2hbm5XKk)