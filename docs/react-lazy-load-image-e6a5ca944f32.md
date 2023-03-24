# 反应迟缓的图像加载和打字稿-不再有缓慢和破碎的图像

> 原文：<https://levelup.gitconnected.com/react-lazy-load-image-e6a5ca944f32>

![](img/bf357e2395a53a115f197c2290f10e51.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

创造一个更好的 UX 并不像看起来那么简单。页面上的每个组件都很重要。在处理一段复杂的代码时，我们几乎忘记了最简单的事情，一个损坏的图像。

在本教程中，我将解释如何在不破坏现有代码的情况下创建一个简单的`Image`组件。该组件将支持延迟加载和损坏的图像。

## 1.创建图像组件

首先，在组件库中创建一个文件夹，并创建一个名为`index.tsx`的文件。

```
mkdir -p components/Image
touch components/Image/index.tsx
```

给`index.tsx`加上下面一行。

```
// components/Image/index.tsx

import React from "react";interface ImageProps extends React.ImgHTMLAttributes<HTMLImageElement> {}export default ({ ...props }: ImageProps) => {
  return <img {...props} />;
};
```

**注意:**我使用 typescript 来构建这个组件。如果您的项目不支持 typescript，您可以删除键入。

## 2.如何在 App 页面消费

您可以像使用 **img** HTML 元素一样使用 **Image** 组件。因为这个图像组件扩展了`HTMLImageElement.`，所以它支持所有的图像属性。

```
// App.tsximport React from "react";
import Image from "./components/Image";function App() {
  return (
    <div className="App">
      <section className="section">
        <div className="container">
          <h1 className="title">React Components Demo</h1>
          <p className="subtitle">Lazy Image</p>
          **<Image
            style={{ width: 400, height: 300 }}
            src="**[**https://upload.wikimedia.org/wikipedia/commons/f/ff/Pizigani_1367_Chart_10MB.jpg**](https://upload.wikimedia.org/wikipedia/commons/f/ff/Pizigani_1367_Chart_10MB.jpg)**"
          />**
          <br />
          <Image src="[https://doesnot.exits.com/image.png](https://doesnot.exits.com/image.png)" />
        </div>
      </section>
    </div>
  );
}export default App;
```

尝试重新加载页面，你会发现图像加载非常缓慢。这是因为图像是`10mb`大。同时另一个图像将被打破。

![](img/e21460e50c1b22567d6d5b3de2a84910.png)

## 3.添加默认图像道具(占位符)

让我们添加一个默认的图像道具，修改界面。

```
// components/Image/index.tsxinterface ImageProps extends React.ImgHTMLAttributes<HTMLImageElement> {
  **placeholderImg?: string;**
}
```

使用此`placeholderImg`图像，并在加载实际图像之前向用户显示此图像。

```
// components/Image/index.tsx

export default ({ placeholderImg, src, ...props }: ImageProps) => {
  **const [imgSrc, setSrc] = useState(placeholderImg || src);**
  return <img {...props} src={imgSrc} />;
};
```

现在问题来了，如果我们显示一个占位符图像，那么我们将如何加载实际的图像。

![](img/acbe559f0be8964c084e8118ff8c3997.png)

## 4.动态加载图像

为此，我们将使用 JavaScript 中的**图像类**。我们将动态创建一个图像，并向其中添加一个事件侦听器。

```
// components/Image/index.tsxexport default ({ src, placeholderImg, ...props }: ImageProps) => {
  const [imgSrc, setSrc] = useState(placeholderImg || src); **const onLoad = useCallback(() => {
    setSrc(src);
  }, [src]);** useEffect(() => {
    **const img = new Image();
    img.src = src as string;
    img.addEventListener("load", onLoad);** return () => {
      **img.removeEventListener("load", onLoad);**
    }; }, [src, onLoad]); return <img {...props} alt={imgSrc} src={imgSrc} />;
};
```

这里我们使用 **useEffect** 来创建图像并将其加载到后台。我们可以使用`addEventListener`来监听加载事件。一旦图像被加载，我们可以用实际的 src URL 替换 src。因为图像已经加载并缓存到浏览器中。下一次装货马上就要开始了。

*因为我们在装载组件时向映像添加了事件监听器和错误监听器。我们应该在卸载组件时移除。*

我们来修改一下 App 代码。

```
// App.tsximport React from "react";
import Image from "./components/Image";function App() {
  return (
    <div className="App">
      <section className="section">
        <div className="container">
          <h1 className="title">React Components Demo</h1>
          <p className="subtitle">Lazy Image</p>
          **<Image
  style={{width: 400, height: 300 }} 
  placeholderImg="**[**https://via.placeholder.com/400x200.png?text=This+Will+Be+Shown+Before+Load**](https://via.placeholder.com/400x200.png?text=This+Will+Be+Shown+Before+Load)**"
  src="**[**https://upload.wikimedia.org/wikipedia/commons/f/ff/Pizigani_1367_Chart_10MB.jpg**](https://upload.wikimedia.org/wikipedia/commons/f/ff/Pizigani_1367_Chart_10MB.jpg)**"
/>**
          <br />
          **<Image
  placeholderImg="**[**https://via.placeholder.com/400x200.png?text=This+Will+Be+Shown+Before+Load**](https://via.placeholder.com/400x200.png?text=This+Will+Be+Shown+Before+Load)**"
  src="**[**https://doesnot.exits.com/image.png**](https://doesnot.exits.com/image.png)**"
/>**
        </div>
      </section>
    </div>
  );
}export default App;
```

![](img/c5fb3743e7f8c768d6b72955201ca5f4.png)

*如果您注意到，第二个图像仍然显示占位符图像。由于图像从未加载成功，所以* `*setSrc*` *从未被调用。*

## 5.处理图像加载错误

让我们来处理图像加载错误。为此，让我们添加另一个属性 **errorImg** 和**监听**的错误**状态。**

```
// components/Image/index.tsximport React, { useCallback, useEffect, useState } from "react";interface ImageProps extends React.ImgHTMLAttributes<HTMLImageElement> {
  placeholderImg?: string;
 ** errorImg?: string;**
}export default ({ src, placeholderImg, errorImg, ...props }: ImageProps) => {
  const [imgSrc, setSrc] = useState(placeholderImg || src);
  const onLoad = useCallback(() => {
    setSrc(src);
  }, [src]); **const onError = useCallback(() => {
    setSrc(errorImg || placeholderImg);
  }, [errorImg, placeholderImg]);** useEffect(() => {
    const img = new Image();
    img.src = src as string;
    img.addEventListener("load", onLoad); **img.addEventListener("error", onError);** return () => {
      img.removeEventListener("load", onLoad);
      **img.removeEventListener("error", onError);**
    }; }, [src, onLoad, onError]);
  return <img {...props} alt={imgSrc} src={imgSrc} />;
};
```

我们来修改一下 App 代码。

```
// App.tsximport React from "react";
import Image from "./components/Image";function App() {
  return (
    <div className="App">
      <section className="section">
        <div className="container">
          <h1 className="title">React Components Demo</h1>
          <p className="subtitle">Lazy Image</p>
          <Image
            style={{ width: 400, height: 300 }}
            placeholderImg="[https://via.placeholder.com/400x200.png?text=This+Will+Be+Shown+Before+Load](https://via.placeholder.com/400x200.png?text=This+Will+Be+Shown+Before+Load)"
            src="[https://upload.wikimedia.org/wikipedia/commons/f/ff/Pizigani_1367_Chart_10MB.jpg](https://upload.wikimedia.org/wikipedia/commons/f/ff/Pizigani_1367_Chart_10MB.jpg)"
          />
          <br />
          <Image
            **errorImg="**[**https://image.shutterstock.com/image-vector/no-image-available-vector-illustration-260nw-744886198.jpg**](https://image.shutterstock.com/image-vector/no-image-available-vector-illustration-260nw-744886198.jpg)**"**
            placeholderImg="[https://via.placeholder.com/400x200.png?text=This+Will+Be+Shown+Before+Load](https://via.placeholder.com/400x200.png?text=This+Will+Be+Shown+Before+Load)"
            src="[https://doesnot.exits.com/image.png](https://doesnot.exits.com/image.png)"
          />
        </div>
      </section>
    </div>
  );
}export default App;
```

![](img/c7dc3ac94001034d7832bf49f40f31aa.png)

**结论:**现在我们已经用相同的组件实现了破损图像和延迟加载图像。

**注意:**您可以将占位符图像用作加载器，并在加载实际图像之前显示加载图像。为此，您可以使用过渡动画或 Gif。

**CodeSandbox Domo:**

**CodeSandbox Domo:**

谢谢你的支持。你可以在 GitHub Repo 中找到所有代码:[deepakshrma/react-components](https://github.com/deepakshrma/react-components)

*最初发布于*[*https://blog . decipe . dev*](https://blog.decipher.dev/lazy-load-image-react-typescript)*。*