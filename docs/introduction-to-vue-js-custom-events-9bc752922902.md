# Vue.js 自定义事件介绍

> 原文：<https://levelup.gitconnected.com/introduction-to-vue-js-custom-events-9bc752922902>

![](img/dad784a6136695dfd3df80b7feda8a12.png)

由[安东尼·坎廷](https://unsplash.com/@arizonanthony?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以利用它来开发交互式前端应用。

在本文中，我们将了解如何定义和发出自定义事件，进行双向绑定，以及侦听本机事件。

# 事件名称

Vue 不会自动转换事件名称。发出的事件的名称必须与用于侦听事件的名称相匹配。

例如，如果一个组件发出一个`someEvent`事件:

```
this.$emit('someEvent')
```

然后我们必须监听父组件中的`someEvent`:

```
<child v-on:someEvent="doSomething"></child>
```

在 JavaScript 中，事件名永远不会被用作变量或属性名，所以没有理由使用 camelCase 或 PascalCase。`v-on`会自动将所有内容转换成小写，因为 HTML 不区分大小写。

所以`v-on:someEvent`会变成`v-on:someevent`。

因此，总是使用烤肉串作为事件名称是个好主意。

# 定制组件 v 模型

默认情况下，组件上的`v-model`使用`value`作为道具，使用`input`作为事件，但是一些输入类型，比如复选框和单选按钮，可能会出于不同的目的使用`value`属性。

要更改属性名，我们可以设置`model`选项以避免冲突。

我们可以编写如下代码:

`src/index.js`:

```
Vue.component("our-input", {
  inheritAttrs: false,
  model: {
    prop: "val",
    event: "inputted"
  },
  template: `    
    <input        
      v-bind:value="val"
      v-on:input="$emit('inputted', $event.target.value)"
    >    
  `
});
new Vue({
  el: "#app",
  data: {
    msg: ""
  }
});
```

`index.html`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>App</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head>
  <body>
    <div id="app">
      <our-input v-model="msg"></our-input>
      <p>{{msg}}</p>
    </div>
    <script src="src/index.js"></script>
  </body>
</html>
```

在上面的代码中，我们更改了输入值的属性名和`model`对象中的事件名，如下所示:

```
model: {
  prop: "val",
  event: "inputted"
}
```

然后在`our-input`的`input`中，我们写道:

```
<input        
  v-bind:value="val"
  v-on:input="$emit('inputted', $event.target.value)"
>
```

发出的是`inputted`事件，而不是通常的`input`事件，我们将输入值绑定到`val`而不是默认的`value`。

然后在`index.html`中，我们照常使用`v-model`:

```
<our-input v-model="msg"></our-input>
```

最后，当我们输入数据时，我们仍然看到它显示在输入下方。

这个特性从 Vue 2.2.0 开始就有了。

# 将本机事件绑定到组件

我们可以通过将`.native`修饰符应用到`v-on`来绑定到本地事件。然而，如果我们的元素四处移动，那么它会无声无息地破裂。

Vue 提供了一个`$listeners`属性，包含一个组件上使用的监听器对象。

它看起来像下面这样:

```
{   
  focus(event) { /* ... */ }   
  input(value) { /* ... */ }, 
}
```

我们可以用`v-on='$listeners'`代码将组件上的监听器转发给特定的子组件。

例如，我们可以编写以下代码，将所有侦听器合并到一个`inputListeners`计算属性中，然后使用如下的`v-on`指令:

`src/index.js`:

```
Vue.component("our-input", {
  inheritAttrs: false,
  props: ["value"],
  computed: {
    inputListeners() {
      var vm = this;
      return Object.assign({}, this.$listeners, {
        input(event) {
          vm.$emit("input", event.target.value);
        }
      });
    }
  },
  template: `          
    <input
      v-bind="$attrs"
      v-bind:value="value"
      v-on="inputListeners"
    >    
  `
});new Vue({
  el: "#app",
  data: {
    msg: ""
  }
});
```

`index.html`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>App</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head>
  <body>
    <div id="app">
      <our-input v-model="msg"></our-input>
      <p>{{msg}}</p>
    </div>
    <script src="src/index.js"></script>
  </body>
</html>
```

在`src/index.js`中，我们将`input`事件处理程序合并到了`this.$listeners`对象中，该对象拥有所有的监听器。

然后，当我们在输入中键入一些内容时，输入的数据会显示在它的下方。

![](img/d8439df262d6056b3c4e5bdbb4917d0c.png)

照片由 [Luc Dobigeon](https://unsplash.com/@luc_dobigeon?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 。同步修改器

为了在我们的代码中澄清我们正在进行双向绑定，建议我们发出前缀为`update:`的事件，因此如果我们除了传入 props 之外还在更新一个值，我们可以编写以下代码:

`src/index.js`:

```
Vue.component("child", {
  inheritAttrs: false,
  props: ["value"],
  template: `          
    <button v-on:click='$emit("update:value", Math.random())'>Generate Random Number</button>   
  `
});new Vue({
  el: "#app",
  data: {
    value: 1
  }
});
```

`index.html`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>App</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head>
  <body>
    <div id="app">
      <child :value="value" v-on:update:value="value = $event"></child>
      <p>{{value}}</p>
    </div>
    <script src="src/index.js"></script>
  </body>
</html>
```

在`index.html`中，我们通过编写`:value=”value”`将`value`属性传入`child`来明确我们正在将一个值从父节点传入`child`。

此外，我们还有:

```
v-on:update:value="value = $event"
```

为了让我们清楚，我们正在用`$event`对象更新`value`的值，该对象具有由`update:value`事件发出的数字。

从 Vue 2.3.0 开始，我们可以通过使用`.sync`修饰符将两个指令合并成一个来缩短这个过程。

我们可以将上面的代码缩短如下:

`src/index.js`:

```
Vue.component("child", {
  inheritAttrs: false,
  props: ["value"],
  template: `          
    <button v-on:click='$emit("update:value", Math.random())'>Generate Random Number</button>   
  `
});new Vue({
  el: "#app",
  data: {
    value: 1
  }
});
```

`index.html`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>App</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head>
  <body>
    <div id="app">
      <child v-bind:value.sync="value"></child>
      <p>{{value}}</p>
    </div>
    <script src="src/index.js"></script>
  </body>
</html>
```

在上面的代码中，我们有:

```
<child v-bind:value.sync="value"></child>
```

这与以下内容相同:

```
<child :value="value" v-on:update:value="value = $event"></child>
```

`src/index.js`保持不变。

带`.sync`修饰符的`v-bind`不适用于表达式。我们也可以用它一次设置多个道具。

我们也可以将`.sync`和一个宾语一起使用，如下所示:

`src/index.js`:

```
Vue.component("child", {
  inheritAttrs: false,
  props: ["value"],
  template: `          
    <button v-on:click='$emit("update:value", { firstName: "Joe", lastName: "Smith" })'>Generate Person</button>   
  `
});new Vue({
  el: "#app",
  data: {
    person: {}
  }
});
```

`index.html`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>App</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head>
  <body>
    <div id="app">
      <child v-bind:value.sync="person"></child>
      <p>{{person.firstName}} {{person.lastName}}</p>
    </div>
    <script src="src/index.js"></script>
  </body>
</html>
```

然后，当我们单击 Generate Person 按钮时，会显示出`Joe Smith`。

`v-bind.sync`仅适用于对象名称。它不适用于对象文字。

# 结论

当我们想在自定义组件上使用`v-model`但不想使用默认名称时，我们可以使用`model`来更改属性和事件名称。

要使用本地侦听器，我们可以使用`this.$listeners`来获取所有可用的侦听器，然后将它们与我们自己的事件处理程序合并。

最后，我们可以使用`.sync`修饰符通过一个简写指令进行双向数据绑定。