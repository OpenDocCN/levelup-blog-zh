# JavaScript 事件处理程序—元数据和 Ajax 事件

> 原文：<https://levelup.gitconnected.com/javascript-events-handlers-metadata-and-ajax-events-de440bafb851>

![](img/a52ccd1fbabb226f92cefd99f39bd9ac.png)

照片由[格温·威斯丁克](https://unsplash.com/@aboeka?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 JavaScript 中，事件是应用程序中发生的动作。它们是由各种事件触发的，比如输入、提交表单、调整大小等元素变化，或者应用程序运行时发生的错误等。我们可以分配一个事件处理程序来处理这些事件。

发生在 DOM 元素上的事件可以通过为相应事件的 DOM 对象的属性分配一个事件处理程序来处理。在本文中，我们将研究媒体 DOM 元素的`onloadedmetadata`属性和 XmlHttpRequest 对象的`onloadend`属性。

# onloadedmetadata

媒体 DOM 元素的`onloadedmetadata`属性让我们设置一个事件处理函数，当`loademetadata`事件被触发时运行这个函数。每当加载媒体元数据时，都会触发此事件。

例如，我们可以先在 HTML 代码中添加一个视频标签来使用它:

```
<video src='[https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4'](https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4')></video>
```

然后在相应的 JavaScript 代码中，我们可以为`onloadedmetadata`属性设置一个事件处理函数，如下面的代码所示:

```
const video = document.querySelector('video');video.onloadedmetadata = (e) => {
  console.log(e); 
}
```

然后我们在`e`参数的`srcElement`属性中获取视频的元数据，这是一个`Event`对象。我们会得到这样的结果:

```
clientHeight: 240
clientLeft: 0
clientTop: 0
clientWidth: 320
contentEditable: "inherit"
controls: false
controlsList: DOMTokenList [value: ""]
crossOrigin: null
currentSrc: "[https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4](https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4)"
currentTime: 0
dataset: DOMStringMap {}
defaultMuted: false
defaultPlaybackRate: 1
dir: ""
disablePictureInPicture: false
disableRemotePlayback: false
draggable: false
duration: 368.2
elementTiming: ""
ended: false
enterKeyHint: ""
error: null
firstChild: null
firstElementChild: null
height: 0
hidden: false
id: ""
innerHTML: ""
innerText: ""
inputMode: ""
isConnected: true
isContentEditable: false
lang: ""
lastChild: null
lastElementChild: null
localName: "video"
loop: false
mediaKeys: null
muted: false
namespaceURI: "[http://www.w3.org/1999/xhtml](http://www.w3.org/1999/xhtml)"
networkState: 1
nextElementSibling: script
nextSibling: text
nodeName: "VIDEO"
nodeType: 1
nodeValue: null
nonce: ""
offsetHeight: 240
offsetLeft: 8
offsetParent: body
offsetTop: 8
offsetWidth: 320
...
outerHTML: "<video src="[https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4](https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4)"></video>"
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
src: "[https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4](https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4)"
srcObject: null
style: CSSStyleDeclaration {alignContent: "", alignItems: "", alignSelf: "", alignmentBaseline: "", all: "", …}
tabIndex: -1
tagName: "VIDEO"
textContent: ""
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
```

一些有用的元数据包括`videoHeight`，它以像素为单位告诉我们视频的高度，`videoWidth`，它以像素为单位告诉我们视频的宽度，以及`duration`，它以秒为单位告诉我们视频的长度。`duration`也可用音频元素。

![](img/b29cfd20eb2ed893c5cb9983913fc7ca.png)

[浸礼会标准员](https://unsplash.com/@baptiststandaert?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# onloadend

XMLHttpRequest 对象的`onloadend`属性允许我们为它分配一个事件处理程序，每当触发`loadend`事件时，该处理程序就会运行。无论请求是否成功完成，只要请求完成，就会触发`loadend`事件。如果成功，那么这个事件将在`load`事件之后被触发。否则，它将在`abort`或`error`事件后触发。

例如，我们可以为`onloadend`事件分配一个事件处理程序，就像我们在下面的代码中所做的那样:

```
const loadButtonSuccess = document.querySelector('.load.success');
const loadButtonError = document.querySelector('.load.error');
const loadButtonAbort = document.querySelector('.load.abort');
const log = document.querySelector('.event-log');function handleLoadEnd(e) {
    log.textContent = log.textContent + `${e.type}: ${e.loaded} bytes transferred\n`;
}function addListeners(xhr) {
  xhr.onloadend = handleLoadEnd;
}function get(url) {
  log.textContent = ''; const xhr = new XMLHttpRequest();
  addListeners(xhr);
  xhr.open("GET", url);
  xhr.send();
  return xhr;  
}loadButtonSuccess.addEventListener('click', () => {
    get('[https://jsonplaceholder.typicode.com/todos/1'](https://jsonplaceholder.typicode.com/todos/1'));
});loadButtonError.addEventListener('click', () => {
    get('[https://somewhere.org/i-dont-exist'](https://somewhere.org/i-dont-exist'));
});loadButtonAbort.addEventListener('click', () => {
    get('[https://jsonplaceholder.typicode.com/todos/1').abort();](https://jsonplaceholder.typicode.com/todos/1').abort();)
});
```

在上面的代码中，我们有 3 个按钮，每当每个按钮被点击时都会运行`click`事件处理程序。点击“加载(成功)”按钮将运行`get` 功能。我们将在对`get`函数的调用中传递一个有效的 URL。“加载(成功)”按钮的点击处理由以下模块完成:

```
loadButtonSuccess.addEventListener('click', () => {
    get('[https://jsonplaceholder.typicode.com/todos/1'](https://jsonplaceholder.typicode.com/todos/1'));
});
```

JSONPlaceholder 有一个可以为任何 URL 服务的测试 API，因为它托管了一个假 API，所以我们可以加载它而不会得到任何错误。同样，我们有按钮加载一个会给出错误的 URL，还有另一个按钮加载一个有效的 URL，但是我们中止请求。一旦 XmlHttpRequest 完成，我们分配给`onloadend`事件处理程序的函数，也就是`handleLoadEnd`函数，将会运行。

`handleLoadEnd`函数有一个参数，它是一个`Event`对象，包含一些关于已完成请求的数据。在该函数中，我们获得了`type`属性的值，该属性具有被触发的事件类型，应该是`loadend`。此外，我们还获得了属性`loaded`的值，它包含了已经加载的数据的字节数。

然后在 HTML 代码中，我们添加上面的`querySelector`调用中列出的元素:

```
<div class="controls">
    <input class="load success" type="button" name="xhr" value="Load (success)" />
    <br>
    <input class="load error" type="button" name="xhr" value="Load (error)" />
    <br>
    <input class="load abort" type="button" name="xhr" value="Load (abort)" />
</div><textarea readonly class="event-log"></textarea>
```

我们有 3 个按钮可以点击来加载成功的 HTTP 请求，一个带有不存在的 URL 的 HTTP 请求，和一个中止的 HTTP 请求。然后我们显示触发的事件和加载的字节数。

media DOM 元素的`onloadedmetadata`属性让我们设置一个事件处理函数，当`loademetadata`事件被触发时运行这个函数。每当加载媒体元数据时，都会触发此事件。我们可以从事件处理函数的`event`参数的`srcElement`属性中获取元数据。

XMLHttpRequest 对象的`onloadend`属性让我们为它分配一个事件处理程序，每当`loadend`事件被触发时，它就会运行。无论请求是否成功完成，只要请求完成，就会触发`loadend`事件。如果成功，那么这个事件将在`load`事件之后被触发。否则，它将在`abort`或`error`事件之后触发。我们可以通过设置 XMLHttpRequest 对象的`onloadend`属性来处理这个事件。事件处理函数应该有一个`event`参数，该参数是一个具有`type`属性的`event`对象，该对象具有被触发的事件类型，应该是`loadend`。此外，我们还获得了`loaded`属性的值，它包含了已经加载的数据的字节数。