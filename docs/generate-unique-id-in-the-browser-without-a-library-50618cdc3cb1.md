# 在没有库的浏览器中生成唯一 Id

> 原文：<https://levelup.gitconnected.com/generate-unique-id-in-the-browser-without-a-library-50618cdc3cb1>

![](img/edcf2b1b4399ffac6a0621e147547d06.png)

布雷特·乔丹在 Unsplash 上拍摄的照片。

每当我需要在客户端使用一个随机的、唯一的值时，我发现自己都在以前的项目中寻找这个函数。您可能没有听说过 Web Crypto API，它提供了一组处理加密的底层原语。对于一个相当冷门的 API，Web Crypto API 在现代浏览器和 IE 11 中都得到了很好的支持。

```
// random.ts
function randomId(): string {
  const uint32 = window.crypto.getRandomValues(new Uint32Array(1))[0];
  return uint32.toString(16);
}
```

有多个包可以实现类似的目的，尽管这种特定的浏览器本地解决方案比 nanoid、shortid 或 uuid 等包有一些优势。使用原生加密模块不会导致项目中不断增长的依赖项列表。输出也很短，易于阅读。如果您的用户能够看到 id，并且您的支持人员可能会要求您提供 id(例如订单号)，那么最后一点就变得尤为重要。

要测试 Node.js 中的 randomId，您需要模仿 Crypto.getRandomValues。

```
// setup.ts
import crypto from "crypto";

function getRandomValuesMock<
  T extends
    | Int8Array
    | Int16Array
    | Int32Array
    | Uint8Array
    | Uint16Array
    | Uint32Array
    | Uint8ClampedArray
    | Float32Array
    | Float64Array
    | DataView
>(array: T) {
  return crypto.randomFillSync(array);
}

global.crypto = { getRandomValues: getRandomValuesMock };
```

测试随机值可能很棘手。在某些用例中，您可能需要确定性的结果，而在其他情况下，您需要在一系列函数调用中传递一个唯一的值。这就是为什么我不会去嘲笑从一开始就产生的价值。

# 包裹

我希望这篇文章对你有用，并让你的用户下载更多的字节。请记住，尽管 getRandomValues 提供了加密安全的随机值，但它仍然是客户端的，您不应该在安全关键的用例中依赖它。

![](img/01451ebd589f1106fe8ec218ba83ac1a.png)

[现在只需 9 美元就能买到！](https://michalzalecki.com/ebooks/mastering-jest-tips-tricks-for-javascript-developers.html)

【https://michalzalecki.com】最初发表于[](https://michalzalecki.com/generate-unique-id-in-the-browser-without-a-library/)**。**