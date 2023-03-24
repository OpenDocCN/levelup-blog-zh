# 解决常见的 Vue 问题

> 原文：<https://levelup.gitconnected.com/solving-common-vue-problems-8018095804b9>

![](img/b4d4e2b6d63bde016c42213474a570fc.png)

[马丁·莫雷诺](https://unsplash.com/@memoreno?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Vue.js 让开发前端应用变得简单。然而，我们仍有可能遇到问题。

在这篇文章中，我们将看看一些常见的问题，并看看如何解决它们。

# 这是未定义的

我们偶尔会看到“无法读取未定义的属性‘prop’”错误。

这是因为我们只能在组件对象的常规方法中引用`this`

例如，我们可以写:

```
mounted() {
  console.log(this);
}
```

或者:

```
new Vue({
  el:'#app',
  created(){
    console.log(this)
  }
});
```

我们总是在方法中使用常规函数来访问`this`。箭头函数没有绑定到`this`，我们无法访问其中`this`的正确值。

# 属性内插值已被删除错误

我们可以通过在模板中使用`v-bind`或`:`语法来解决这个错误。我们不能再使用插值向 props 传递值了。

例如，我们不能写:

```
<div id="purchase-{{ item.id }}"></div>
```

相反，我们必须写:

```
<div :id="`purchase-${item.id}`"></div>
```

或者:

```
<div v-bind:id="`purchase-${item.id}`"></div>
```

冒号是`v-bind`的简写。

# 从子组件更新父数据

为了从子组件更新父数据，我们可以调用`this.$emit`从子组件发出一个事件，并监听父组件中的事件。

例如，我们可以写:“

```
Vue.component('child', {
  template: '#child',

  props: ['value'],
  methods: {
    updateValue(value) {
      this.$emit('input', value);
    }
  }
});
```

用值`value`发出`input`事件。

然后我们可以监听`input`事件，并通过编写以下内容来设置父状态的值:

```
<child @input="parentValue = $event"></child>
```

`$event`是来自`child`的`value`的值，并且`parentValue`被设置为`$event`的值。

因此`value`的值被设置为`parentValue`的值。如果我们的组件发出一个`input`事件并接受一个`value`道具。然后我们可以将两者与`v-model`指令结合起来。

例如，不要写:

```
<child @input="parentValue = $event" :value='parentValue'></child>
```

我们可以写:

```
<child v-model='parentValue'></child>
```

# 箭头函数不能用作计算属性函数

我们不能使用对计算属性无效的箭头函数，因为它们没有绑定到`this`的正确值。

为了在计算的属性函数中获得正确的值`this`,我们必须使用常规函数。

例如，不要写:

```
computed:{
  switchRed: () => {
    return { red: this.turnRed }
  },
}
```

我们写道:

```
computed:{
  switchRed(){
    return { red: this.turnRed }
  },
}
```

# 向 Vue 组件添加外部 JS 脚本

我们可以通过使用`Vue.loadScript`方法将外部脚本添加到我们的 Vue 组件中。

例如，我们可以写:

```
Vue.loadScript("https://maps.googleapis.com/maps/api/js")
```

# 让 VueJS 和 jQuery 玩得更好

我们应该只在指令中使用 jQuery 来操作 DOM。

在其他地方，我们使用 Vue 的内置功能来呈现我们的页面。

例如，我们可以这样写一个指令:

```
Vue.directive('focus', {
  inserted(el) {
    $(el).focus()
  }
})
```

我们将`el`DOM element 传入`$`函数，这样我们就可以对它调用`focus`。

然后我们可以通过写来使用它:

```
<input v-focus>
```

# 在同级组件之间通信

我们可以使用 mixin 来创建一个发出事件的 Vue 实例。

例如，我们可以写:

```
const eventHub = new Vue();Vue.mixin({
  data() {
    return {
        eventHub: eventHub
    }
  }
})
```

然后我们可以用它发出一个事件:

```
this.eventHub.$emit('update', data)
```

因为我们用`Vue.mixin`全局定义了 mixin。

然后我们可以通过写来听事件:

```
this.eventHub.$on('update', data => {
  // do your thing
})
```

一个更简单的解决方案是使用`thus.$root.$emit`发出一个具有给定值的事件:

```
this.$root.$emit('event', data);
```

然后，我们可以在任何组件中监听它，方法是在组件挂载时向组件添加一个事件监听器:

```
mounted() {
  this.$root.$on('event', data => {
    console.log(data);
  });
}
```

现在，组件将侦听事件。

回调函数有一个`data`参数，这个参数包含了发出的数据。

![](img/cc16251697378b145b62f040dc1fb8c2.png)

亚历山大·杜默在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们可以通过使用正则函数来解决`this`的很多问题。

使用`this.$root.$emit`可以全局发出事件。

此外，我们可以通过从子节点发出事件并在父节点中监听它来在子节点和父节点之间进行交流。