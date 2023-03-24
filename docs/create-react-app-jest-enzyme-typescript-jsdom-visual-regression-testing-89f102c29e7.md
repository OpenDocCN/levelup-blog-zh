# 使用 Create React App、TypeScript 和 Jest 进行可视化回归测试

> 原文：<https://levelup.gitconnected.com/create-react-app-jest-enzyme-typescript-jsdom-visual-regression-testing-89f102c29e7>

![](img/6ebfe87a3b3029ec4ba3e56d35b27ba9.png)

我广泛地使用 Typescript 做任何事情，包括 React。当我想学习使用上面给出的堆栈进行测试时，令人惊讶的是我找不到任何可以从中受益的资源或博客帖子。在做了大量的研究和代码阅读后，我从其他开发人员的努力中创建了一个混合解决方案，也许它只是将他们的代码翻译成打字稿和修剪一下，但要点是理解过程的基础。在这篇文章的开头，我们将使用 Jest framework 测试一个组件，从设置它开始。

在做任何事情之前，我们需要安装所需的软件包作为开发者依赖:

```
npm install connect enzyme enzyme-adapter-react-16 enzyme-to-json finalhandler jest-image-snapshot puppeteer -D
```

以及它们的类型定义:

```
npm install [@types/enzyme](http://twitter.com/types/enzyme) [@types/enzyme-adapter-react-16](http://twitter.com/types/enzyme-adapter-react-16) [@types/jest-image-snapshot](http://twitter.com/types/jest-image-snapshot) [@types/puppeteer](http://twitter.com/types/puppeteer) @types/connect @types/finalhandler -D
```

现在该配置了。首先，我们将在根目录下创建我们的 Jest 配置文件，它通常在`src`目录的上一级:

```
module.exports *=* {
"roots": [
  "<rootDir>/src"
],
"transform": {
  "^.+\\.tsx?$": "ts-jest"
},
"testRegex": "(/__tests__/.*|(\\.|/)(test|spec))\\.tsx?$",
"moduleFileExtensions": [
  "ts",
  "tsx",
  "js",
  "jsx",
  "json",
  "node"
],
"snapshotSerializers": ["enzyme-to-json/serializer"],
}
```

这个配置将你的根目录设置为`src`目录，用`ts-jest`模块运行`.tsx`文件，并在`src`目录的任何子目录下寻找`.ts, .tsx, .js, .jsx, .json`文件。

我们还使用`enzyme-to-json`序列化器作为 JSON 来制作组件的快照，而不是使用酶的默认方式。

现在我们必须在`src`目录下创建一个名为`setupTests.js`的文件。这个文件名很特别，因为由 create-react-app 创建的应用程序会查找这个文件名。这是一个在所有测试文件中全局导入的文件。因此，我们在这里的每个测试中反复添加我们需要导入的内容，以节省时间。下面是`setupTests.js`:

```
*import* { *configure* } *from* 'enzyme';
*import* *Adapter* *from* 'enzyme-adapter-react-16';
*import* { *toMatchImageSnapshot* } *from* "jest-image-snapshot";*expect*.*extend*({ *toMatchImageSnapshot* });*configure*({ adapter: *new* *Adapter*() });
```

现在我们准备好测试了。挑选你最喜欢的组件，尝试浅层渲染:

```
import React from "react";
import { shallow } from "enzyme";
import FavComponent from "./FavComponent";test(`should render`, () => {
  const wrapper = shallow(<FavComponent />);
  expect(wrapper).toMatchSnapshot();
});
```

将这个测试文件保存到组件所在的目录中。命名为`FavComponent.test.tsx`这些扩展对 Jest 找到这些测试文件很重要。

接下来，我们可以继续我们的视觉回归测试，因为我们已经看到了基础知识。为了测试我们组件的截图，我们将编写自己的实用程序，我将向您解释它，以便您可以创建自己的解决方案。

首先，让我们看看我们在测试文件中要求什么:

```
*import* *React* *from* "react";
*import* *ReactDOM* *from* "react-dom";
*import* FavComponent *from* "./FavComponent";
*import* *getComponentImage* *from* "../utils/getComponentImage";test(`should match screenshot`, async () => {
  const div = document.createElement("div");
  document.body.appendChild(div);
  ReactDOM.render(<FavComponent />, div);

  const screenshot = await getComponentImage();
  expect(screenshot).toMatchImageSnapshot();

  ReactDOM.unmountComponentAtNode();
  document.body.removeChild(div);
});
```

首先，我们导入`React`以获得 JSX 的支持。然后`ReactDOM`在一个元素中安装组件，并在一个容器元素中呈现它，这个容器元素是我们创建的`div`常量。我们可以使用`document.createElement("div")` like 语法，因为 JSDOM 内置了 create-react-app。我们向我们的实用程序请求截图。一会儿你会看到它的代码，但是你会注意到我们没有传递任何参数给`getComponentImage()`。这是因为它从全局 JSDOM 对象获取组件的 HTML 内容。完成所有工作后，我们卸载组件并移除容器元素，以防止与其他测试冲突。

现在……什么是`getComponentImage`?基本上，它是一个创建 HTTP 服务器的实用程序，该服务器用我们组件的 HTML 进行响应，并通过`puppeteer`连接到该 HTTP 服务器以获取其屏幕截图。然后它返回这个截图。我们开始写吧。

首先，我们需要一个服务器来服务组件的 HTML:

```
*import* *connect* *from* "connect";
*import* *finalhandler* *from* "finalhandler";
*import* *http* *from* "http";
*import* *puppeteer* *from* "puppeteer";
*import* { *AddressInfo* } *from* "net";*const* *createServer* *=* *async* (*html:* string) *=>* {*const* *app* *=* *connect*();*app*.*use*((*request:* any, *response:* any, *next:* *connect*.*NextFunction*) *=>
  request*.*url* *===* "/" *?* *response*.*end*(*html*) *:* *next*()
);*app*.*use*(*finalhandler*);*const* *server* *=* *http*.*createServer*(*app*);*await* *new* *Promise*((*resolve*, *reject*) *=>* {
  *const* *startServer* *=* () *=>* {
    *server*.*once*("error", (*e:* *NodeJS*.*ErrnoException*) *=>* {
      *if* (*e*.*code* *===* "EADDRINUSE") {
        *server*.*close*(*startServer*);
      }
      });
      *server*.*listen*(0, (*err?*) *=>* (*err* *?* *reject*(*err*) *:* *resolve*()));
    };
    *startServer*();
  });
  *return* *server*;
};
```

我们从导入必要的模块开始。让我们了解一下模块:

`connect` : Connect 是一个支持中间件的 HTTP 服务器模块。你一眼就能看出 express.js 用法的相似之处。

`http`:这是一个原生模块。它创建了一个 HTTP 服务器，您可以在特定的端口和 IP 上监听它。

`puppeteer`:木偶师提供高级 API 控制铬或铬。它通常用于无头模式，但也可以用于无头模式。

`finalhandler`:它是最后一步中调用的最后一个函数，用来响应 HTTP 请求。

我们创建了我们的`connect`对象，并在中间件的支持下拥有了一个框架。然后我们安装一个中间件，如果它在根资源路径“/”上，它用我们的组件的 HTML 进行响应，如果没有，通过让请求`next()`到`finalhandler`用 404 进行响应。然后，我们创建一个 HTTP 服务器，并在等待它完成的承诺中监听它的事件。

出错时，它抛出`EADDRINUSE`——意味着该端口被另一个服务或程序使用。如果发生这种情况，我们的实用程序将停止，不会继续进行。为了克服这一点，我们每次遇到错误时都要重新启动服务器。服务器通过将端口参数设置为 0 来监听一个随机端口，但是它仍然可能与另一个程序冲突，我们需要监听错误。一切结束后，我们归还服务器。

我们提供了组件的 HTML，现在我们需要“看到”它。

```
*const* *takeScreenshot* *=* *async* (*url:* string) *=>* {
  *const* *browser* *=* *await* *puppeteer* .*launch*(
      {
        args: ["--disable-lcd-text"],
        defaultViewport: { width: 1920, height: 1080 },
      },
    );

  *const* *page* *=* *await* *browser*.*newPage*();

  *await* *page*.*goto*(*url*, { waitUntil: "load" }); *const* *image* *=* *await* *page*.*screenshot*();
  *browser*.*close*();

  *return* *image*;
};
```

我们从导入的木偶师对象调用 launch 来创建 chromium 实例。我们推出了两个重要的选项属性。首先是`args: ["--disable-lcd-text"]`。这是为了禁用反走样，使铬在每一个不同的显示器给出不同的截图。此外，我们需要组件的尺寸一致，可能会有不同分辨率的媒体查询，所以我们设置了一个固定的视口尺寸。这些安排将确保我们在任何地方和每次测试时都得到相同的结果。

我们在浏览器中打开一个新页面，并将其重定向到我们的服务器，稍后它将作为参数传递给`url`。我们制作木偶师来截取页面的截图。

为了防止内存泄漏，我们关闭浏览器，然后返回图像供以后使用。

除非我们调用这些函数，否则它们不会启动，所以最后一个函数就是这样做的:

```
const generateImage = async () => {
  const html = document.documentElement.outerHTML;  

  const server = await createServer(html);
  const address = server.address()! as AddressInfo;
  const port = address.port;

  const url = `http://localhost:${port}`; const screenshot = await takeScreenshot(url);
  await new Promise((resolve) => server.close(resolve));

  return screenshot
};export default generateImage;
```

我们从 JSDOM 的全局对象中获取 HTML，用给定的 HTML 创建我们的服务器，将它响应回`takeScreenshot()`的木偶浏览器实例。为了重定向并从这个函数中获取图像，我们传递一个指向 localhost 的 URL 参数。"!"在地址变量中意味着`server.address()`不为空。我们称它为`AddressInfo`,因为它也可以是一个字符串。我们通过将 resolve 传递给它的事件处理程序来等待服务器关闭。在函数的结尾，我们返回截图，在文件的结尾，我们导出这个函数用于测试文件。

你知道我们如何在我们最喜欢的组件的测试文件中使用它。现在你有了自己解决问题的想法。

你可以阅读[这个回购](https://github.com/dferber90/jsdom-screenshot/blob/master/generateImage.js)来获得我们`getComponentImage`实用程序的更高级和原始版本。它包含更多的功能，如服务静态文件，并让您能够使用不同的选项，感谢它的开发者帮助我们。

希望这篇文章对你有帮助或启发，谢谢阅读。

[](https://gitconnected.com/learn/react) [## 学习 React -最佳 React 教程(2019) | gitconnected

### 前 50 名 React 教程-免费学习 React。课程由开发人员提交并投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/react)