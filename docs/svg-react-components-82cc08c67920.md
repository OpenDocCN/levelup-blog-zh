# SVG 反应组件

> 原文：<https://levelup.gitconnected.com/svg-react-components-82cc08c67920>

![](img/9e88d0d716901b781f8df887b35ad818.png)

由 [Ferenc Almasi](https://unsplash.com/@flowforfrank?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

为了准备我将要从事的项目，我决定看一看 SVG 库。我偶然听到一个叫 Dmitry Baranovskiy 的坏脾气家伙的演讲，叫做[你不知道 SVG](https://youtu.be/SeLOt_BRAqc) 。除了了解到他真的不喜欢潮人，我还发现他是 Raphael 的创造者，Raphael 是用于 SVG 的最流行的 javascript 库。几年前，他创建了一个名为 [Snap](http://snapsvg.io/about/) 的新图书馆，以`snapsvg-cjs`的名字托管在 NPM。这是我用来在 react 中创建项目的包。

# 入门指南

如果你想继续编码，首先确保你已经配置了[react](https://www.codecademy.com/articles/react-setup-i)。我正在使用一个叫做`create-react-app`的[快捷方式](https://github.com/facebookincubator/create-react-app)，它将更快地构建我的项目。在终端中运行`create-react-app`,后跟你的项目名称。`cd`进入文件夹然后运行`npm install snapsvg-cjs`。现在运行`npm start`开始在浏览器中运行 react 应用程序。希望此时你能看到 react 的标志。

在这篇文章中，我将使用 Snap.svg 库制作一个自定义滑块。下面是一个代码笔概述了预期的结果。

Snap 提供的主要功能有:

```
Snap(...) 
//Identifies or creates a new svg element to work with as a snap objectPaper.circle( x, y, r )
//Creates a circle object which can be appended to the snap object
//The x and y coordinates are relative to the parent element.Paper.line( x1, y1, x2, y2 )
//Create a line from the first to second (x,y) coordinates relative to the parent containerElement.attr(...) 
//Sets attributes for any elements within the snap object. This function takes in an argument of an object with key value pairs corresponding to the different attributes you can apply to any given element.Element.limitDrag() is a custom function which is probably not the nicest implementation of this code, but it works.Element.drag(onmove, onstart, onend, [mcontext], [scontext], [econtext]) 
//This is called inside of the Element.limitDrag() function in the code pen above. It takes three callback functions that are required.
```

关于如何为 Element.drag()编写回调函数的更详细的例子，请看这个演示。对于这个特定的滑块，我必须向 Snap 中的 Element.prototype 添加另一个函数来停止滑块。幸运的是，我能够在快照文档中找到一些限制拖拽的代码。

# 创建组件

在名为`src`的文件夹中，我创建了另一个名为`animations`的文件夹。在这里，我将把我所有的单个 SVG 组件从。

在我的动画文件夹中，我创建了一个名为`Slider.js`的新文件，在这里我将创建我的组件。首先我导入 React 和 Snap:

```
import React from 'react';
import Snap from 'snapsvg-cjs';
```

接下来，我为这个滑块创建一个智能组件。起初，我试图为这个滑块创建一个哑组件，因为我不需要对它的状态做任何事情，但我稍后将回到为什么我选择使用智能组件。

```
import React from 'react';
import Snap from 'snapsvg-cjs';class Slider extends React.Component{ let s = Snap('#svg') render () {
    return(
      <svg id='svg'/>
    )
}
export default Slider
```

因此，Snap()函数的工作方式是通过 id 找到一个 SVG 元素或者创建一个新的元素。一旦找到元素，它就创建一个 snap 对象，可以引用该对象来更改 SVG 元素。最初我试着只返回' s ',但是 React 总是给我一个错误，说组件不能返回一个对象。相反，我决定返回一个 SVG。我遇到的下一个问题是，当我试图将元素应用于' s '时，React 一直告诉我' s '未定义。结果是我的 Snap 相关代码在 React 组件呈现之前就已经运行了。

为了解决这个问题，我在 React 的生命周期方法`componentDidMount()`中包装了所有的 Snap 元素。这将阻止在此方法中运行代码，直到组件呈现出来并可以使用。现在，在 Snap 试图找到所述 SVG 之前，页面上有一个具有正确 id 的 SVG。

```
import React from 'react';
import Snap from 'snapsvg-cjs';//insert Element.prototype.limitDrag function hereclass Slider extends React.Component{ componentDidMount() {
    var s = Snap("#svg" + this.props.keyId.toString()) s.line(30, 30, this.props.width-30, 30).attr({stroke: '#000'}) var myCircle2 = s.circle(30,30,20) myCircle.attr({ stroke: '#123456', 'strokeWidth': 3,
       fill: this.props.fill, 'opacity': 0.2 }) myCircle2.limitDrag({ x: 0, y: 0, minx: 0, miny: 0,
       maxx: this.props.width-20, maxy: 0 })
   }render () {
     const idKey = "svg" + this.props.keyId.toString()
    return (
      <svg style={this.props.style} 
            width={this.props.width} height="60" id={idKey}/>
    )
  }
}export default Slider
```

对于这个组件，我在文件的顶部导入了 Snap 库，以便该类可以使用它。我认为这个决定是有意义的，因为这个库的使用是特定于这个组件的。为了保持这个组件的灵活性，我添加了一些可以在调用时传递给它的道具。让我们看看应用程序容器:

```
import React, { Component } from 'react';
import Slider from './animations/Slider'class App extends Component {render() {
    return (
        <div>
          <Slider keyId={1} width={400} fill={'green'}/>
          <Slider keyId={2} width={400} fill={'red'}/>
          <Slider keyId={3} width={400} fill={'yellow'}/>
        </div>
    );
  }
}export default App;
```

你会注意到在顶部，我从我的动画文件夹中导入滑块。当 slider 组件被调用时，我传递给它三个对每个 Slider 来说是唯一的道具。您还会注意到，每个滑块都有一个唯一的 keyId 属性。这用于唯一地标识由组件呈现的 SCG，以便 Snap()知道在哪里放置每个元素。

# 滑的开心！