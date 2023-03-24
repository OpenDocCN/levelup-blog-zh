# 创建自动笑话模仿

> 原文：<https://levelup.gitconnected.com/creating-automatic-jest-mocks-2fe5137c2a1>

![](img/b13dcba1c7ab4c52ebb246417eca185c.png)

照片由 [Battlecreek 咖啡烘焙师](https://unsplash.com/@battlecreekcoffeeroasters?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

为了创建单元测试，我们经常需要模拟函数来绕过代码的各个部分。例如，如果我们不想运行在函数中调用 API 的代码，那么我们必须创建该函数的模拟。

在本文中，我们将研究一些在 Jest 测试中创建模拟函数和类的方法。

# ES6 类模拟

我们可以使用 Jest 来创建 ES6 类模拟。

ES6 类只是构造函数之上的语法糖，所以我们可以使用模拟函数的相同方法来模拟类。

# 制作测试用的东西

首先，我们通过创建`videoPlayer.js`并添加:

```
class VideoPlayer {
    constructor() { } playVideoFile(fileName) {
        console.log(`Playing ${fileName}`);
    }
}module.exports = VideoPlayer;
```

然后我们创建`app.js`如下:

```
const VideoPlayer = require('./videoPlayer');class VideoPlayerApp {
    constructor() {
        this.videoPlayer = new VideoPlayer();
    } play() {
        const videoFileName = 'video.mp4';
        this.videoPlayer.playVideoFile(videoFileName);
    }
}module.exports = VideoPlayerApp;
```

# 创建自动模拟

在这个例子中，我们将模拟`VideoPlayer`类。

为此，我们可以调用`jest.mock(‘./videoPlayer’);`，因为我们用`module.exports`导出了`VideoPlayer`类。

Jest 足够聪明，可以在测试中创建自己的模拟类来代替实际的`VideoPlayer`类。

它将用一个模拟构造函数替换这个类，并用总是返回`undefined`的模拟函数替换它的所有方法。

方法调用保存在`theAutomaticMock.mock.instances[index].methodName.mock.calls`中，其中`theAutomaticMock`被替换为与我们最初模仿的类相同的名称。

在我们的例子中，我们不打算创建自己的方法实现，所以一旦我们调用了`jest.mock(‘./videoPlayer’);`，我们就完成了对`VideoPlayer`类的模仿。

然后我们可以创建`app.test.js`来放置我们的测试。我们补充如下:

```
const VideoPlayer = require('./videoPlayer');
const VideoPlayerApp = require('./app');
jest.mock('./videoPlayer');beforeEach(() => {
    VideoPlayer.mockClear();
});test('VideoPlayer is called once', () => {
    const videoPlayerApp = new VideoPlayerApp();
    expect(VideoPlayer).toHaveBeenCalledTimes(1);
});test('VideoPlayer is called with video.mp4', () => {
    expect(VideoPlayer).not.toHaveBeenCalled();
    const videoPlayerApp = new VideoPlayerApp();
    videoPlayerApp.play();
    const videoFileName = 'video.mp4';
    const mockVideoPlayer = VideoPlayer.mock.instances[0];
    const mockPlayVideoFile = mockVideoPlayer.playVideoFile;
    expect(mockPlayVideoFile.mock.calls[0][0]).toEqual(videoFileName);
    expect(mockPlayVideoFile).toHaveBeenCalledWith(videoFileName);
    expect(mockPlayVideoFile).toHaveBeenCalledTimes(1);
});
```

我们重视包含每个类的模块。`VideoPlayer`类将被我们的 mock 替换，但是我们仍然需要导入它。

然后我们有我们的`beforeEach`钩子，它运行:

```
VideoPlayer.mockClear();
```

清除`VideoPlayer`类的模拟。

接下来，我们编写第一个测试，它是:

```
test('VideoPlayer is called once', () => {
    const videoPlayerApp = new VideoPlayerApp();
    expect(VideoPlayer).toHaveBeenCalledTimes(1);
}););
```

我们创建一个新的`VideoPlayerApp`实例。`VideoPlayerApp`调用的构造函数运行`new VideoPlayer();`，这意味着`VideoPlayer`类被调用。

然后我们检查:

```
expect(VideoPlayer).toHaveBeenCalledTimes(1);
```

这应该是正确的，因为`VideoPlayer`构造函数，也就是类，被调用了一次。

接下来，我们来看第二个测试，它更复杂:

```
test('VideoPlayer is called with video.mp4', () => {
    expect(VideoPlayer).not.toHaveBeenCalled();
    const videoPlayerApp = new VideoPlayerApp();
    videoPlayerApp.play();
    const videoFileName = 'video.mp4';
    const mockVideoPlayer = VideoPlayer.mock.instances[0];
    const mockPlayVideoFile = mockVideoPlayer.playVideoFile;
    expect(mockPlayVideoFile.mock.calls[0][0]).toEqual(videoFileName);
    expect(mockPlayVideoFile).toHaveBeenCalledWith(videoFileName);
    expect(mockPlayVideoFile).toHaveBeenCalledTimes(1);
});
```

首先，我们检查`VideoPlayer`没有被调用，以确保`VideoPlayer.mockClear()`正在工作。

然后，我们创建一个新的`VideoPlayerApp`实例，并对其调用`play`，如下所示:

```
const videoPlayerApp = new VideoPlayerApp();
videoPlayerApp.play();
```

之后，我们通过运行以下命令获得我们的模拟`VideoPlayer`实例:

```
const mockVideoPlayer = VideoPlayer.mock.instances[0];
```

然后我们调用被模仿的`VideoPlayer`类的`playVideoFile`方法如下:

```
const mockPlayVideoFile = mockVideoPlayer.playVideoFile;
```

然后，我们可以通过使用以下命令获得传递给模拟`playVideoFile`调用的第一个参数:

```
mockPlayVideoFile.mock.calls[0][0]
```

然后，我们可以通过编写以下内容来检查调用是否通过了`'video.mp4'`:

```
expect(mockPlayVideoFile.mock.calls[0][0]).toEqual(videoFileName);
```

等价地，我们可以写:

```
expect(mockPlayVideoFile).toHaveBeenCalledWith(videoFileName);
```

最后，我们通过运行以下命令来检查`playVideoFile`方法是否只被调用了一次:

```
expect(mockPlayVideoFile).toHaveBeenCalledTimes(1);
```

这是有意义的，因为在我们的测试中`playVideoFile`只被调用一次。

![](img/bf476f03abb410ed308e4fc88037d05c.png)

[安德鲁·唐劳](https://unsplash.com/@andrewtanglao?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 运行测试

确保通过运行以下命令安装 Jest:

```
npm i jest
```

并放上:

```
"test": "jest"
```

在`scripts`部分，这样我们可以运行测试。

最后，我们应该得到:

```
PASS  ./app.test.js
  √ VideoPlayer is called once (3ms)
  √ VideoPlayer is called with video.mp4 (2ms)Test Suites: 1 passed, 1 total
Tests:       2 passed, 2 total
Snapshots:   0 total
Time:        2.067s
Ran all test suites.
```

# 结论

有了 Jest 的自动模拟，我们可以很容易地模拟类或构造函数。

所有方法都用返回`undefined`的函数模拟。

然后我们可以通过使用`mockedObject.mock.instances`来检索 mock，这是一个数组。

然后我们可以像往常一样进行检查。用`toEqual`、`toHaveBeenCalledWith`等匹配器方法。