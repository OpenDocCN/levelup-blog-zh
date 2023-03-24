# 解决常见的 Vue 问题——类、绑定等等

> 原文：<https://levelup.gitconnected.com/solving-common-vue-problems-classes-binding-and-more-4d4292daa66d>

![](img/98256a4e3ac32db18653ba56e5486828.png)

[Jelleke Vanooteghem](https://unsplash.com/@ilumire?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Vue.js 让开发前端应用变得简单。然而，我们仍有可能遇到问题。

在这篇文章中，我们将看看一些常见的问题，并看看如何解决它们。

# 条件类样式绑定

有多种方法可以动态改变元素的类名。

我们可以使用`:class`或`v-bind:class`绑定。

例如，我们可以写:

```
<div v-bind:class="getClass()"></div>
```

然后在`getClass`中，我们可以返回一个对象，将类名作为属性键，并将它们显示为值:

```
methods:{
  getClass(){
    return {
      'checked': this.content.shouldCheck,  
      'unchecked': !this.content.shouldCheck
    }
  }
}
```

如果`this.content.shouldCheck`是`true`，我们将`checked`类应用于 div。否则，如果`this.content.shouldCheck`是`false`，我们就应用`unchecked`类。

我们也可以传入任何我们想要的参数:

```
methods:{
  getClass(prop){
    return {
      'checked': this.content[prop],  
      'unchecked': !this.content[prop]
    }
  }
}
```

然后我们可以写:

```
<div v-bind:class="getClass(prop)"></div>
```

在我们的模板中通过属性名`this.content`返回类名。此外，我们可以编写任何表达式，返回带有我们想要应用的类名的字符串。

例如，我们可以写:

```
<div v-bind:class="checked"></div>
```

其中`checked`是一个计算属性:

```
computed: {
  checked() {
    return this.content.checked ? 'checked' : 'unchecked';
  }
}
```

`checked`是一个计算属性，根据`this.content.checked`的值返回带有我们想要的类名的字符串。

# 不要在 Vue.js for 循环中使用 Index 作为键

当我们使用`v-for`时，我们应该为每个条目传递一个唯一的 ID 来表示`key`属性的值。这样，如果我们以任何方式重新排列数组条目，key prop 的值都不会受到影响。

当我们重新排列或删除条目时，索引可能会改变，所以使用索引不会帮助 Vue 跟踪它们。

因此，我们应该始终使用唯一的 ID 作为`key`属性的值。

# 在页面之间共享数据

我们可以通过使用 Vuex 在页面之间共享数据。使用 Vuex，我们可以定义一个商店来存储应用范围内可用的状态。

所以我们可以写:

```
import Vue from 'vue'
import Vuex from 'vuex'Vue.use(Vuex)const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment(state) {
      state.count++
    }
  }
})
```

将全局`count`状态存储在存储器中。

然后，为了更新`count`状态的值，我们可以通过编写以下代码对其调用`this.$store.commit`:

```
this.$store.commit('increment')
```

这可以在组件中调用。

那么我们可以通过写下得到这个值:

```
this.$store.state.count
```

在组件中。

# v-model 和 v-bind 的区别

让我们将数据从输入绑定到状态变量。

例如，如果我们有:

```
<input v-model="value">
```

然后输入的值被自动设置为`value`的值。同样，如果我们设置了`value`的值，那么输入的值也被设置。

`v-model`是`v-bind:value`和`v-on:input`合在一起的简称。

因此，我们也可以这样写:

```
<input
   v-bind:value="value"
   v-on:input="value= $event.target.value"
>
```

或者:

```
<input
  :value="value"
  @input="value= $event.target.value"
>
```

当我们输入时，输入会发出`input`事件。发出时，具有输入值的`$event.target.value`被设置为`value`的值。

然后，我们用以下方式将值属性绑定到`value`:

```
:value="value"
```

或者:

```
v-bind:value="value"
```

任何发出`input`事件并具有`value`属性的组件都可以替代输入元素。这使得用 Vue.js 创建我们的定制输入组件变得容易。

从上面的例子中我们可以看到，我们用`v-bind`或简称`:`将`value`状态的值传递给输入。

# 监听组件上的本机事件

我们可以在`v-on:click`指令上添加`native`修饰符来监听本地点击事件。

例如，我们可以写:

```
<foo v-on:click.native="testFunction"></foo>
```

我们用`v-on:click.native`而不是`v-on:click`来监听`foo`组件上的点击事件。

同样，我们也可以把它写得更短:

```
<foo @click.native="testFunction"></foo>
```

![](img/a33a5c3e9b66c7392b95ac1897895b5b.png)

照片由[海萨姆·纳巴维](https://unsplash.com/@hessamnbv?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们可以用`native`修饰语来听本地事件。

此外，我们可以通过返回一个对象或一个包含我们想要的类的字符串来有条件地应用类。

`v-bind`进行从父到子的单向数据绑定，`v-model`启用双向数据绑定。