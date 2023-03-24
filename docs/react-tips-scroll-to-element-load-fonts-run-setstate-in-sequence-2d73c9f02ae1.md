# 反应提示-滚动到元素，加载字体，按顺序运行设置状态

> 原文：<https://levelup.gitconnected.com/react-tips-scroll-to-element-load-fonts-run-setstate-in-sequence-2d73c9f02ae1>

![](img/02e417d1fc28a0d94b88386c8ff43236.png)

[兰迪·拜恩](https://unsplash.com/@arbayne?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我们将了解一些编写更好的反应应用程序的技巧。

# 在反应中从外部访问组件方法

如果我们给组件分配一个引用，我们可以从外部访问组件方法。

例如，我们对组件进行分类，我们可以编写:

```
const ref = React.createRef()const parent = (<div>
  <Child ref={ref} /> 
  <button onClick={e => console.log(ref.current)}>click me</button>
</div>)
```

我们使用`React.createRef`方法创建参考。

然后我们将引用传递给`Child`组件。

然后，我们可以像在`onClick`回调中那样得到具有`ref.current`属性的组件。

# 滚动到反应组件中的元素

我们可以使用带有参考的`window.scrollTo`方法滚动到反应组件中的一个元素。

例如，在函数组件中，我们可以编写:

```
import React, { useRef } from 'react'const scrollToRef = (ref) => window.scrollTo(0, ref.current.offsetTop);const App = () => {
  const myRef = useRef(null)
  const scrollTo = () => scrollToRef(myRef)
  return (
    <> 
      <div ref={myRef}>scroll to me</div> 
      <button onClick={scrollTo}>click me to scroll</button> 
    </>
  )
}
```

我们创建`scrollToRef`函数来调用`scrollTo`。

我们想滚动到元素的顶部，所以获取`ref.current.offsetTop`来获取它，并将其作为第二个参数。

然后在`App`中，我们调用`useRef`钩子来创建我们的参考。

然后我们可以把我们的引用传递给 div 来分配它。

一旦我们这样做了，我们添加`scrollTo`方法来滚动到分配给我们的参考的 div。

有了类组件，我们可以做同样的事情。

例如，我们可以写:

```
class App extends Component {
  constructor(props) {
    super(props)
    this.myRef = React.createRef()  
  } render() {
    return <div ref={this.myRef}></div> 
  } scrollToRef = () => window.scrollTo(0, this.myRef.current.offsetTop)   
}
```

我们用`React.createRef`创建我们的参考，然后用`scrollToRef`方法滚动到它。

我们得到了具有`current`属性的元素。

滚动逻辑是相同的。

如果我们使用 ref 回调来分配我们的 ref，那么我们可以写:

```
class App extends Component { render() {
    return <div ref={(ref) => this.myRef = ref}></div>
  } scrollToMyRef = () => window.scrollTo(0, this.myRef.offsetTop)
}
```

我们将回调传递给`ref`道具，而不是使用`createRef`来创建我们的参考。

然后我们得到分配给引用的元素，而没有`current`属性。

# 在 setState 完成更新后运行一个函数

我们可以通过将回调传入`setState`的第二个参数，在`setState`完成更新后运行一个函数。

例如，我们可以写:

```
const promiseState = async state => new Promise(resolve => this.setState(state, resolve));
```

我们用`async`函数创建了一个承诺，该函数以`resolve`作为第二个参数。

然后我们可以写道:

```
promiseState({...})
  .then(() => promiseState({
    //...
  })
  .then(() => {
    //...
    return promiseState({
      //...
    });
  })
  .then(() => {
    //...
  });
```

然后我们就可以想打多少次`setState`就打多少次。

# 向基于 create-react-app 的项目添加字体

要将字体添加到 create-react-app 项目中，我们可以将其放入 CSS 代码中。

例如，我们可以写:

```
@font-face {
  font-family: 'SomeFont';
  src: local('SomeFont'), url(./fonts/SomeFont.woff) format('woff');
}
```

在`index.css`里。

然后我们可以通过编写以下内容来导入它:

```
import './index.css';
```

我们用`font-face`规则定义了一种新字体。

`src`有字体的位置。

`font-family`有字体名。

我们可以像用 Webpack 导入模块一样导入 CSS。

我们也可以把字体放在`index.html`里。

例如，我们可以写:

```
<link href="https://fonts.googleapis.com/css?family=Montserrat" rel="stylesheet">
```

或者写:

```
<style>
    @import url('https://fonts.googleapis.com/css?family=Montserrat');
</style>
```

另一个解决方案是使用 webfontloader 包。

要安装它，我们运行:

```
yarn add webfontloader
```

或者

```
npm install webfontloader --save
```

然后我们可以写:

```
import WebFont from 'webfontloader';WebFont.load({
   google: {
     families: ['Open Sans:300,400,700', 'sans-serif']
   }
});
```

来加载我们想要的字体。

![](img/1f31975009aabb9318b28cce1e4aacad.png)

照片由 [Yogendra Singh](https://unsplash.com/@yogendras31?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

有多种方法可以将字体加载到我们的 create-react-app 项目中。

我们可以通过给组件分配一个 ref 来从组件外部访问组件方法，然后我们可以调用这些方法。

这适用于类组件。

有多种方法可以滚动到一个元素。

如果我们将一个函数传递给它的第二个参数，我们可以按顺序运行`setState`。