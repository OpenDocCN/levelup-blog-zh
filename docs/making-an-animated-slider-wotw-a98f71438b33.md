# 建立一个动画滑块-WotW

> 原文：<https://levelup.gitconnected.com/making-an-animated-slider-wotw-a98f71438b33>

![](img/14d032e7b344790099e3c7b9ada61115.png)

欢迎来到“每周小部件”系列，在这里我拍摄了令人敬畏的 UI/UX 组件的 gif 或视频，并用代码将它们赋予生命。

> 查看本周文章中的所有[小部件，并关注 gitconnected，确保您不会错过任何即将到来的 JavaScript 和 CSS 教程](https://levelup.gitconnected.com/wotw/home)

这一次我们将创建一个温度滑块，虽然它可以用于任何事情。灵感来自于这个由[ramykhufash](https://uimovement.com/user/10/ramykhuffash/)创作的[投稿](https://uimovement.com/ui/5774/smart-home/)，看起来是这样的:

![](img/c203c55eeca784aae91be32d54d0c0b1.png)

## 准备

对于今天的小部件，我们将使用 [Vue.js](https://vuejs.org/) ，对于一些动画，我们将使用 [TweenMax](https://greensock.com/tweenmax) 。我们还需要一个温度图标，所以我们将使用从[字体真棒](https://fontawesome.com/)的一个。

如果你想继续，你可以派生这个已经有依赖关系的 [codepen 模板](https://codepen.io/ederdiaz/pen/gzyxWv)。

## 匹配设计

这个小部件的 HTML 标记比通常的要复杂一些，所以这一次我将使用 HTML + CSS 把它分成几个部分，直到我们符合最初的设计。

让我们从设置上下两部分开始——上部分包含数字，下部分包含滑块控件。

```
<div id="app" class="main-container">
  <div class="upper-container">

  </div>
  <div class="lower-container">

  </div>
</div>
```

在对它们进行样式化之前，我们需要在`body`中添加几个主要的 CSS 属性。

```
body {
  margin: 0;
  color: white;
  font-family: Arial, Helvetica, sans-serif;
}
```

我们将边距设置为`0`，以避免`main-container`周围出现空白。这里还设置了`color`和`font-family`，以避免在其他元素中重复出现。

现在我们将使用`CSS grid`属性将屏幕分成两部分。上半部分需要取垂直高度的`3/4`，我们可以用`fr`来实现。

```
.main-container {
  display: grid;
  grid-template-columns: 1fr;
  grid-template-rows: 3fr 1fr;
  height: 100vh;
}
```

注意`height`属性中的`100vh`值，它允许我们垂直填充屏幕，即使我们的 div 没有任何内容。

现在只需要给这些部分添加一个背景色。对于上面一个，我们将使用梯度:

```
.upper-container {
  position: relative;
  background: linear-gradient(to bottom right, #5564C2, #3A2E8D);
}
.lower-container {
  background-color: #12132C;
}
```

在`upper-container`中设置的`position: relative`属性将在我们托盘定位其内部元素时使用。

![](img/c63bb127051e994869c93d6d7ea34edf.png)

我们才刚开始热身。

上半部分的数字似乎是合乎逻辑的下一步。

```
<!-- inside .upper-container -->
    <h2 class="temperature-text">10</h2>
```

这将是显示当前温度的大数字，让我们使用一些 CSS 来更好地定位它:

```
.temperature-text {
  position: absolute;
  bottom: 150px;
  font-size: 100px;
  width: 100%;
  text-align: center;
  user-select: none;
}
```

`user-select: none`属性应该帮助我们避免在与滑块交互时选择文本。

在添加下面显示的数字之前，让我们用一些数据启动 Vue 实例，以帮助我们避免重复不必要的标记元素:

```
new Vue({
  el: '#app',
  data: {
    temperatureGrades: [10, 15, 20, 25, 30]
  }
})
```

现在我们可以使用`temperatureGrades`数组来显示设计中的元素:

```
<!-- just after .temperature-text -->
    <div class="temperature-graduation">
      <div class="temperature-element" 
           v-for="el in temperatureGrades" 
           :key="el">
        <span class="temperature-element-number">{{el}}</span><br>
        <span class="temperature-element-line">|</span>
      </div>
    </div>
```

请注意，我们使用一个`|`字符来渲染每个数字，我们可以用它来使它们看起来像一个“尺子”。

对于我们需要居中文本的数字和行，我们将在`temperature-element`规则内这样做。我们还将使元素成为`inline-blocks`，这样它们就可以彼此相邻。最后，`|`字符需要变小，`font-size`会处理好这一点:

```
.temperature-element {
  text-align: center;
  display: inline-block;
  width: 40px;
  margin: 0 10px 0 10px;
  opacity: 0.7;
}
.temperature-element-line {
  font-size: 7px;
}
```

检查`.temperature-graduation`元素，我们可以看到它的宽度是 300 像素。为了使其居中，我们可以通过以下方式使用计算值:

```
.temperature-graduation {
  position: absolute;
  left: calc(50% - 150px); // subtracting half the width to center
  bottom: 25px;
  user-select: none;
}
```

我们还设置了`bottom`属性，使其显示在下半部分的正上方。

![](img/3eab1bd3db231a34e3bc2336739ed72e.png)

## 滑块

上半部分准备好了，现在我们将添加滑块控件。这个按钮很简单，我们只需要一个带有图标的 div:

```
<!-- inside .lower-container -->
    <div class="slider-container">
      <div class="slider-button">
        <i class="fas fa-thermometer-empty slider-icon"></i>
      </div>
    </div>
```

现在让我们来设计按钮的样式，下面的 CSS 代码大部分都是手工“调整”的值，以便能够将元素放置在所需的位置。

```
.slider-container {
  width: 150px;
  height: 80px;
  margin-top: -30px;
  margin-left: calc(50% - 187px);
  position: relative;
}
.slider-button {
  position: absolute;
  left: 42px;
  top: 5px;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background-color: #2724A2;

  cursor: grab;
  cursor: -webkit-grab; 
  cursor: -moz-grab;
}

.slider-icon {
  margin-top: 16px;  
  margin-left: 21px;  
  color: white;
}
```

按钮内的`grab`值会在光标悬停时将光标变成一只手。

滑块现在只是缺少了一个“波浪”形状，起初我试图通过使用`border-radius`值和旋转`div`来实现，但遗憾的是它与设计不匹配。我最后做的是一个`SVG`图形，看起来像这样:

![](img/c73eb9402c7d7cc0c96972afeae7e428.png)

绝对不是蛇里面的大象

这个形状的代码是:

```
<!-- inside .slider-container -->
<svg width="150" height="30" viewBox="0 0 150 30" fill="none" ae lg" href="http://www.w3.org/2000/svg" rel="noopener ugc nofollow" target="_blank">http://www.w3.org/2000/svg">
<path d="M74.3132 0C47.0043 2.44032e-05 50.175 30 7.9179 30H144.27C99.4571 30 101.622 -2.44032e-05 74.3132 0Z" transform="translate(-7.38794 0.5)" fill="#12132C"/>
</svg>
```

![](img/f6e80a89aeda7a9c1e277ab3e470c98b.png)

有点困难，但我们已经准备好了设计。

## 互动

到目前为止，这个小部件的交互中最引人注目的是拖放滑块。我们以前在制作[卡片滑块](http://ederdiaz.com/blog/2018/04/18/animated-card-slider-with-vue-gsap/)时已经这样做过了，所以我将遵循类似的方法:

```
// inside data
    dragging: false,
    initialMouseX: 0,
    sliderX: 0,
    initialSliderX: 0
```

这些数据属性将帮助我们跟踪用户何时开始/停止拖动、鼠标和滑块位置。

当用户交互时，以下方法将初始化这些变量:

```
// after data
  methods: {
    startDrag (e) {
      this.dragging = true
      this.initialMouseX = e.pageX
      this.initialSliderX = this.sliderX
    },
    stopDrag () {
      this.dragging = false
    },
    mouseMoving (e) {
      if(this.dragging) {
        // TODO move the slider        
      }
    }
  }
```

现在让我们将它们绑定到模板上

```
<div id="app" class="main-container"
    @mousemove="mouseMoving"
    @mouseUp="stopDrag">
      <!-- ... inside .slider-container
        <div class="slider-button" 
             @mouseDown="startDrag">
```

你可能已经注意到`@mouseDown`动作被设置在滑块按钮中，但是`@mouseMove`和`@mouseUp`在主 div 的层次上。

这背后的原因是，用户将通过按下滑块按钮开始，但当移动光标时，他们通常会在滑块轨道之外，如果他们在按钮之外放开鼠标，它将不会被跟踪，并将导致按钮跟随你，直到你再次单击它。

现在让我们用一个算法来填充`mouseMoving`方法，该算法将把`sliderX`属性设置到期望的位置。我们需要为滑块声明一些约束来匹配我们之前做的标尺。

```
// before the Vue instance
const sliderMinX = 0
const sliderMaxX = 240

  // inside mouseMoving method
    // replace the "TODO" line with this:
    const dragAmount = e.pageX - this.initialMouseX
    const targetX = this.initialSliderX + dragAmount

    // keep slider inside limits
    this.sliderX = Math.max(Math.min(targetX, sliderMaxX), sliderMinX)

  // after methods
  computed: {
    sliderStyle () {
      return `transform: translate3d(${this.sliderX}px,0,0)`
    }
  }
```

正如您可能已经猜到的，计算属性`sliderStyle`存储了滑块的位置，我们只需要将它绑定到`.slider-container`:

```
<div class="slider-container" :style="sliderStyle">
```

我们几乎有一个工作的滑块控件，但它缺少一个重要的东西，跟踪滑块值。这听起来可能很复杂，但是我们可以用一个计算属性来计算这个值，因为我们已经知道了`sliderX`的位置:

```
// inside computed    
    currentTemperature () {
      const tempRangeStart = 10
      const tempRange = 20 // from 10 - 30
      return (this.sliderX / sliderMaxX * tempRange ) + tempRangeStart
    }
```

您可以通过在`.temperature-text`元素中呈现它来判断它是否工作:

```
<h2 class="temperature-text">{{currentTemperature}}</h2>
```

![](img/ba49b692ee6640ac69cc39809af63f6b.png)

现在的问题是它呈现的是浮点数。我们可以使用过滤器来避免这种情况:

```
// after data
  filters: {
    round (num) {
      return Math.round(num)
    }
  },
```

现在我们可以像这样使用过滤器:

```
<h2 class="temperature-text">{{currentTemperature | round}}</h2>
```

## 最后的润色

我们可以就此收工，让小部件像这样，但它仍然缺少一些细节。当温度超过 25 度时，背景应该改变颜色，标尺的数字也应该像波浪一样移动。

作为背景，我们将在顶部声明几个常数和一些新的数据属性:

```
const coldGradient = {start: '#5564C2', end: '#3A2E8D'}
const hotGradient = {start:'#F0AE4B', end: '#9B4D1B'}

// inside Vue
    // inside data
      gradientStart: coldGradient.start,
      gradientEnd: coldGradient.end

    //inside computed
      bgStyle () {
        return `background: linear-gradient(to bottom right, ${this.gradientStart}, ${this.gradientEnd});`
      }
```

它们将保存渐变背景所需的颜色。每次`gradientStart`和`gradientEnd`改变时，`bgStyle`计算属性将生成背景。让我们将它绑定到相应的 HTML 元素:

```
<div class="upper-container" :style="bgStyle">
```

现在它看起来应该是一样的，但是当我们在`mouseMoving`方法中添加动画规则时，情况会发生变化:

```
// set bg color
    let targetGradient = coldGradient
    if (this.currentTemperature >= 25) {
      targetGradient = hotGradient
    }

    if(this.gradientStart !== targetGradient.start) {
      // gradient changed
      TweenLite.to(this, 0.7, {
        'gradientStart': targetGradient.start,
        'gradientEnd': targetGradient.end
      }) 
    }
```

我们正在做的是，当温度变化到 25 度或更高时，从冷到热改变梯度值。过渡是用 TweenLite 而不是 CSS 过渡来完成的，因为它们只适用于纯色。

最后，如果滑块靠近标尺元素，我们需要改变它们的`Y`位置。

```
<div class="temperature-element" v-for="el in temperatureGrades"
           :style="tempElementStyle(el)"
           :key="el">
```

类似于上面的部分，我们将通过一个方法绑定要改变的样式，这个方法将接收每个标尺的值。现在只需要做一些数学运算来计算距离并生成一些 CSS 变换道具:

```
// inside methods
    tempElementStyle (tempNumber) {
      const nearDistance = 3
      const liftDistance = 12

      // lifts up the element when the current temperature is near it
      const diff = Math.abs(this.currentTemperature - tempNumber)
      const distY = (diff/nearDistance) - 1

      // constrain the distance so that the element doesn't go to the bottom
      const elementY = Math.min(distY*liftDistance, 0)
      return `transform: translate3d(0, ${elementY}px, 0)`
    }
```

而现在最后的结果！

这就是本周的**小部件。下周见，关注 [gitconnected](https://levelup.gitconnected.com) 获取每周小工具！**

如果你渴望更多，你可以查看其他 WotW:
— [3D 面对小工具](/making-a-3d-facing-widget-8ab51a9eb573)
— [卡片悬停动画](/cards-hover-animation-wotw-7d1304f16ec6)
— [滚动卡片列表](/making-a-scrolling-card-list-wotw-bcd5e31fbcc5)

另外，如果你想看下周的某个小部件，可以在评论区发表。

*最初发表于*[*Eder díaz*](http://ederdiaz.com/blog/2018/07/11/making-an-animated-slider-wotw/)*。*

[![](img/439094b9a664ef0239afbc4565c6ca49.png)](https://levelup.gitconnected.com/)