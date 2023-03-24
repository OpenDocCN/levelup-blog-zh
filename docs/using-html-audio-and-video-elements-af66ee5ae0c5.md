# 使用 HTML 音频和视频元素

> 原文：<https://levelup.gitconnected.com/using-html-audio-and-video-elements-af66ee5ae0c5>

![](img/89345ced569fdd2c9ec26dfdeff396e8.png)

[李安迪](https://unsplash.com/@aperture_andy?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

HTML5 为我们提供了许多工具和标签来处理多媒体。HTML5 的一个新特性是能够嵌入音频和视频，而不需要像 Flash 这样的插件。

在本文中，我们将了解如何向网页添加音频和视频元素，并使用 JavaScript 控制它们。

# 添加音频或视频元素

为了添加音频和视频元素，我们分别使用了`audio`和`video`标签。它们有一些属性。它们是:

*   `autoplay` —指定是否自动播放音频或视频
*   `controls` —指定是否显示回放控件
*   `loop` —指定剪辑结束后是否要再次播放
*   `muted` —指定是否播放声音
*   `preload` —指定剪辑的哪一部分应该预加载。它可以是加载所有内容的`auto`，仅加载元数据的`metadata`，或者不加载任何内容的`none`
*   `src` —剪辑的 URL

要在我们的网页中嵌入音频，我们可以编写以下代码:

```
<audio controls>
  <source src="[https://file-examples.com/wp-content/uploads/2017/11/file_example_OOG_1MG.ogg](https://file-examples.com/wp-content/uploads/2017/11/file_example_OOG_1MG.ogg)" type="audio/ogg">
  <source src="[https://file-examples.com/wp-content/uploads/2017/11/file_example_MP3_700KB.mp3](https://file-examples.com/wp-content/uploads/2017/11/file_example_MP3_700KB.mp3)" type="audio/mpeg">
  Your browser does not support the audio tag.
</audio>
```

在上面的代码中，我们指定了我们想要加载的不同格式的音频。

同样，我们可以对视频做同样的事情:

```
<video controls width="250">
  <source src="[https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4](https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4)" type="video/mp4">
  Your browser does not support the video tag.
</video>
```

在这两段代码中，标签中的文本是在浏览器不支持这些标签时的后备文本。

# 用 JavaScript 控制元素

我们可以用 JavaScript 代码定制音频或视频元素的功能。以下是可用于控制音频或视频元素回放的方法:

*   `play` —开始播放音频或视频
*   `pause` —暂停音频或视频
*   `load` —重新加载音频或视频元素
*   `addTextTrack` —为音频或视频添加文本轨道
*   `canPlayType` —检查浏览器是否可以播放指定类型的音频或视频

我们还可以设置一些属性来控制音频或视频播放:

*   `currentTime` —以秒为单位设置音频或视频的当前时间
*   `defaultMuted` —指示音频输出是否默认静音
*   `defaultPlaybackRate` —表示媒体默认播放的数字
*   `loop` —布尔值，表示播放结束后是否要重新开始播放
*   `muted` —我们可以设置静音的布尔型
*   `playbackRate` —设置剪辑的回放速度
*   `volume` —更改音频或视频的音量

我们可以如下使用这些方法和值属性。首先，我们将剪辑添加到 HTML 代码中:

```
<video controls width="250">
  <source src="[https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4](https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4)" type="video/mp4">
  Sorry, your browser doesn't support embedded videos.
</video>
```

然后，我们可以添加按钮来改变各种事情，如回放速度或静音音频如下。首先，我们添加一些按钮，让我们通过单击来更改设置:

```
<button id='fast-button'>1.5x speed
</button><button id='fastest-button'>2x speed
</button><button id='mute'>Toggle Mute
</button>
```

然后，我们添加一些代码来获取按钮和视频元素，并相应地设置设置。

```
const fastButton = document.querySelector('#fast-button');
const fastestButton = document.querySelector('#fastest-button');
const muteButton = document.querySelector('#mute');
const video = document.querySelector('video');fastButton.onclick = () => {
  video.playbackRate = 1.5
}fastestButton.onclick = () => {
  video.playbackRate = 2
}muteButton.onclick = () => {
  video.muted = !video.muted;
}
```

在上面的代码中，我们获取了元素，然后在按钮的 click 事件处理程序中，我们设置了视频的`playbackRate`和`muted`属性。

我们可以用这些方法做一些类似的事情。例如，我们可以添加以下按钮:

```
<button id='play'>Play
</button><button id='pause'>Pause
</button>
```

然后，我们可以添加以下按钮单击处理程序，以编程方式播放和暂停剪辑:

```
playButton.onclick = () => {
  video.play();
}pauseButton.onclick = () => {
  video.pause();
}
```

这对于创建不使用默认浏览器风格的定制视频和音频播放器，以及显示或隐藏控件的`controls`属性非常方便。

另一个例子是为我们的剪辑制作一个搜索栏。我们可以通过添加一个范围滑块来实现，如下所示:

```
<input type="range" name="time" id='time' min='0'>
```

然后，我们可以将滑块的`max`属性设置为剪辑的持续时间，并添加一个`onchange`事件处理程序，如下所示:

```
timeInput.max = video.duration
timeInput.onchange = (e) => {
  video.currentTime = e.target.value;
}
```

当我们用滑块前后滑动时，让我们从视频的开始到结束寻找任何地方。

![](img/a4da35a19480dc980c277e4353894c40.png)

照片由[普里西拉·杜·普里兹](https://unsplash.com/@priscilladupreez?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 事件

音频和视频元素也触发了以下事件，因此我们可以使用我们希望的事件处理程序来处理它们:

*   `canplay` —浏览器可以播放媒体，但估计没有足够的数据可以在不停止缓冲的情况下完成播放。
*   `canplaythrough` —浏览器估计回放结束，无需停止以获得更多缓冲
*   `complete` —播放结束
*   `durationchange` —持续时间已更新
*   `emptied` —介质已变空
*   `ended` —播放在剪辑结束前结束
*   `loadeddata` —第一帧已经加载完毕
*   `loadedmetadata` —元数据已加载
*   `pause` —播放暂停
*   `play` —播放开始
*   `playing` —回放因缺少数据而暂停或延迟后，准备开始
*   `progress` —浏览器加载资源时定期触发
*   `ratechange` —回放速率改变
*   `seeked`—寻道操作完成
*   `seeking` —寻道操作开始
*   `stalled`—一个用户代理试图获取媒体数据，但没有任何内容传入
*   `suspend` —媒体数据加载已暂停
*   `timeupdate`—`currentTime`属性指示的时间已经更新
*   `volumechange` —音量已改变
*   `waiting` —由于暂时缺少数据，回放停止