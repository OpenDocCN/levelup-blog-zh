# WebAssembly + React 简化版

> 原文：<https://levelup.gitconnected.com/webassembly-react-simplified-cc092521a984>

![](img/17666973d0acca2cd123e9ffdd0e00c6.png)

WebAssembly (WASM)有一个时间和地点，但即使你找到了你需要使用它的确切的正确时刻，也很难从周围的噪音中提取如何使用它的信息。

我们将着眼于获取一些开源 C++并在自定义钩子中从 React 应用程序调用它。

# 什么是 WebAssembly？

我见过的最好的解释来自 https://developer.mozilla.org/en-US/docs/WebAssembly:

“WebAssembly 是一种可以在现代 web 浏览器中运行的新型代码，它是一种低级的类似汇编的语言，具有紧凑的二进制格式，以接近本机的性能运行，并为 C/C++、C#和 Rust 等语言提供编译目标，以便它们可以在 web 上运行。它还被设计成与 JavaScript 并行运行，允许两者协同工作。”

## 我们不是一直都能做到吗？

是也不是。我们已经能够在本地运行所有这些语言，但不能在浏览器中运行。传统上，如果我们需要使用 C++之类的东西来运行在浏览器中的任何类型的代码，它通常是一个发送到服务器上运行的请求。

## 为什么我们需要在浏览器中运行它？

有很多时候你没有；这不是标准后端代码的替代品。但是如果你有一些不需要安全但是需要高性能的东西，那么能够在本地运行 C/C++之类的东西是一个巨大的优势。服务器上的处理能力是要花钱的，在用户的浏览器上运行可以潜在地为你省钱，也为用户节省一些时间。对于我们的例子，我们有一个需要运行数百万次模拟的计算引擎，如果你在服务器上完成所有的工作，这可能会很昂贵，如果你用 Javascript 编写它，它会更慢。所以两全其美，浏览器中的 C++。

Wasm 正被用于许多事情，包括图形，对于我们的例子，我们只是将它作为一个本地服务。

# 所以你决定要在你的 React 应用中运行 C/C++？

如果(像我一样)你是一名 Javascript 开发人员，你已经找到了一些 C/C++开源代码，现在想在你的浏览器中重用它，我有一点坏消息要告诉你。可能要写点 C++。先别走，还没那么糟。你发现的大多数 C++程序都有一个 main，并期望在命令行上运行。这并不理想，我们需要把它变成更容易调用我们的自定义钩子的东西。

## 第一步

如果还没有，在 C++项目中为你的 API 层写一个新的类。这主要是重新创建现有项目的“main”方法中的内容。谷歌将成为你的朋友。C++已经存在了几个世纪，所以有很多帮助。尽可能简单地使用这个 API 的请求和响应参数。我将 json 作为一个字符串传入，并使用 [jsoncpp](https://github.com/open-source-parsers/jsoncpp) 读取它，但是还有许多其他选项可供选择。

## 第二步

我们将使用 [emscripten](https://emscripten.org/docs/introducing_emscripten/index.html) 来移植代码，所以我们需要一个`[EMSCRIPTEN_BINDINGS](https://emscripten.org/docs/porting/connecting_cpp_and_javascript/embind.html)()` 块来公开我们的方法。我的看起来像这样:

## 第三步

安装 emscripten——这里的说明很容易理解。

## 第四步

使用 emscripten 移植您的 C++。这里需要注意的重要一点是，如果有头文件(。h 扩展名)，那么您需要在列表中指定相应的 cpp 类。我的看起来像这样:

```
em++ --bind emBinding.cpp fileOne.cpp fileTwo.cpp file3.cpp jsoncpp.cpp -s WASM=1 -o ../wasm/poker.js -s 'ENVIRONMENT="web"' -s USE_ES6_IMPORT_META=0 -s EXPORT_ES6=1 -s MODULARIZE=1 
```

如果这是成功的，它应该生成 2 个文件。A *。js 和*。wasm 使用您提供的名称。现在我们可以忘记 C/C++了，回到可爱的 Javascript 世界。

## 第五步

更新 webpack.config 以加载到。wasm 文件是这样的:

## 第六步

创建您的自定义挂钩:

让我们仔细看看！首先，我们加载 wasm 文件:

我对此有一个尝试，因为有些人在他们的浏览器中禁用了 webassembly(为了测试这一点，你可以进入 firefox 并在地址栏中输入 about:config，然后将 javascript.options.wasm 更改为 false)。如果没有这个，你的用户可能只会收到一个没有任何提示的空白页面。

对于一个定制钩子来说，剩下的就很正常了，我们现在只是从 wasm 中调用我们的 exposed 方法。

我们能谈谈房间里的大象吗？为什么我的承诺超时是 0 毫秒？当我第一次写这篇文章时，我让我的计算运行了 200 万次模拟——这几乎立刻就有了回报。(我知道，对！wasm 能有多快就是这么快。)我想给用户提供运行更多的能力，只是 1000 万，这给了一个明显的停顿，我希望用户有一些迹象表明这里正在发生一些事情。所以，正如你所做的，我增加了一个装载旋转器。奇怪的事情发生了，直到 wasm 被调用后，它才显示出 spinner，它闪烁着消失了。在进行了一些 h̶a̶c̶k̶i̶n̶g̶调查并试图理解[事件循环](https://youtu.be/cCOL7MC4Pl0)之后，我发现这个超时冲走了承诺，显示了微调器，然后开始使用 Wasm。

## 第 100 步(好吧，我记不清了)

现在就像使用其他钩子一样使用你的新钩子。关于 Wasm 的例子，你可以点击这里查看我的扑克应用[。](https://rockhopper.netlify.app?handid=293496318366581254)