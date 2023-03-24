# 如何用 Node.js 下载文件

> 原文：<https://levelup.gitconnected.com/how-to-download-a-file-with-node-js-e2b88fe55409>

![](img/7f4e2357a57b803c4c243753fb90b7cb.png)

由[萨凡纳·维克菲尔德](https://unsplash.com/@sw_creates?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/download-file?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

上周末，我对我的时间追踪应用程序“Tie Tracker”([PWA](https://tietracker.com)/[GitHub](https://github.com/peterpeterparker/tietracker))进行了依赖更新。

在这个特殊的工具中，我将繁重的工作委托给了 Web Workers，这样 UI 就不会发现自己处于阻塞状态。

因为这款应用可以离线使用，并且可以在[应用商店](https://apps.apple.com/us/app/tie-tracker/id1493399075)和 [Google Play](https://play.google.com/store/apps/details?id=com.tietracker.app) 中找到，所以我没有通过 CDN 导入所需的工人依赖项，而是在本地导入。

```
importScripts('./a-third-party-library.min.js');
```

该应用程序本身是用 [React](https://reactjs.org/) 开发的，但是我用普通的 JavaScript 实现了工人，并且没有包管理器来处理他们的依赖关系。

因此，我必须用一个 [Node.js](https://nodejs.org/en/) 脚本来更新库😇。

# 节点获取

Node.js 中没有类似的 API，但是有一个轻量级模块带来了这样的功能。这就是为什么我使用[节点获取](https://github.com/node-fetch/node-fetch)来实现文件的下载。

```
npm i node-fetch --save-dev
```

# 脚本

我开发的用于更新依赖项的脚本如下:

```
const {createWriteStream} = require('fs');
const {pipeline} = require('stream');
const {promisify} = require('util');
const fetch = require('node-fetch');

const download = async ({url, path}) => {
  const streamPipeline = promisify(pipeline);

  const response = await fetch(url);

  if (!response.ok) {
    throw new Error(`unexpected response ${response.statusText}`);
  }

  await streamPipeline(response.body, createWriteStream(path));
};

(async () => {
  try {
    await download({
      url: 'https://unpkg.com/...@latest/....min.js',
      path: './public/workers/libs/....min.js',
    });
  } catch (err) {
    console.error(err);
  }
})();
```

上面的`download`函数使用流管道下载文件，如 node-fetch README 中所示，并使用内置的`fs`模块将输出写入文件系统。

从 Node.js v14.8.0 开始可以使用顶级 Await，但是我使用了 immediate 函数，因为我将它集成到了一个链中，而在这个链中它还不可用。

就这样，🥳

# 继续阅读

如果你想了解更多关于 React 和 Web Workers 的内容，我去年连续发表了三篇关于它的博文😉。

*   [反应过来的还有 Web 工作人员](/react-and-web-workers-c9b60b4b6ae8)
*   [React，Web Workers，和 IndexedDB](/react-web-workers-and-indexeddb-a973797e771b)
*   [React，Web Workers，IndexedDB 和 ExcelJS](/react-web-workers-indexeddb-and-exceljs-2439ff1341ff)

到无限和更远的地方！

大卫

你可以通过[推特](https://twitter.com/daviddalbusco)或者我的[网站](https://daviddalbusco.com/)联系我。

试着为你的下一个演示做准备🤟。

[![](img/4a4e6a18dc5c0a659736147c9321ab22.png)](https://deckdeckgo.com)

DeckDeckGo——用于演示的开源 web 编辑器