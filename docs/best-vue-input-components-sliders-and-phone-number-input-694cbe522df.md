# 最佳 Vue 输入组件—滑块和电话号码输入

> 原文：<https://levelup.gitconnected.com/best-vue-input-components-sliders-and-phone-number-input-694cbe522df>

![](img/df794ecc0063c2a83641bbd641fb954b.png)

照片由[安东尼·罗伯茨](https://unsplash.com/@arcreates?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在这篇文章中，我们将看看我们可以使用的滑块和电话号码输入组件，这样我们就不必从头开始创建一个。

# Vue 圆形滑块

Vue 圆形滑块组件允许我们添加一个圆形的幻灯片。

它支持触摸，非常可定制。我们可以设置最小和最大值，以及圆的半径等。

要安装，我们只需运行:

```
npm install --save vue-circle-slider
```

那么我们可以如下使用它:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import VueCircleSlider from "vue-circle-slider";Vue.use(VueCircleSlider);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div id="app">
    <circle-slider
      v-model="val"
      :side="150"
      :min="0"
      :max="5000"
      :step-size="100"
      :circle-width-rel="20"
      :progress-width-rel="10"
      :knob-radius="10"
    ></circle-slider>
    <p>{{val}}</p>
  </div>
</template><script>
export default {
  name: "App",
  data() {
    return { val: 0 };
  }
};
</script>
```

在上面的代码中，我们有了`circle-slider`组件，通过用`VueCircleSlider`插件调用`Vue.use`方法可以得到它。

然后，我们可以将`min`和`max`设置为允许值范围的最小值和最大值。

我们还有`circle-width-rel`和`progress-width-rel`来指定相对的圆宽度，这是通过公式`(side/2) / circleWidthRel.`计算的

然后`progress-width-rel`号如果相对进度曲线宽度，则由`(side/2) / progressWidthRel`计算。

`side`以像素为单位表示 SVG 正方形的大小。

`step-size`是数值之间的差距。

`circle-color`是圆的颜色，可以改变。`progress-color`是进度曲线的颜色。

`knob-color`是旋钮的颜色，也就是进度曲线上的点，

`knob-radius-rel`是相对旋钮半径，由`(side/2) / knobRadiusRel`计算。

p 元素显示通过滑动圆形滑块设置的设置值，所以当我们玩滑块时，我们将看到 p 标签中显示的值。

# 电话输入

`vue-tel-input`包为我们提供了一个组件，让我们只输入一个电话号码。

我们可以从下拉列表中选择国家前缀，并添加一个标签来显示输入电话号码的说明。

要安装它，我们可以运行以下命令:

```
npm i --save vue-tel-input
```

那么我们可以如下使用它:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import VueTelInput from "vue-tel-input";Vue.use(VueTelInput, []);
Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div id="app">
    <vue-tel-input v-model="phone"></vue-tel-input>
  </div>
</template><script>
import { VueTelInput } from "vue-tel-input";export default {
  name: "App",
  components: {
    VueTelInput
  },
  data() {
    return { phone: "" };
  }
};
</script>
```

在上面的代码中，我们有`vue-tel-input`组件。然后我们会看到一个电话号码输入。

我们可以选择输入控件左侧的国家来选择国家代码。

然后，我们可以在主输入框中输入我们的手机。

我们可以通过自己的方法监听`country-changed`事件来获取国家代码，如下所示:

```
<template>
  <div id="app">
    <vue-tel-input [@country](http://twitter.com/country)-changed="onCountryChange" v-model="phone"></vue-tel-input>
    <p>{{countryCode}} {{phone}}</p>
  </div>
</template><script>
import { VueTelInput } from "vue-tel-input";export default {
  name: "App",
  components: {
    VueTelInput
  },
  data() {
    return { phone: "", countryCode: "" };
  },
  methods: {
    onCountryChange(val) {
      this.countryCode = val.dialCode;
    }
  }
};
</script>
```

`val`的`dialCode`属性有`countryCode`，这样我们就可以随心所欲地使用它。

在上面的代码中，我们只是将它设置为我们的`countryCode`属性，并在模板上显示它。

它还有许多其他选项，我们可以通过传递带有我们自己选项的道具来调整改变行为。

我们可以改变输入字段的 ID，是否在国家下拉列表中显示旗帜，输入字段的占位符等等。

它还集成了`vue-form-generator`封装。

![](img/7cfc7d892fe5347370e4dec1c7a46777.png)

由[paweczerwiński](https://unsplash.com/@pawel_czerwinski?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

Vue 圆形滑块允许我们创建一个圆形滑块控件，用户可以使用它来设置数值。

如果我们想要一个带有国家代码下拉菜单的电话号码输入，我们可以使用`vue-tel-input`包，它有很多选项来调整它的行为和外观。