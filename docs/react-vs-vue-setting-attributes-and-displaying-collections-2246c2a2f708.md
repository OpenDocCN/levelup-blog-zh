# React vs Vue —设置属性和显示集合

> 原文：<https://levelup.gitconnected.com/react-vs-vue-setting-attributes-and-displaying-collections-2246c2a2f708>

![](img/433ebb54da305debce2999c62d5c9530.png)

照片由 [Giovany Pineda Gallego](https://unsplash.com/@giovanypg?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

React 是最近几年最流行的前端库。

Vue 是一个前端框架，它的受欢迎程度正在赶上 React，并且有一个非常热情的开发人员社区采用它。

很难在这两种框架之间做出选择，因为它们各有利弊。当我们选择了一个，我们必须坚持几年。

在本文中，我们将比较 React 和 Vue 的设置属性和显示集合。

# 设置属性

使用 React，我们可以像 HTML 一样设置属性。但是我们也可以在我们的应用程序中设置动态属性。

例如，我们可以编写以下代码来实现这一点:

```
import React, { useState } from "react";export default function App() {
  const [color, setColor] = useState("red");
  const toggleColor = () => {
    setColor(color === "red" ? "green" : "red");
  }; return (
    <div className="App">
      <button onClick={toggleColor}>Toggle</button>
      <div className="box" style={{ color }}>
        foo
      </div>
    </div>
  );
}
```

在上面的代码中，我们用`useState`钩子动态设置 div 的颜色，钩子返回`setColor`函数让我们设置颜色。

然后我们将`color`传递给`style`属性。要为我们的 div 设置静态类名，我们必须使用`className`属性。当我们用 React 设置属性时，我们必须记住像`className`而不是`class`这样的区别。

使用 Vue，我们将数据和方法放在组件中，并设置静态和动态属性，如下所示:

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
      <button @click="toggleColor">Toggle</button>
      <div class="box" :style="{color: color}">
        foo
      </div>
    </div>
    <script src="index.js"></script>
  </body>
</html>
```

`index.js`:

```
const app = new Vue({
  el: "#app",
  data: {
    color: "red"
  },
  methods: {
    toggleColor() {
      this.color = this.color === "red" ? "green" : "red";
    }
  }
});
```

使用 Vue，我们必须将`toggleColor`方法添加到方法中，并为`data`对象中的`color`字段添加默认值。

然后，我们必须在`toggleColor`方法中添加设置颜色的相同逻辑。

这比我们使用 React 要复杂得多。但是，它确实将显示逻辑和业务逻辑分成了不同的部分。

当我们的组件更复杂时，这更容易处理。

# 显示收藏

通过将数组映射到组件集合，我们可以用 React 显示集合。

例如，如果我们有一个名称数组，我们可以编写以下代码用 React 显示它们:

```
import React, { useState } from "react";export default function App() {
  const [persons] = useState([
    { name: "Mary" },
    { name: "John" },
    { name: "Joe" }
  ]); return (
    <div className="App">
      {persons.map(p => (
        <div>{p.name}</div>
      ))}
    </div>
  );
}
```

在上面的代码中，我们有一个`persons`数组，然后用`p.name`作为文本内容映射到 divs。

为了对 Vue 做同样的事情，我们可以编写以下代码:

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
      <div v-for="p of persons">
        {{p.name}}
      </div>
    </div>
    <script src="index.js"></script>
  </body>
</html>
```

`index.js`:

```
const app = new Vue({
  el: "#app",
  data: {
    persons: [{ name: "Mary" }, { name: "John" }, { name: "Joe" }]
  }
});
```

在上面的代码中，我们有 Vue 组件中的`persons`数组，以及显示`persons`数组中项目的`v-for`指令。

这比我们用 React 得到的要干净。我们保持每个文件中的代码更加紧凑。

如果组件越多越复杂，这一点就会更加明显，这是经常发生的情况。

`v-for`的另一个好处是，我们也可以使用它们来遍历对象的条目。

例如，我们可以编写以下代码来实现这一点:

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
      <div v-for="(age, name) in persons">
        {{name}}: {{age}}
      </div>
    </div>
    <script src="index.js"></script>
  </body>
</html>
```

`index.js`:

```
const app = new Vue({
  el: "#app",
  data: {
    persons: {
      John: 20,
      Mary: 19,
      Jane: 20
    }
  }
});
```

我们可以用下面的方法分别得到一个对象条目的值和键:

```
v-for="(age, name) in persons"
```

使用 React，我们使用`Object.entries`来做同样的事情:

```
import React, { useState } from "react";export default function App() {
  const [persons] = useState({
    John: 20,
    Mary: 19,
    Jane: 20
  }); return (
    <div className="App">
      {Object.entries(persons).map(([name, age]) => (
        <div>
          {name}: {age}
        </div>
      ))}
    </div>
  );
}
```

表达式变得越来越复杂，因为我们必须调用`Object.entries`，然后将它们映射到我们想要呈现的组件。

![](img/8b1b3832bc8adc5eaca15b74394602ea.png)

照片由[哈坎·努拉尔](https://unsplash.com/@hakannural?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 裁决

对于简单的组件，React 和 Vue 在设置属性方面没有太大的区别。

然而，我们必须记住，它们都是大小写字母，名称可能与 HTML 不同。

Vue 在这里有一点优势，因为我们不用担心这个。动态属性可以用`:`设置，其余相同。

使用 Vue，我们必须学习`v-for`指令来呈现集合，而我们只是使用普通的 JavaScript 来完成 React。

然而，使用 React，我们必须编写一个大的表达式来完成这项工作，而在 Vue 中，我们可以向模板添加指令来完成这项工作。

这使得用 Vue 渲染集合更干净，所以 Vue 在这里也有一点优势。