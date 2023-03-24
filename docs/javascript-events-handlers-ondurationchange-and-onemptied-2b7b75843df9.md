# JavaScript 事件处理程序— ondurationchange 和 onemptied

> 原文：<https://levelup.gitconnected.com/javascript-events-handlers-ondurationchange-and-onemptied-2b7b75843df9>

![](img/fa7343523e66bc9e861b8376f2853ae1.png)

赫耳墨斯·里维拉在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

在 JavaScript 中，事件是应用程序中发生的动作。它们是由各种事件触发的，比如输入、提交表单、调整大小等元素变化，或者应用程序运行时发生的错误等。我们可以分配事件处理程序来响应这些事件。发生在 DOM 元素上的事件可以通过为相应事件的 DOM 对象的属性分配一个事件处理程序来处理。在本文中，我们将看看`ondurationchange` 和`onemptied`事件处理程序。

# 老化变化

`ondurationchange`事件处理程序让我们可以设置自己的事件处理程序来处理`durationchange`事件，当媒体元素`video`和`audio`的`duration`属性被更新时就会触发该事件。例如，我们可以使用它来获取视频的持续时间，方法是编写以下 HTML 代码来添加一个视频和一个`p`元素，用于将视频的持续时间告知网页:

```
<video width="320" height="240" controls id='video'>
  <source src="[https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_1mb.mp4](https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_1mb.mp4)" type="video/mp4">
  Your browser does not support the video tag.
</video><p id='duration'></p>
```

然后在我们的 JavaScript 代码中，我们可以使用`ondurationchange`属性为它分配我们自己的事件处理程序，如下面的代码所示:

```
const video = document.getElementById('video');
const duration = document.getElementById('duration');video.ondurationchange = (e) => {
  console.log(e.srcElement.duration);
  duration.innerHTML = `The video is ${e.srcElement.duration} seconds long`;
};
```

在我们的事件处理函数中，我们有一个`e`参数，它是一个`Event`对象。其中有一个`srcElement`属性，该属性具有`video`元素的 DOM 对象表示，这是触发`durationchange`事件的源元素。由于`srcElement`是`video`元素，我们可以通过使用`duration`属性用它来获得持续时间，持续时间以秒为单位。然后在我们的`ondurationchange`事件处理函数中，我们可以通过添加以下内容来显示视频的持续时间:

```
duration.innerHTML = `The video is ${e.srcElement.duration} seconds long`;
```

之后，当我们重新加载页面时，我们得到视频，并显示“视频长 13.696 秒”，因为当我们使用`e.srcElement.duration`以秒为单位获得视频持续时间时，我们得到了 13.696 秒的持续时间。我们也可以使用`addEventListener`方法来编写这样的代码，而不是像下面这样:

```
video.addEventListener('durationchange', (e) => {
  console.log(e.srcElement.duration);
  duration.innerHTML = `The video is ${e.srcElement.duration} seconds long`;
})
```

这与给`ondurationchange`属性分配一个事件处理程序是完全一样的。

![](img/7b611ff69b0e1cb87cee04edb924b2d3.png)

托马斯·勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 一个空的

我们可以为`onemptied`属性设置自己的事件处理程序来处理`emptied`事件，该事件在媒体变空时被触发。例如，当媒体已经被加载并且调用`load`方法来重新加载它时。为了使用这个属性，我们可以将下面的`video`添加到我们的 HTML 代码中:

```
<video width="320" height="240" controls id='video'>
  <source src="[https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_1mb.mp4](https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_1mb.mp4)" type="video/mp4">
  Your browser does not support the video tag.
</video>
```

然后，当我们调用`load`方法的`video`元素时，如下面的代码所示:

```
const video = document.getElementById('video');
const duration = document.getElementById('duration');video.load();video.onemptied = (e) => {
  console.dir(e.srcElement);
};
```

然后我们可以看到`onemptied`事件处理程序被调用，因为我们将看到`srcElement`属性被记录在事件处理程序函数中。`srcElement`具有触发`emptied`事件的`video`元素。我们应该从事件处理函数中的`console.dir`调用中得到类似下面的输出:

```
accessKey: ""
assignedSlot: null
attributeStyleMap: StylePropertyMap {size: 0}
attributes: NamedNodeMap {0: width, 1: height, 2: controls, 3: id, width: width, height: height, controls: controls, id: id, length: 4}
autocapitalize: ""
autoplay: false
baseURI: "[https://fiddle.jshell.net/_display/](https://fiddle.jshell.net/_display/)"
buffered: TimeRanges {length: 1}
childElementCount: 1
childNodes: NodeList(3) [text, source, text]
children: HTMLCollection [source]
classList: DOMTokenList [value: ""]
className: ""
clientHeight: 240
clientLeft: 0
clientTop: 0
clientWidth: 320
contentEditable: "inherit"
controls: true
controlsList: DOMTokenList [value: ""]
crossOrigin: null
currentSrc: "[https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_1mb.mp4](https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_1mb.mp4)"
currentTime: 0
dataset: DOMStringMap {}
defaultMuted: false
defaultPlaybackRate: 1
dir: ""
disablePictureInPicture: false
disableRemotePlayback: false
draggable: false
duration: 13.696
elementTiming: ""
ended: false
enterKeyHint: ""
error: null
firstChild: text
firstElementChild: source
height: 240
hidden: false
id: "video"
innerHTML: "
  <source src="[https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_1mb.mp4](https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_1mb.mp4)" type="video/mp4">
  Your browser does not support the video tag.
"
...
offsetHeight: 240
offsetLeft: 8
offsetParent: body
offsetTop: 8
offsetWidth: 320
...
outerHTML: "<video width="320" height="240" controls="" id="video">
  <source src="[https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_1mb.mp4](https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_1mb.mp4)" type="video/mp4">
  Your browser does not support the video tag.
</video>"
outerText: ""
ownerDocument: document
parentElement: body
parentNode: body
part: DOMTokenList [value: ""]
paused: true
playbackRate: 1
played: TimeRanges {length: 0}
playsInline: false
poster: ""
prefix: null
preload: "metadata"
previousElementSibling: null
previousSibling: text
readyState: 4
remote: RemotePlayback {state: "disconnected", onconnecting: null, onconnect: null, ondisconnect: null}
scrollHeight: 240
scrollLeft: 0
scrollTop: 0
scrollWidth: 320
seekable: TimeRanges {length: 1}
seeking: false
shadowRoot: null
sinkId: ""
slot: ""
spellcheck: true
src: ""
srcObject: null
style: CSSStyleDeclaration {alignContent: "", alignItems: "", alignSelf: "", alignmentBaseline: "", all: "", …}
tabIndex: 0
tagName: "VIDEO"
textContent: "

  Your browser does not support the video tag.
"
textTracks: TextTrackList {length: 0, onchange: null, onaddtrack: null, onremovetrack: null}
title: ""
translate: true
videoHeight: 240
videoWidth: 320
volume: 1
webkitAudioDecodedByteCount: 10995
webkitDecodedFrameCount: 4
webkitDisplayingFullscreen: false
webkitDroppedFrameCount: 0
webkitSupportsFullscreen: true
webkitVideoDecodedByteCount: 37638
width: 320
```

或者，我们可以使用`addEventListener`方法来做同样的事情:

```
video.addEventListener('emptied, (e) => {
  console.dir(e.srcElement);
});
```

`video`或其他媒体元素如`audio`有一些方便的值属性:

*   `buffered` —返回`TimeRanges`对象的只读属性，该对象具有时间范围的集合，用于在加载媒体时跟踪媒体的哪一部分已经被缓冲。我们可以通过向`start`和`end`方法传递一个索引来获得每个范围。表示在访问`buffered`属性时，浏览器已经缓冲的媒体源的范围(如果有的话)。
*   `defaultMuted` —反映`muted` HTML 属性的布尔属性，该属性指示媒体元素的音频输出是否应该默认静音。
*   `defaultPlaybackRate` —表示媒体默认播放速率的数字属性。我们可以通过给它分配一个正数来设置它，或者从这个属性中获取值。
*   `duration` —只读双精度浮点值，表示媒体的总持续时间(秒)。如果没有可用的媒体数据，返回值为`NaN`。如果媒体的长度不定，那么像 WebRTC 流，那么值是`+Infinity`。
*   `ended` —只读属性，返回指示媒体元素是否已结束播放的布尔值。
*   `muted` —决定音频是否静音的布尔属性。`true`如果音频静音，否则`false`。这是一个可写属性，所以我们可以设置它。
*   `networkState` —一个只读属性，返回一个数字，指示通过网络获取媒体的当前状态。它可以是 0、1、2 或 3。0 表示没有数据，1 表示元素已经选择了一个资源，但是它没有使用网络。2 表示浏览器正在下载数据，3 表示没有找到`src`属性。
*   `paused` —只读属性，返回指示媒体元素是否暂停的布尔值。
*   `playbackRate` —表示媒体播放速率的数字。它是一个正数，我们可以设置这个属性并从中获取一个值。
*   `played` —一个只读属性，返回一个`TimeRanges`对象，该对象包含浏览器已经播放的媒体源的范围(如果有的话)。

# 结论

`ondurationchange`事件处理程序允许我们设置自己的事件处理程序来处理`durationchange`事件，当媒体元素`video`和`audio`的`duration`属性被更新时，就会触发该事件。我们可以为`onemptied`属性设置自己的事件处理程序来处理`emptied`事件，该事件在媒体变空时被触发。例如，当媒体已经被加载并且调用`load`方法来重新加载它时。我们可以用`e`参数的`srcElement`属性获得触发这些事件的元素，该参数是我们的事件处理函数中的一个`Event`对象。它有许多有用的属性，可以让我们获得许多关于媒体元素的信息。