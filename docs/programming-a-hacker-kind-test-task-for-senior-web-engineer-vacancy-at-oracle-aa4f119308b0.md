# 为甲骨文公司的高级网络工程师职位空缺编写一个类似黑客的测试任务

> 原文：<https://levelup.gitconnected.com/programming-a-hacker-kind-test-task-for-senior-web-engineer-vacancy-at-oracle-aa4f119308b0>

2021 年春天，我一直在想有没有空房。引起我注意的是甲骨文公司远程高级网络工程师的职位空缺。在寄出我的简历后，我收到了许多可供选择的测试任务。更进一步，我设法完成了它，并得到了一份工作。

![](img/e93ab4adbae7e7a1be73cf795d4a5960.png)

一个特别有趣的测试任务是实现四种从后端接收网页数据的方法，而不使用任何类型的 AJAX 及其 XMLHttpRequestObject。让我们回顾一下这四个想法。

## IFrame 注射液

第一个是在页面上创建一个 iframe 元素。Iframe 标签允许在现有网页中打开一个新的网页。在该页面中，我们可以找到需要从服务器接收的数据。DOM 包含 iframe 元素的事件，该事件允许通过 *textContent* 属性读取其内容。

代码如下所示:

```
 let iframeEl = document.createElement("iframe");
    iframeEl.style.display = "none";
    iframeEl.setAttribute("src", "/iframe-data.txt");

    let getContent = () => 
                iframeEl.contentWindow.document.body.textContent;

    iframeEl.onload = () => 
                console.log('Iframe tag injection: ' + getContent());

    document.body.appendChild(iframeEl);
```

## 脚本标签注入

下一步是添加一个额外的脚本标记。标签用于从外部文件运行 JavaScript 代码。因此，在这些脚本文件中，我们可以找到一个普通 JS 代码，其中包含 JSON 对象和 onload 事件后被访问的已知变量。代码如下:

```
 let srcTag = document.createElement("script");
    srcTag.setAttribute("src", " /data.txt");

    srcTag.onload = () => 
                console.log("Script tag injection: " + dataList.sample);

    document.body.appendChild(srcTag);
```

其中 *data.txt* 包含

```
let dataList = {
    "sample": "data from data.txt file"
};
```

## 链接标签注入

CSS 链接标签呢？它能用来接收数据和脚本标签吗？答案是肯定的。可以延迟加载外部 CSS 样式表文件，通过*内容*属性将数据注入网页标签。

```
p.s3fc-hidden::after {
    content: "{cssJson: 'cssJsonValues'}";
}
```

下面是它的加载方式:

```
 let linkTag = document.createElement("link");
    linkTag.setAttribute("rel", " stylesheet");
    linkTag.setAttribute("href", " /style.css");

    let getContent = () => document.styleSheets[document.styleSheets.length - 1]
                            .cssRules[0]
                            .styleMap.get("content");

    linkTag.onload = () => 
                console.log("Link tag injection: " + getContent());

    document.body.appendChild(linkTag);
```

## 对象标签注入

其中一个有趣的想法是使用图像从其内容中接收数据。唯一的问题是格式，因为大多数图像都是以某种二进制形式表示的。例外情况是 SVG 图像文件，它们是以文本格式表示的格式良好的 XML 文件。

```
<svg version="1.1"
     baseProfile="full"
     width="300" height="200"
     >
  <text x="0" y="15" fill="white">{vectorSample: "data"}</text>
</svg>
```

和前面的例子一样，为了加载文件，我们需要创建适当的标记( *<对象>* ，在本例中为 *)* ，并使用其 *onload* 事件:

```
 let objectTag = document.createElement("object");
    objectTag.setAttribute("data", "/vector.svg");
    objectTag.setAttribute("width", "0");
    objectTag.setAttribute("height", "0");

    let getContent = () => 
                objectTag.contentDocument.firstChild.textContent;

    objectTag.onload = () =>  
        console.log("Object tag injection " + getContent());

    document.body.appendChild(objectTag);
```

## 总结

表示四种接收数据的方式不会在浏览器检查器的网络选项卡中显示为 AJAX 服务器调用。这是完成测试任务的一个目标。测试任务的完整工作代码可以从 GIT 资源库下载:

[](https://github.com/sergiiriabokon/simple-jsonp) [## GitHub-sergiiriabokon/simple-jsonp:不使用 XHR 调用发出 AJAX 请求的四种方法

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/sergiiriabokon/simple-jsonp)