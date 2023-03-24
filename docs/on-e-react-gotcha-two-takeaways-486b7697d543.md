# 一个反应抓到你，两个外卖

> 原文：<https://levelup.gitconnected.com/on-e-react-gotcha-two-takeaways-486b7697d543>

## [JavaScript 和打字技巧](https://gentille.us/typescript-tips-b74925485b78?sk=4c9067cf57be6406abc26e44cb7fb872)

您可能知道在 React 中不要以“use”开头函数名，但是在某些情况下，以“on”开头变量也会导致问题。

![](img/2c474a48f4c47440fbcba504b55dfd7e.png)

我们想要一个方便的变量来存储我们在应用程序中的特定视图上的事实。这使得我们在视图中以独特的方式呈现某些元素变得更加容易。我们用了这样一句话:

```
const [onSpecialPage, setOnSpecialPage] = useState()
```

给事物命名是困难的，但是这个似乎是正确的。我们随后在一个参数化样式的 div 中使用了这种状态。这里有一个省略版本:

```
export const StyledSpecial = styled.div<{onSpecialPage:boolean}>`
margin-top: ${p => p.onObjectiveExplorer ? 0 : 30}px;}
`
```

代码按预期运行，但它也意外地(对我来说)在浏览器的控制台中生成了以下错误。

```
Warning: Unknown event handler property `onSpecialPage`. It will be ignored.
```

我们学到了一个艰难的方法，即使事情看起来正常，也不要忽略浏览器控制台错误。尽管最初的词是“警告”，但这是一个控制台错误。React 不太喜欢以“on”开头的参数名，如果它们不用于事件处理程序的话(想想`onClick`)。一旦你知道发生了什么，就很容易解决。问题是没有一个自动林挺帮助我们抓住这一点。

我想在这里提出两点:

1.  避免以“on”开头的名字，在这种情况下，我认为“in”也可以。
2.  记得经常检查你的浏览器控制台，这样当你创建一个新的警告或错误时你就知道了。

还有更多 [JavaScript 和 TypeScript 文章](https://gentille.us/typescript-tips-b74925485b78?sk=4c9067cf57be6406abc26e44cb7fb872)。

平静地编码。