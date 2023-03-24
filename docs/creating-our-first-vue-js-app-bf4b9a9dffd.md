# 创建我们的第一个 Vue.js 应用

> 原文：<https://levelup.gitconnected.com/creating-our-first-vue-js-app-bf4b9a9dffd>

![](img/4af0e44eca859540eb079f26eac25879.png)

巴德·汉森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在本文中，我们将看看如何创建我们的第一个 Vue.js 应用程序。我们将研究数据绑定、条件呈现和循环。

# 入门指南

Vue.js 旨在成为一个构建 ui 的渐进式框架。它可以以增量方式添加到使用其他库的现有应用程序中。

此外，它可以用来创建一个新的独立应用程序。像 Angular 和 React 等其他框架一样，它可以通过 Vue CLI 及其自己的库生态系统，使用现代工具来创建单页应用程序。

为了快速开始，我们可以使用一个`script`标签来包含 Vue.js 框架的开发版本，它包含了只有在使用这个构建时才可用的警告。这使得开发更容易。

我们可以通过编写以下内容来包含开发构建:

```
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

在我们的`index.html`文件中或:

```
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
```

用于生产构建。

为了创建我们的第一个应用程序，我们首先创建`index.html`并添加:

```
<!DOCTYPE html>
<html>
  <head>
    <title>Hello</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head> <body>
    <div id="app">
      {{ message }}
    </div>
    <script src="./src/index.js"></script>
  </body>
</html>
```

然后在`src/index.js`中，我们添加:

```
new Vue({
  el: "#app",
  data: {
    message: "Hello"
  }
});
```

然后我们应该看到`Hello`印在浏览器标签上。

在`index.html`中，我们添加了 Vue.js 框架，其中包含:

```
<script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
```

然后我们添加了 Vue.js 模板，如下所示:

```
<div id="app">
  {{ message }}
</div>
```

接下来，我们添加了:

```
<script src="./src/index.js"></script>
```

这样我们就可以加载自己的 JavaScript 代码，使用 Vue.js 来初始化应用程序。

然后在`src/index.js`中，我们添加:

```
new Vue({
  el: "#app",
  data: {
    message: "Hello"
  }
});
```

这样我们就可以加载我们的应用程序，将`message`变量设置为`'Hello'`字符串，当我们有:

```
{{ message }}
```

双花括号表示它是`data`的属性，它将用我们传递给`Vue`构造函数的对象中`data`的`message`属性的值填充`message`占位符。

将元素属性绑定到`data`属性中的属性值的另一种方法是使用`v-bind:title`。

在`index.html`中，我们写道:

```
<!DOCTYPE html>
<html>
  <head>
    <title>Hello</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head> <body>
    <div id="app">
      <span v-bind:title="message">
        Hello
      </span>
    </div>
    <script src="./src/index.js"></script>
  </body>
</html>
```

然后，当我们保持`src/index.js`不变时，当我们将鼠标悬停在它上面时，我们会看到一个工具提示说`Hello`。

我们所做的是将`span`的`title`属性设置为传递给`Vue`构造函数的对象中`data.message`的值。

`v-bind`被称为指令，用于在上面的代码中动态设置`title`属性的值。

![](img/42ed205331a151899eee0be03965169d.png)

[马特·希顿](https://unsplash.com/@mattisrad?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 条件式

我们可以通过使用`v-if`指令有条件地在屏幕上显示一些东西。

为了使用它，我们可以在`index.html`中编写以下内容:

```
<!DOCTYPE html>
<html>
  <head>
    <title>Hello</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head> <body>
    <div id="app">
      <span v-if="visible">
        Hello
      </span>
    </div>
    <script src="./src/index.js"></script>
  </body>
</html>
```

那么当我们在`src/index.js`中写下如下内容时:

```
new Vue({
  el: "#app",
  data: {
    visible: false
  }
});
```

我们什么也没看见。

这是因为我们通过将`visible`设置为`false`并将其传递给`v-if`来隐藏`span`。当我们有一个`v-if`指令添加到一个元素中时。只有当我们传递给`v-if`指令的值是`true`时，它才会显示。

`v-if=”visible”`表示当`data.visible`为`true`时，将显示该元素的内容。

所以如果我们把`src/index.js`改成:

```
new Vue({
  el: "#app",
  data: {
    visible: true
  }
});
```

我们应该会在浏览器屏幕上看到`Hello`。

# 环

我们可以使用`v-for`指令来遍历数组，这些数组是`data`的属性值。

例如，我们可以如下使用它。在`index.html`中，我们写道:

```
<!DOCTYPE html>
<html>
  <head>
    <title>Hello</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head> <body>
    <div id="app">
      <ul>
        <li v-for="person in persons">
          {{person.name}}
        </li>
      </ul>
    </div>
    <script src="./src/index.js"></script>
  </body>
</html>
```

然后在`src/index.js`中，我们写道:

```
new Vue({
  el: "#app",
  data: {
    persons: [{ name: "Mary" }, { name: "Jane" }, { name: "Joe" }]
  }
});
```

然后我们得到:

```
Mary
Jane
Joe
```

在我们的浏览器屏幕上。

上面的代码所做的是，我们将`index.js`中的`data.persons`设置为一个数组，然后这个数组可以被`index.html`中的`v-for`指令循环。

然后，Vue.js 按照我们在模板中指定的方式呈现每个`li`元素中每个条目的`name`属性的值:

```
<ul>
  <li v-for="person in persons">
    {{person.name}}
  </li>
</ul>
```

所以我们得到了列表中的条目。

# 结论

我们可以通过在 HTML `script`标签中包含 Vue.js 框架来创建一个简单的 Vue.js 应用程序，然后我们可以在 JavaScript 文件中添加 Vue.js 应用程序的代码。

然后，我们可以使用指令来显示对象的`data`属性中的项目，并将其传递给模板中的`Vue`构造函数。

我们可以使用`v-bind`指令来设置数据属性值。`v-if`有条件地显示内容，而`v-for`通过遍历数组条目并呈现内容来显示来自数组条目的内容。