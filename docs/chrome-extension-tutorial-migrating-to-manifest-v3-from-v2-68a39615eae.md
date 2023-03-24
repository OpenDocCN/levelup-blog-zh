# Chrome 扩展教程:从 V2 迁移到清单 V3

> 原文：<https://levelup.gitconnected.com/chrome-extension-tutorial-migrating-to-manifest-v3-from-v2-68a39615eae>

![](img/5e8e49a362e64055899e78720e2c6868.png)

*本文原载于* [*我的个人博客*](https://blog.shahednasser.com/chrome-extension-tutorial-migrating-to-manifest-v3-from-v2/) *。*

2020 年 11 月，Chrome 推出了 Manifest V3。长期以来，扩展一直使用清单 V2，所以这是一个很大的转变，尤其是 V3 中的新特性。

在本教程中，我们将看到从清单 V2 到 V3 所需的步骤。我将使用以前教程的扩展( [Chrome 扩展教程——用皮卡丘](https://blog.shahednasser.com/chrome-extension-tutorial-replace-images-in-any-website-with-pikachu/)替换任何网站中的图像)和一个新的分支。如果你不熟悉它，我们建立了一个 chrome 扩展，用我们通过 API 检索的随机皮卡丘图像替换网站中的所有图像。你可以在这里查看存储库[。](https://github.com/shahednasser/pikachu-everywhere/tree/manifest-v3)

# 为什么要迁移到清单 V3？

正如 Chrome 的文档所说:

> *使用 MV3 的扩展将享受安全性、隐私性和性能的增强；他们也可以使用 MV3 采用的更现代的开放网络技术，比如服务人员和承诺。*

# 对 manifest.json 的更改

## 更改版本

第一个明显的步骤是，您需要更改清单的版本。在 manifest.json 文件中，将其更改如下:

```
{ 
    ..., 
    "manifest_version": 3, 
    ... 
}
```

如果您现在尝试将您的扩展添加到 chrome 中(或者如果它已经存在，则重新加载它)，您将看到不同的错误，这些错误与您仍然需要对 manifest.json 文件进行的更改有关。

## 主机权限

在 Manifest V2 中，有两种方法可以从扩展中获得 API 或任何主机的权限:要么在`permissions`数组中，要么在`optional_permissions`数组中。

在清单 V3 中，所有主机权限现在都在一个新数组中用键`host_permissions`分开。不应再将主机权限与其他权限相加。

回到我们的例子，这是我们的`permissions`阵列:

```
{ 
   ..., 
   "permissions":
     [ 
       "https://some-random-api.ml/*"
     ],
   ... 
}
```

现在，它应该变成这样:

```
{ 
   ..., 
   "host_permissions": [ 
      "https://some-random-api.ml/*" 
    ],
   ...
}
```

在我们的例子中，我们只需要将密钥从`permissions`改为`host_permissions`。但是，如果您的扩展在`permissions`中有其他值，那么您应该将它们保留在其中，并将您的主机权限转移到`host_permissions`。

## 后台脚本

Manifest V3 用服务工作者替换了后台脚本。我们稍后将讨论如何进行转换，但是首先需要在 manifest.json 中进行转换。

在我们的扩展中，`background`对象目前看起来像这样:

```
{ 
   ..., 
   "background": {
     "scripts": ["assets/js/background.js"], 
     "persistent": false
   },
   ...
}
```

我们需要做的是将`scripts`数组键改为`service_worker`，现在你应该有一个服务工作者，而不是多个后台页面或脚本。所以，它应该是这样的:

```
{ 
  ..., 
  "background": {
    "service_worker": "assets/js/background.js" 
  },
  ...
}
```

注意，我们不再需要添加`persistent`了。另外，如果`background`里面有`page`，那也应该改成服务人员。

## 行动

以前动作是`browser_action`和`page_action`，现在统一成`action`清单 V3。这是因为随着时间的推移，它们变得越来越相似，没有必要将它们分开。

我们在扩展中不使用它，但这是它应该是什么样子的一个例子:

```
{ 
   ..., 
   "action": {
      //include everything in browser_action 
      //include everything in page_action
   },
   ... 
}
```

还需要对代码进行修改，我们稍后会谈到。

## 内容安全政策

同样，这在我们的扩展中没有使用，但是我们仍然需要检查它。如果您的扩展有一个内容安全策略(CSP)，那么您需要将它从一个字符串(在清单 V2 中的方式)更改为一个对象(在清单 v3 中的方式)。

清单 V3 中的示例应该是这样的:

```
{
    ...,
    "content_security_policy": {
  	"extension_pages": "...",
 	 "sandbox": "..."
     },
    ...
}
```

## 网络可访问的资源

您需要在 manifest.json 中进行的最后一项更改是将`web_accessible_resources`数组更改为一个详细描述所有资源的对象。下面是 V3 中的一个示例:

```
{
    ...,
    "web_accessible_resources": {
    	"resources": [
           //the array of resources you had before
        ]
    },
    ...
}
```

该对象还将在未来版本中支持键`matches`(URL 数组)`extension_ids`(键数组)和`use_dynamic_url`(布尔)。

## 添加扩展

现在，如果你在浏览器中打开 chrome://extensions 并添加或重新加载你的扩展，它将成功地更改为清单 V3 扩展。然而，在我们的例子中，它会在扩展框中向您显示一个错误按钮，当您单击它时，它会显示“服务人员注册失败”这是因为我们的代码中还有更多工作要做。

# 从后台脚本到服务人员

一、什么是服务工作者，和后台脚本有什么区别？

后台脚本在几乎所有的扩展中都是必不可少的。它们允许你做一些动作或者执行代码，而不需要用户打开某个页面或者做一些事情。这可以用来发送通知，管理与内容脚本的通信，等等。后台脚本通常总是在后台运行。

服务人员在需要时被处决。与后台脚本不同，它们并不总是在后台运行。在顶层，服务工作者应该注册一些事件的侦听器，以便允许它们在以后被执行。

从后台脚本到服务人员的转换取决于您在扩展中的代码。一些扩展可能需要大量的返工，而另一些则不需要这么多。

您需要做的第一步是将以前是后台脚本或页面的文件移动到扩展的根目录。这实际上是为什么在我们的扩展中，我们收到了服务工作者注册失败的错误声明。我们的后台脚本的路径是相对于我们的扩展的根的`js/assets/background.js`。

如果您的情况类似，将您的后台脚本移动到您的扩展的根目录，然后在您的清单中更改`service_worker`的值以反映更改:

```
{
    ...,
    "background": {
    	"service_worker": "background.js"
    },
    ...
}
```

如果重新加载扩展，服务人员应该可以成功注册。

现在，让我们看看代码。在我们的扩展中，我们的后台脚本如下所示:

```
chrome.runtime.onMessage.addListener(function(message, sender, senderResponse){
  if(message.msg === "image"){
    fetch('https://some-random-api.ml/img/pikachu')
          .then(response => response.text())
          .then(data => {
            let dataObj = JSON.parse(data);
            senderResponse({data: dataObj, index: message.index});
          })
          .catch(error => console.log("error", error))
      return true;  // Will respond asynchronously.
  }
});
```

基本上，我们的后台脚本使用`chrome.runtime.onMessage.addListener`监听一条消息，如果这条消息是请求图像，它将向 API 发送请求，然后将数据返回给我们的内容脚本。

我们的后台脚本实际上不需要任何额外的改变。背后的原因是，现在作为服务工作者的后台脚本只是注册一个事件监听器，并在事件发生时执行代码，这正是服务工作者应该做的。

然而，并不是所有的扩展都是这样的，因为有不同的用例。以下是您需要在后台脚本中检查和修改的内容:

## 全局变量

如上所述，以前后台脚本总是在后台运行。意思是如果我有下面的代码:

```
let count = 0;

chrome.runtime.onMessage.addListener( (message) => {
	count++;
    console.log(count);
});
```

每当后台脚本收到一条消息，计数就会增加。所以，一开始，它会是 0，然后是 1，然后是 2，等等。

在服务行业，这种方法不再适用。服务人员将只在需要时运行，并在完成工作后终止。因此，上面的代码将总是打印在控制台“1”中。

改变这一点取决于您的用例。在上面的例子中，计数可以在后台脚本和内容脚本之间来回传递，以获得所需的结果。更好的方法是使用 Chrome 的存储 API。

使用它，代码看起来会像这样:

```
chrome.runtime.onMessage.addListener ( (message) => {
	chrome.storage.local.get(["count"], (result) => {
        const count = result.count ? result.count++ : 1;
    	chrome.storage.local.set({count});
        console.log(count);
    });
});
```

同样，这取决于您的代码，所以请确保根据最适合您的方式进行更改。

## 计时器和闹钟

计时器在后台脚本中使用没有问题，因为它们总是在后台运行。然而，这对服务人员不起作用。你应该用[警报 API](https://developer.chrome.com/docs/extensions/reference/alarms/) 替换所有计时器。

## 访问 DOM

服务人员无权访问 windows 或 DOM。如果你的扩展需要，你可以使用像 [jsdom](https://github.com/jsdom/jsdom) 这样的库或者使用 [chrome.windows.create](https://developer.chrome.com/docs/extensions/reference/windows#method-create) 和 [chrome.tabs.create](https://developer.chrome.com/docs/extensions/reference/tabs#method-create) 。这要看你的使用情况，什么适合你的需求。

如果您的后台脚本记录音频或视频，这也是需要的，因为这在服务人员中是不可能的。

## 创建画布

如果您的后台脚本先前创建了 canvas，您仍然可以使用 [OffscreenCanvas](https://html.spec.whatwg.org/multipage/canvas.html#the-offscreencanvas-interface) API 来创建。你要做的就是把`document`换成`OffscreenCanvas`。

例如，如果这是您的代码:

```
let canvas = document.createElement('canvas');
```

那么你应该把它改成:

```
let canvas = new OffscreenCanvas(width, height);
```

## 检查你的分机

在您完成了需要将您的后台脚本更改为服务人员的更改之后，请在浏览器中重新加载您的扩展以查看它是否正常工作。

在我们的例子中，`background.js`除了移动到根之外不需要任何改变。所以，如果你重新加载扩展并进入一个页面，你会发现图片已经被皮卡丘图片成功替换。

![](img/c35e443434125e3a63b2d8222423e598.png)

# 动作 API

如前所述，`browser_action`和`page_action`现在合并为`action`。同样的道理也应该应用在你的代码中。如果您使用如下所示的`browserAction`或`pageAction`:

```
chrome.browserAction.onClicked.addListener(tab => { ... }); chrome.pageAction.onClicked.addListener(tab => { ... });
```

它应该更改为使用新的操作 API，如下所示:

```
chrome.action.onClicked.addListener(tab => { ... });
```

因此，确保用`action`替换所有的`browserAction`和`pageAction`用法。

# 执行脚本

如果您的代码使用 executeScript 的`code`属性执行任意字符串，您有两种方法可以改变它。另外，不要用`chrome.tabs.executeScript`，你需要用`scripting`代替`tabs`，这样它就是`chrome.scripting.executeScript`。

## 将代码移动到新文件中

您需要将`code`的值移动到一个新文件中，并使用 executeScript 的`file`属性。

例如，如果您的代码如下所示:

```
chrome.tabs.executeScript({
    code: alert("Hello, World!")
});
```

你应该将`code`的值，这里是`alert("Hello, World!")`移动到一个新文件中(姑且称之为`hello-world.js`):

```
alert("Hello, World!");
```

然后将前面的代码更改为以下内容:

```
chrome.scripting.executeScript({
    file: 'hello-world.js'
});
```

## 将代码放在一个函数中

如果您的代码可以放在一个函数中，就像示例代码一样，那么只需将它移动到同一个文件中的一个函数中，然后将`executeScripts`的`function`属性赋给您创建的函数:

```
function greeting() {
    alert("Hello, World!");
}

chrome.scripting.executeScript({
    function: greeting
});
```

# 附加工作

这里有一个列表，列出了您需要在代码中查找的其他更改和内容:

1.  如果您的扩展使用 webRequest API，这通常用于强制安装扩展的企业设置中，您需要用[declarativeNetRequest](https://developer.chrome.com/docs/extensions/reference/declarativeNetRequest/)API 替换它。
2.  如果您在内容脚本中提出任何 CORS 请求，请确保将它们转移到您的服务人员。
3.  不再允许远程承载的代码。您需要找到另一种方法来执行远程托管的代码。Chrome 的文档建议要么使用**配置驱动的特性和逻辑**，这意味着您检索一个包含所需配置的 JSON 文件，并在本地缓存它以备后用；要么使用一个远程服务**将逻辑外部化**，这意味着您必须将您的应用程序逻辑从您的扩展移动到远程 web 服务。
4.  查看 [API 参考](https://developer.chrome.com/docs/extensions/reference/)以了解您可能正在使用的任何不推荐使用的 API 或方法。

*原载于 2021 年 2 月 11 日 https://blog.shahednasser.com**的* [*。*](https://blog.shahednasser.com/chrome-extension-tutorial-migrating-to-manifest-v3-from-v2/)