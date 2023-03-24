# React 与 Vue —组件和道具基础知识

> 原文：<https://levelup.gitconnected.com/react-vs-vue-components-and-props-basics-4d4b047e36c1>

![](img/86b5223296a32700fad2b6d823bf6267.png)

照片由[奥内拉·宾尼](https://unsplash.com/@ornellabinni?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

React 是最近几年最流行的前端库。

Vue 是一个前端框架，在过去几年里，它的流行程度已经赶上了 React。

很难在这两种框架之间做出选择，因为它们各有利弊。当我们选择了一个，我们必须坚持几年。

在本文中，我们将比较如何创建 React 和 Vue 组件，处理道具以及哪一个对开发人员更方便。

# 创建组件

使用 React，我们通过创建函数来创建组件，并在`props`参数中传递属性。

例如，我们可以通过编写以下代码来创建一个组件并将一个道具传递给它:

```
import React from "react";function Foo({ msg }) {
  return <div>{msg}</div>;
}export default function App() {
  return (
    <div className="App">
      <Foo msg="hello" />
    </div>
  );
}
```

在上面的代码中，我们创建了一个带有从`App`传递到`Foo`的`msg`道具的`Foo`组件。然后我们访问`Foo`组件中`prop`参数的`msg`属性，并在那里显示`msg`。

使用 Vue，我们定义组件和使用它们的方式与 React 示例相似。

我们可以创建一个 Vue 组件并将一个道具传递给它，如下所示:

`index.html`:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>App</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  </head>
  <body>
    <div id="app">
      <Foo msg="hello"></Foo>
    </div>
    <script src="index.js"></script>
  </body>
</html>
```

`index.js`:

```
Vue.component("Foo", {
  props: ["msg"],
  template: "<div>{{msg}}</div>"
});const app = new Vue({
  el: "#app",
  data: {}
});
```

在上面的代码中，我们用`Vue.component`创建了一个`Foo`组件，然后将`msg`属性指定为道具。

然后在`index.html`中，我们引用`Foo`并传入`msg`属性，就像我们对 React 示例所做的那样。

正如我们所看到的，这个简单的例子在 React 和 Vue 之间非常相似。

# 将数据从子代传递到父代

React 和 Vue 都能够将数据从子节点传递到父节点。

使用 React，我们可以将一个函数传递给带有一些参数的子组件。

子进程调用该函数，以便父进程可以获取数据。

我们可以做我们用 React 描述的事情，如下所示:

```
import React, { useState } from "react";function FooInput({ setMsg }) {
  return <input onChange={e => setMsg(e.target.value)} />;
}export default function App() {
  const [msg, setMsg] = useState();
  return (
    <div className="App">
      <FooInput setMsg={setMsg} />
      <br />
      <span>{msg}</span>
    </div>
  );
}
```

在上面的代码中，我们将从`useState`钩子返回的`setMsg`函数传递给了我们的`FooInput`组件。

然后`FooInput`用输入的值调用我们从 props 传入的`setMsg`函数。

这将把输入值设置为`msg`的值。

最后，我们在 span 中显示`msg`的值。

有了 Vue，我们不必将父组件的功能直接传递给子组件。

相反，我们可以发出一个事件，其值是我们希望发送给父节点的值。然后父节点可以对这个值做任何他们想做的事情。

我们可以在 React 示例中用 Vue 做同样的事情，如下所示:

`index.html`:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>App</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  </head>
  <body>
    <div id="app">
      <foo-input v-bind:value="msg" v-on:input="msg = $event"></foo-input>
      <br />
      <span>{{msg}}</span>
    </div>
    <script src="index.js"></script>
  </body>
</html>
```

`index.js`:

```
Vue.component("foo-input", {
  props: ["value"],
  data() {
    return {
      msg: ""
    };
  },
  watch: {
    value(val) {
      this.msg = val;
    }
  },
  template: `<input v-model="msg" @input="$emit('input', $event.target.value)">`
});const app = new Vue({
  el: "#app",
  data: {
    msg: ""
  }
});
```

在上面的代码中，我们有一个`foo-input`组件，它接受一个`value`属性，我们用观察器将它的最新值设置为`this.msg`，这样我们就不会直接改变`value`属性。

然后我们用`input`事件名和输入的值调用`$emit`，输入的值存储在`$event.target.value`中。

输入的值随后被发送到父对象，并在`$event`对象中可用。

我们监听事件，通过写入以下内容将值设置到`msg`字段:

```
v-on:input="msg = $event"
```

作为`foo-input`组件上的指令。

然后我们在`foo-input`组件下面的跨度中显示`msg`的值。

一旦我们完成了所有这些，我们应该看到我们输入的内容显示在输入下面。

的简写:

```
v-bind:value="msg" v-on:input="msg = $event"
```

是:

```
v-model='msg'
```

这是一个特例，不适用于应用程序发出的事件。

所以我们可以写:

`index.html`:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>App</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  </head>
  <body>
    <div id="app">
      <foo-input v-model="msg"></foo-input>
      <br />
      <span>{{msg}}</span>
    </div>
    <script src="index.js"></script>
  </body>
</html>
```

无论如何，React 示例更简单。从子组件和父组件传递数据肯定比 Vue 更容易反应。

![](img/cee07cf16ec8a169ee1127ea7a2df2f3.png)

克里斯·莱佩尔特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 裁决

比较 React 和 Vue 时，创建组件和从父级和子级传递道具的过程非常相似。

React 确实比 Vue 更容易将数据从孩子传递给父母。我们只是将一个函数传递给孩子，并从那里调用它，而不是发出事件并监听它们。