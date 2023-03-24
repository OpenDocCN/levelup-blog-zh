# 异步加载一个音频文件并用 howler.js 播放

> 原文：<https://levelup.gitconnected.com/async-load-an-audio-file-and-play-it-with-howler-js-fefc4f537656>

![](img/a5b7790be2ef35e08d17726d08b28a91.png)

如何使用 XHR 将二进制文件下载到 Javascript 对象中。

本文以 [Korerorero](https://github.com/nzcodarnoc/korerorero-front-end/tree/v0.3.1) 项目为例。Korerorero 是一个带有语音识别的动画聊天机器人的开源实现。

为了实现聊天机器人的语音，语音音频由[koerrorero-Mary TTS](https://github.com/nzcodarnoc/korerorero-marytts)服务创建。这些数据需要异步下载，然后存储在内存中，并传递到音频播放器库。 [howler.js](https://github.com/goldfire/howler.js/) 音频库需要文件的 URL。

为了设置阶段，API 已经返回了要下载的 WAV 文件的 URL。下面的代码向音频服务发起一个`GET`请求，并将结果`arraybuffer`存储在内存中。这个`arraybuffer`然后被分派到`recieveAudio(…)`动作。

注:[koreroro-前端](https://github.com/nzcodarnoc/korerorero-front-end/tree/v0.3.1)使用 [redux](https://github.com/reduxjs/redux) 进行状态管理， [axios](https://github.com/axios/axios) 进行网络通信， [redux-thunk](https://github.com/reduxjs/redux-thunk) 进行异步管理。

## response.ts(用 XHR 获得二进制)

*来源:*[*https://github . com/nzcodarnoc/koerrorero-front-end/blob/v 0 . 3 . 1/src/redux/actions/response . ts*](https://github.com/nzcodarnoc/korerorero-front-end/blob/v0.3.1/src/redux/actions/response.ts)

## response.ts(文件同上)

因为 howler.js 需要一个文件引用，上面的代码创建了一个[对象 URL](https://developer.mozilla.org/en-US/docs/Web/API/URL/createObjectURL) ，这是一个引用存储音频的内存位置的 URL。它们看起来像这样:

```
> URL.createObjectURL(new Blob())
< "blob:[https://www.example.com/d2c2bedd-af4e-4ea8-a4c2-7cfe25884e5d](https://developer.mozilla.org/d2c2bedd-af4e-4ea8-a4c2-7cfe25884e5d)"
```

它们可以用在任何需要 URL 的地方，比如`audio`标签的`src`属性。

## 避免内存泄漏

根据[文档](https://developer.mozilla.org/en-US/docs/Web/API/URL/createObjectURL)的说法，内存需要手动管理，这对于长期的[spa](https://en.wikipedia.org/wiki/Single-page_application)来说尤其重要，对于与台式机相比内存通常有限的移动平台来说更是如此。

> 当你不再需要它们时，必须通过调用`[URL.revokeObjectURL()](https://developer.mozilla.org/en-US/docs/Web/API/URL/revokeObjectURL)`来释放它们。

亲爱的读者，在写这篇文章的时候，我还没有实现这个重要的内存释放，所以这个应用程序有内存泄漏。猜猜看，我的待办事项列表的第一位是什么？