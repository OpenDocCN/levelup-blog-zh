# JavaScript 技巧——笑话模仿、反应背景等等

> 原文：<https://levelup.gitconnected.com/javascript-tips-jest-mocks-react-background-and-more-bfd5590e80f7>

![](img/91c2fb499c583f144d9381c706ca47a4.png)

照片由[海伦娜·赫兹](https://unsplash.com/@imperiumnordique?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何类型的应用程序一样，当我们编写 JavaScript 应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些常见 JavaScript 问题的解决方案。

# Regex 来检查字符串是否只包含数字

我们可以使用正则表达式来检查一个字符串是否只有数字。

例如，我们可以写:

```
const reg = /^\d+$/;
```

`^`从开始匹配字符串，`\d`是数字。

# Javascript 文件名命名约定

JavaScript 文件名应该遵循一些常见的约定。

我们应该使用全部小写的文件名。

此外，我们不想在文件名中使用空格。

我们可以选择使用连字符作为单词分隔符。

如果有的话，我们还可以在文件名中添加一个版本号。

通过更新文件名中的版本号，我们总是在旧版本旁边提供带有 CDN 的最新版本。

# 从 iframe 调用父窗口函数

我们可以用`window.parent`属性访问父窗口。

因此，如果我们想从 iframe 调用父窗口中的函数，我们可以写:

```
<a onclick="parent.foo();" href="#" >click me</a>
```

`foo`是父窗口中的顶层函数。

# 将弹出窗口置于屏幕中央

为了使弹出窗口在屏幕上居中，我们可以做一些计算来使窗口居中，并将其传递给规范字符串。

例如，我们可以写:

```
const openPopup = (url, title, w, h) => {
  const left = (screen.width / 2) - (w / 2);
  const top = (screen.height / 2) - (h / 2);
  return window.open(url, title, `width=${w}, height=${h}, top=${top}, left=${top}`);
}
```

我们通过用宽度除以 2 减去宽度除以 2 得到左上角的 x 坐标来得到左坐标。

同样，我们做同样的事情，但用高度替换宽度，以获得左上角的 y 坐标。

然后，我们将它们传递给规范字符串，并对宽度和高度进行同样的处理。

# 使用 React 内联样式设置背景图像

要用 React 内联样式设置背景图像，我们可以将图像作为模块导入。

然后我们可以把它插入到 CSS 字符串中来设置背景图片。

例如，我们可以写:

```
import Background from '../images/background.png';const styles = {
  backgroundImage: `url(${Background})`
}
```

我们用带有我们导入的`Background`图像的`url`字符串来设置`backgroundImage`属性。

# 删除给定字符后的所有内容

要删除字符串中给定字符之后的部分，我们可以使用`split`方法。

例如，我们可以写:

```
url.split('?')[0]
```

然后我们在`?`之前得到`url`字符串的一部分。

# JavaScript 中闭包的实际用途

闭包非常适合隐藏私有的东西，比如我们不想暴露给外界的变量和函数。

例如，我们可以写:

```
const obj = (() => {
  const private = () => {
    console.log('hello');
  } return {
    public() {
      private();
    }
  }
})();
```

`private`是私有函数。

而`public`是`obj`中的一个函数，所以它可以在外部编码。

# 获取图像的实际宽度和高度

要获得图像的实际宽度和高度，我们可以使用`naturalHeight`属性获得实际高度，使用`naturalWidth`获得实际宽度。

例如，我们可以写:

```
const height = document.querySelector('img').naturalHeight;
```

并且:

```
const width = document.querySelector('img').naturalWidth;
```

这个从 HTML5 开始就有了。

# 使用 Jest 模拟 ES6 模块导入

要使用 Jest 模拟 ES6 模块导入，我们可以使用`jest.fn()`函数来模拟函数。

例如，我们可以写:

`logger.js`

```
export const log = (y) => console.log(y)
```

`module.js`

```
import { log } from './logger';export default (x) => {
  doSomething(x * 2);
}
```

`module.test.js`

```
import module from '../module';
import * as logger from '../logger';describe('module test', () => {
  it('logs the double the input', () => {
    dependency.log = jest.fn(); 
    module(2);
    expect(dependency.log).toBeCalledWith(4);
  });
});
```

我们所要做的就是调用`jest.fn`来创建一个模拟函数。

我们导入了整个模块，所以成员不是只读的。

因此，我们可以给它分配我们自己的模拟函数。

然后我们调用我们的`module`函数来测试我们的函数并检查我们的结果。

![](img/ada2df47dead6241e4275d85142c3460.png)

[TS 谢尔盖](https://unsplash.com/@ttsergey?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

我们应该遵循一些常见的文件命名惯例来命名 JavaScript 文件。

同样，我们可以用`jest.fn()`方法模仿函数。

弹出窗口可以位于屏幕中央。

我们可以通过导入背景图像并将其插入 React 组件中的 CSS 字符串来设置背景图像。