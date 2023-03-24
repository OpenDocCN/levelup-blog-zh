# 处理事件侦听器回调中的反应状态

> 原文：<https://levelup.gitconnected.com/handle-react-state-inside-an-event-listener-callback-2c37399c3e0c>

## 而无需在每次渲染时重新创建函数。

![](img/b28e95c9a883d2aeaff52135b596ad2f.png)

照片由 [Erik-Jan Leusink](https://unsplash.com/@ejleusink?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

前几天，我的女朋友 danwessels 正在开发一个功能，你可以使用键盘上的箭头来浏览一系列图像。旋转木马已经有了 UI 箭头的功能，但是人类很懒，不想移动光标和点击按钮。**添加一个足够简单的功能…或者是吗？**

添加键盘功能之前的轮播代码:

```
import { useEffect, useState } from 'react'import { getImages } from './api/images'const Carousel = () => {
  const [currentImage, setCurrentImage] = useState(null)
  const [images, setImages] = useState([])
  const [currentIndex, setCurrentIndex] = useState(0) useEffect(() => {
    fetchImages()
  }, []) useEffect(() => {
    setCurrentIndex(images.findIndex(({ id }) => id === currentImage.id))
  }, [images, currentImage]) async function fetchImages() {
    const images = getImages() // e.g. from an API
    setImages(images)
    setCurrentImage(images[0])
  } function nextItem() {
    let newCurrentIndex = currentIndex
    if (currentIndex === images.length - 1) newCurrentIndex = 0
    else newCurrentIndex += 1
    setCurrentImage(images[newCurrentIndex])
  } function previousItem() {
    let newCurrentIndex = currentIndex
    if (currentIndex === 0) newCurrentIndex = images.length - 1
    else newCurrentIndex -= 1
    setCurrentImage(images[newCurrentIndex])
  } return (
    <div>
      {images?.length > 1 && (
        <button
          onClick={previousItem}
        >
          Previous
        </button>
      )}
      {currentImage?.id &&
        <div>
          <img src={currentImage.src} alt={currentImage.altText} />
        </div>
      }
      {images.length > 1 && (
        <button
          onClick={nextItem}
        >
          Next
        </button>
      )}
    </div>
  )
}export default Carousel
```

carousel 代码很简单，但是还没有使用键盘箭头的功能。为此，您需要通过这样做来监听`keydown`事件:

```
useEffect(() => {
  document.addEventListener('keydown', navigate)
  return function cleanup() {
    document.removeEventListener('keydown', navigate)
  }
}, [])function navigate(e) {
  if (e.keyCode === 37) {
    previousItem()
  } else if (e.keyCode === 39) {
    nextItem()
  }
}
```

问题是`navigate`函数被声明为`eventListener`的回调函数。这样做意味着`navigate`函数是用初始状态创建的，而**永远不会有最新的状态。有一个简单的修复方法，就是将状态附加到`useEffect`钩子的依赖数组中:**

```
useEffect(() => {
  document.addEventListener('keydown', navigate)
  return function cleanup() {
    document.removeEventListener('keydown', navigate)
  }
}, [currentIndex])
```

但是这样做意味着导航功能将在每次点击后被重新创建！那么我们该怎么办？

这是我女朋友想出来的😄：

```
const prevBtn = useRef(null)
const nextBtn = useRef(null)useEffect(() => {
  document.addEventListener('keydown', navigate)
  return function cleanup() {
    document.removeEventListener('keydown', navigate)
  }
}, [])function navigate(e) {
  if (e.keyCode === 37) {
    prevBtn.current.click()
  } else if (e.keyCode === 39) {
    nextBtn.current.click()
  }
}// render method
<button
  onClick={previousItem}
  ref={prevBtn}
>
  Previous
</button><button
  onClick={nextItem}
  ref={nextBtn}
>
  Previous
</button>
```

通过这样做，我们从按钮模拟了`onClick`事件，并且我们可以访问我们组件的最新`state`。相当牛逼！🎉

完整的代码现在看起来像这样:

```
import { useEffect, useRef, useState } from 'react'import { getImages } from './api/images'const Carousel = () => {
  const prevBtn = useRef(null)
  const nextBtn = useRef(null)
  const [currentImage, setCurrentImage] = useState(null)
  const [images, setImages] = useState([])
  const [currentIndex, setCurrentIndex] = useState(0) useEffect(() => {
    fetchImages()
    document.addEventListener('keydown', navigate)
    return function cleanup() {
      document.removeEventListener('keydown', navigate)
    }
  }, [])useEffect(() => {
    setCurrentIndex(images.findIndex(({ id }) => id === currentImage.id))
  }, [images, currentImage]) function navigate(e) {
    if (e.keyCode === 37) {
      prevBtn.current.click()
    } else if (e.keyCode === 39) {
      nextBtn.current.click()
    }
  } async function fetchImages() {
    const images = getImages() // e.g. from an API
    setImages(images)
    setCurrentImage(images[0])
  } function nextItem() {
    let newCurrentIndex = currentIndex
    if (currentIndex === images.length - 1) newCurrentIndex = 0
    else newCurrentIndex += 1
    setCurrentImage(images[newCurrentIndex])
  } function previousItem() {
    let newCurrentIndex = currentIndex
    if (currentIndex === 0) newCurrentIndex = images.length - 1
    else newCurrentIndex -= 1
    setCurrentImage(images[newCurrentIndex])
  } return (
    <div>
      {images?.length > 1 && (
        <button
          onClick={previousItem}
          ref={prevBtn}        
         >
          Previous
        </button>
      )}
      {currentImage?.id &&
        <div>
          <img src={currentImage.src} alt={currentImage.altText} />
        </div>
      }
      {images.length > 1 && (
        <button
          onClick={nextItem}
          ref={nextBtn}
         >
          Next
        </button>
      )}
    </div>
  )
}export default Carousel
```

尽情享受吧！所有的荣誉都要归功于我的女朋友 danwessels 提出了这个巧妙的解决方案。

[](https://blog.jagonzalr.com/membership) [## 加入我的介绍链接媒体-何塞安东尼奥冈萨雷斯罗德里格斯

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

blog.jagonzalr.com](https://blog.jagonzalr.com/membership)