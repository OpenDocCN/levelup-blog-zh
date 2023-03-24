# 处理 HTML 输入中的数值

> 原文：<https://levelup.gitconnected.com/handling-number-values-in-html-inputs-3a43cb6e6c44>

## 阅读文档并保存代码行

![](img/e96f5467e7a80c75c3093196a47d3e81.png)

由[马修·奎罗斯](https://unsplash.com/@queirozmm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

当建立一个网站时，你会遇到建立一个表单的必要性。你知道这样的事情:

```
<form>
  <input type='text' placeholder='Enter your name'/>
</form>
```

要访问表单内部输入的值，通常要根据每次更改时触发的事件来访问它的值:

```
const input = document.querySelector('input')
input.addEventListener(event => console.log(event.target.value))
```

很简单，对吗？

当输入代表一个数字时，“问题”就出现了。您通常会这样声明输入:

```
<input type='number' placeholder='Enter your age' />
```

使用上面的事件监听器仍然很容易访问该值。

您使用 API 将该值发送到 DB，DB 抱怨它期望一个数字，但是您发送了一个字符串。**对，一串！**

您感到困惑，并开始记录您的值的类型，如下所示:

```
input.addEventListener(event => {
  const value = event.target.value
  console.log(typeof(value))
})
```

而且惊喜惊喜它打印出**字符串**。您现在检查 HTML 输入的[文档，并意识到输入事件的值将始终是一个字符串。](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement)

所以你很想做这样的事情:

```
input.addEventListener(event => {
  const value = event.target.value
  const valueAsNumber = parseInt(value) // or parseFloat
  console.log(typeof(value))
})
```

现在你的值是一个数字，你的 DB 很高兴。

事情是这样的，没有必要使用`parseInt/parseFloat`来获取数值。向下滚动 HTML 输入的[文档，您会看到一个名为`valueAsNumber`的属性，它将尝试按顺序返回值:时间值、数字或 NaN。](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement)

因此，您的代码现在看起来像这样:

```
input.addEventListener(event => {
  const value = event.target.valueAsNumber
  console.log(typeof(value))
})
```

这可能看起来是一个很小的变化，也许没有必要，但是你需要写的代码越少，你的代码库中的维护或错误就越少。

[](https://blog.jagonzalr.com/membership) [## 加入我的介绍链接媒体-何塞安东尼奥冈萨雷斯罗德里格斯

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

blog.jagonzalr.com](https://blog.jagonzalr.com/membership)