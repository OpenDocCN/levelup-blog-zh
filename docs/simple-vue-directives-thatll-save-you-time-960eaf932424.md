# 简单的 Vue 指令可以节省您的时间

> 原文：<https://levelup.gitconnected.com/simple-vue-directives-thatll-save-you-time-960eaf932424>

![](img/e41aa478a1a8d5267eee54961d54ff15.png)

由 [Djim Loic](https://unsplash.com/@loic?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在这篇文章中，我们将会看到一些 Vue 指令，它们将会节省你开发 Vue 应用的时间。

# v 热键

这个`v-hotkey`包让我们可以在 Vue 应用中添加热键处理，而不会有太多麻烦。

我们可以通过运行以下命令来安装它:

```
npm install --save v-hotkey
```

那么我们可以如下使用它:

`main.js`:

```
import Vue from "vue";
import App from "./App.vue";
import VueHotkey from "v-hotkey";Vue.use(VueHotkey);Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`:

```
<template>
  <div id="app">
    <span v-hotkey="keymap" v-show="show">Toggle me with Ctrl+Shift+H</span>
  </div>
</template><script>
export default {
  data() {
    return {
      show: true
    };
  },
  methods: {
    toggle() {
      this.show = !this.show;
    }
  },
  computed: {
    keymap() {
      return {
        "ctrl+shift+h": this.toggle
      };
    }
  }
};
</script>
```

在上面的代码中，我们定义了具有`ctrl+shift+h`属性的`keymap`计算属性。如果我们按 Ctrl+Shift+H 来显示或隐藏上面的模板文本，这将运行我们选择的方法。

在我们的例子中，我们运行`this.toggle`方法在`true`和`false`之间切换`this.show`，这被用作显示我们跨度的条件。

这个包使用 Vue 内置的事件修改器 luke `prevent`来阻止默认行为，使用`stop`来停止事件的传播。

我们可以这样做:

```
<span v-hotkey.prevent="keymap" v-show="show">Toggle me with Ctrl+Shift+H</span>
```

# v-单击-外部

`v-click-outside`指令让我们在单击某个组件外部时运行一些代码，这通常是我们想要做的事情。

没有任何附加组件，没有简单的方法可以做到这一点。因此，这将有助于我们。我们通过运行以下命令来安装它:

```
npm install --save v-click-outside
```

那么我们可以如下使用它:

```
<template>
  <div id="app">
    <button @click="show = true">Menu</button>
    <div v-show="show" v-click-outside="onClickOutside">Menu</div>
  </div>
</template><script>
export default {
  data() {
    return {
      show: true
    };
  },
  methods: {
    onClickOutside(event) {
      if (event.target.localName === "button") {
        this.show = true;
        return;
      }
      this.show = false;
    }
  }
};
</script>
```

在上面的代码中，我们有一个菜单按钮，它显示下面的 div，因为我们将`this.show`设置为`true`。然后我们有`onClickOutside`方法，如果我们没有点击按钮，这个方法被调用来设置`this.show`为`false`。

因此，只有当我们在 div 和按钮之外单击时，才会移除 div。

我们还可以传入一个配置对象，而不是事件处理程序方法，如下所示:

```
<template>
  <div id="app">
    <button [@click](http://twitter.com/click)="show = true">Menu</button>
    <div v-show="show" v-click-outside="vcoConfig">Menu</div>
  </div>
</template><script>
export default {
  data() {
    return {
      show: true,
      vcoConfig: {
        handler: this.onClickOutside,
        events: ["dblclick", "click"]
      }
    };
  },
  methods: {
    onClickOutside(event) {
      if (event.target.localName === "button") {
        this.show = true;
        return;
      }
      this.show = false;
    }
  }
};
</script>
```

在上面的代码中，我们用`data`方法返回了一个`vcoConfig`对象，这样我们就可以在模板中引用它作为`v-click-outside`指令的值。

在对象中，我们有`handler`属性，它被设置为第一个例子中使用的相同的处理程序。

最后，我们应该有和第一个例子一样的结果。

![](img/dd842c942c75213ec99d53b14637feb3.png)

亚历山大·席默克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# v-剪贴板

`v-clipboard`指令让我们只需编写几行代码就可以将文本从模板复制到剪贴板。我们可以通过运行以下命令来安装它:

```
npm install --save v-clipboard
```

那么我们可以如下使用它:

`main.js`:

```
import Vue from "vue";
import App from "./App.vue";
import Clipboard from "v-clipboard";Vue.use(Clipboard);Vue.config.productionTip = false;new Vue({
  render: h => h(App)
}).$mount("#app");
```

`App.vue`:

```
<template>
  <div id="app">
    <button v-clipboard="value">Copy to clipboard</button>
  </div>
</template><script>
export default {
  data() {
    return {
      value: "hello world"
    };
  }
};
</script>
```

我们所要做的就是在模板中包含`v-clipboard`指令，然后我们可以将`value`中的字符串集复制到剪贴板。

当复制到剪贴板成功或失败时，它还可以采用事件处理程序，如下所示:

```
<template>
  <div id="app">
    <button
      v-clipboard="value"
      v-clipboard:success="clipboardSuccessHandler"
      v-clipboard:error="clipboardErrorHandler"
    >Copy to clipboard</button>
  </div>
</template><script>
export default {
  data() {
    return {
      value: "hello world"
    };
  },
  methods: {
    clipboardSuccessHandler({ value, event }) {
      console.log("success", value);
    }, clipboardErrorHandler({ value, event }) {
      console.log("error", value);
    }
  }
};
</script>
```

我们添加了两个方法来处理复制成功或失败的情况，然后在模板中用两个指令引用它们— `v-clipboard:success` 和`v-clipboard:error`。

同样，我们可以使用`$clipboard`方法复制任何东西，如下所示:

```
<template>
  <div id="app">
    <button [@click](http://twitter.com/click)="copy">Copy to clipboard</button>
  </div>
</template><script>
export default {
  data() {
    return {
      value: "foo bar"
    };
  },
  methods: {
    copy() {
      this.$clipboard(this.value);
    }
  }
};
</script>
```

我们调用`this.$clipboard(this.value);`将`this.value`的值复制到剪贴板。

# 结论

`v-hotkey`包让我们用几行代码就能给我们的应用程序添加热键处理。

`v-click-outside`指令让我们处理当用户点击组件外部时我们想要运行代码的情况。

最后，`v-clipboard`指令是一个简单的指令，它让我们将文本复制到操作系统的剪贴板上。