# 用普通 JS 编写单页应用程序

> 原文：<https://levelup.gitconnected.com/writing-a-single-page-application-with-vanilla-js-a5c7f7e23f6d>

![](img/012023a97d8b73385b810929496d14dc.png)

马尔科姆·曼纳斯拍摄的开花香草植物的照片

最近，我遇到了以下关于单页面应用程序(SPA)中刷新状态处理的堆栈溢出问题。我发现 OP 开发 SPA 的方法很有趣:他们想避免第三方库和框架，因为他们“更喜欢简单的方法”。

这篇文章中的问题是:

[](https://stackoverflow.com/a/64440218/3482831) [## 如何处理浏览器刷新按钮的这种中断行为

### 我做了一个简单的概念证明来说明如何实现你所寻找的，你可以在这里看到…

stackoverflow.com](https://stackoverflow.com/a/64440218/3482831) 

我做了一个简单的概念证明来说明如何实现他们所寻找的东西，可以在这里看到(或者从 [github](https://github.com/lucasreta/vanilla-spa) 克隆而来)。

在上传的版本中，我使用最后一个 URL 参数来确定应该加载什么。这是标准做法，但是需要服务器端配置，以便将所有传入的请求路由到单个文件。

另一种选择(github 上代码的默认选项)是使用锚片段。我的脚本支持这两种选择，你可以在这里看到[上传的版本使用锚代替 URL 重写。](https://lucasreta.com/stack-overflow/spa-vanilla-js-no-rewrite/#section-2)

代码非常简单(尽管可以通过使用 fetch 或不太冗长的 XHR 实现来进一步简化)。

# 超文本标记语言

这里我们只关心两件事:

*   `#content-box`部分(第 21 行)，在这里我们将放置从我们的 API 加载的任何内容。
*   用于内部路由的`a`元素，必须有与之关联的类`link`(第 14–17 行)。

# Java Script 语言

首先我们初始化几个全局变量:

```
const useHash = true;
const apiUrl = 'https://lucasreta.com/stack-overflow/spa-vanilla-js/api';
const routes = ['section-1', 'section-2'];
const content_box = document.getElementById("content_box");
```

`useHash`将决定我们是否应该使用 URL 的锚(散列),或者我们内部路由的最后一个参数。

`apiUrl`设置我们简单 API 的基本 URL。

`routes`定义了我们的应用程序的有效路径。

`content_box`是我们将使用外部数据更新的 DOM 元素。

然后我们定义我们的异步 getter，它仍然是一个非常标准的 XHR 调用，类似于查询者在示例代码**(错误处理缺失)**中的调用:

```
function get(page) {
  const xhr = new XMLHttpRequest();
  xhr.onreadystatechange = function() {
    if (this.readyState == 4 && this.status == 200) {
      data = JSON.parse(xhr.responseText);
      content_box.innerHTML = data.content;
      const title = `${data.title} | App Manual`;
      document.title = title;
      window.history.pushState(
        { 'content': data.content, 'title': title},
        title,
        useHash ?
          `#${page}` :
          page
      );
    }
  };
  xhr.open('GET', `${apiUrl}/${page}`, true);
  xhr.send();
}
```

这里，我们向我们的`get`函数发送一个名为`page`的参数，该参数匹配我们将使用的 API 的端点以及我们将在状态和 URL 中使用的名称，以确定我们必须显示的内容。

最后，我们必须处理单页应用程序状态被修改的三个事件:

```
// add event listener to links
const links = document.getElementsByClassName('link');
for(let i = 0; i < links.length; i++) {
  links[i].addEventListener('click', function(event) {
    event.preventDefault();
    get(links[i].href.split('/').pop());
  }, false);
}// add event listener to history changes
window.addEventListener("popstate", function(e) {
  const state = e.state;
  document.title = state.title;
  content_box.innerHTML = state.content;
});// add ready event for initial load of our site
(function(fn = function() {
  const page = useHash ?
    window.location.hash.split('#').pop() :
    window.location.href.split('/').pop();
  get(routes.indexOf(page) >= 0 ? page : routes[0]);
}) {
  if (document.readyState != 'loading'){
    fn();
  } else {
    document.addEventListener('DOMContentLoaded', fn);
  }
})();
```

首先，我们用类`.link`获取所有元素，并为它们附加一个事件监听器，这样当它们被点击时，默认事件被停止，取而代之的是用 href 的最后一个参数调用我们的`get`函数。

因此，当我们单击上面列出的第一个链接时，我们将对`api.com/section-1`执行一个 GET 请求，并将应用程序的 URL 更新为`app.com/section-1`或`app.com/#section-1`。

这里是我的实现的两个限制:

*   API 和应用程序路由必须匹配。
*   路线不能有多个参数。

两者都是可修复的，我不会详细说明，因为它避开了简单概念验证的要点，但我必须指出这一点。第一个问题可以通过使用某种字典来解决，该字典将我们的路由匹配到它们应该获取的端点。第二个问题可以通过使链接的事件监听器中的逻辑更复杂一点来解决，扩展简单的`links[i].href.split('/').pop()`以包含所有期望的参数。

接下来，我们有了历史变化的事件监听器。由于我们将 API 返回的内容存储在历史状态本身中，所以当历史发生变化时，我们所要做的就是用我们的`state.content`重新填充`content_box`。

最后，我们有 ready 函数，在 DOM 最初结束加载时调用，我们检查我们的 URL 以获得最后一个参数或 hash/anchor 的值，然后验证我们从 URL 获得的内容是否存在于我们定义的内部路由数组中。如果是的话，我们调用我们的`get`函数，把它作为一个参数。如果没有，我们从数组中取出第一条路径，用它来调用它。

# 这是更简单的方法吗？

虽然我认为任何人都可以从了解这一点和如何做到这一点中受益，但我不知道我是否同意 OP 的观点，即避免第三方库是做事的“更简单的方式”。即使这个简单的 POC 也有缺陷，如果试图将其扩展到更复杂的场景中，就会暴露出更多的缺陷。

我认为这种方法的简单性仅仅是对当前前端框架的复杂性的一种更没有根据的认识的结果。我鼓励任何人在抛弃他们并选择普通的 JS 路线之前，给 [Vue](https://vuejs.org/) 或 [React](https://reactjs.org/) 一个机会。

***关于我:*** *我是一名拥有超过 7 年经验的全栈式 web 开发人员，目前正在接受新的机会。你可以查看* [*我的网站*](https://lucasreta.com/en/) *做进一步参考或者直接写信给我*[*1.lucasreta@gmail.com*](mailto:1.lucasreta@gmail.com)