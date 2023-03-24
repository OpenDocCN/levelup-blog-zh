# 在 Webpack 前端使用环境变量的简单指南

> 原文：<https://levelup.gitconnected.com/a-dead-simple-guide-to-using-environment-variables-in-your-front-end-with-webpack-5765ce7a6fd8>

![](img/d626c164de74a0c75e9a7f83f3cb6d96.png)

生产和开发环境通常需要各自的环境变量。图片来源:[Pexels.com 奥利雅·达尼莱维奇](https://www.pexels.com/@olia-danilevich/)

**如果你在 React 应用中使用 API，你很有可能需要使用环境变量来隐藏敏感信息和区分开发模式。**

我不会深入环境变量的细节，因为已经有很多关于使用它们的文章了，但是如果你是使用 Create React App 的 ***而不是*** ，我将概述一个在你的前端使用它们的**两步过程**。

我之所以说如果你*没有*使用 Create React App，是因为如果你使用了，Create React App [有一个使用它们的内置方法](https://create-react-app.dev/docs/adding-custom-environment-variables/#referencing-environment-variables-in-the-html)。您可以简单地在根文件夹中创建一个. env 文件，然后像这样输入您的密钥:

```
REACT_APP_MY_SECRET_API_KEY="key123"
```

如果您将代码引用为:

```
process.env.CREATE_REACT_APP_MY_SECRET_API_KEY
```

> 但是如果我没有使用 Create React App 呢？

如果您正在阅读这篇文章，您可能正在进行一个项目，需要在前端使用环境变量，但不知道应该如何做。

## 使用节点包 [dotenv-webpack](https://www.npmjs.com/package/dotenv-webpack) 我们可以访问我们的。env 文件很容易从前端。

## 第一步

```
npm install dotenv-webpack --save-dev
```

## 第二步

在 webpack.config.json 文件中:

## 确保“系统变量”设置为真

*更多详情请见下文。*

仅此而已。

无需任何额外的代码，您的。env 文件在前端可以像在后端一样被访问:

```
process.env.MY_SECRET_API_KEY
```

确保在修改您的。环境文件。

也记得添加你的。环境文件到。gitignore 和在生产环境中配置环境变量。

## 示例 1

```
console.log(process.env.MY_SECRET_API_KEY) // 'key123'
```

## 示例 2

```
const myReactComponent = () => {const key = process.env.MY_SECRET_KEYreturn (
<p>
Hello, my key is {key}.
</p>
)
}
```

# 结论

最近，我使用我参加的编码训练营提供的样板代码启动了一个 React 项目。当需要在 React 组件中使用环境变量时，我很难找到解决方案。

解决方案通常假设读者正在使用 Create React 应用程序。另外，我发现这个问题的其他解决方案过于复杂，或者是针对稍微不同的问题，比如在 webpack.config.json 文件中使用环境变量*，并且不能被前端的其他文件访问。我经常出错:*

```
process.env.MY_SECRET_KEY // error: process is not defined
```

找到 dotenv-webpack 是我一直在寻找的简单解决方案，但我发现它的说明不是非常清楚。例如:

> “system vars:true；//加载所有预定义的“process.env”变量，这些变量将根据 dotenv 规范胜过任何本地变量。
> 
> —[https://www.npmjs.com/package/dotenv-webpack](https://www.npmjs.com/package/dotenv-webpack)

如果这对你有意义，请不要犹豫在评论区阐明它的意思。我个人认为上述措辞令人困惑。我偶然发现 systemvars 需要设置为 true。False 是默认设置。

我希望这篇文章尽可能简单地在您的前端使用环境变量。

请随意联系:

[](https://www.linkedin.com/in/jeffreylwood/) [## 杰弗里·伍德，美国纽约布鲁克林| LinkedIn

### 我于 2019 年来到纽约市，在纽约艺术学院读研究生。在那里的时候，我帮助…

www.linkedin.com](https://www.linkedin.com/in/jeffreylwood/) [](https://github.com/JeffreyLWood) [## 杰弗里·伍德

### 我是杰夫，刚刚从纽约艺术学院(艺术硕士绘画 2021)和富斯塔克学院(网络…

github.com](https://github.com/JeffreyLWood)