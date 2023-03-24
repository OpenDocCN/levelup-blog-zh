# 用 v-model 的 Vue.js 处理表单输入

> 原文：<https://levelup.gitconnected.com/handling-form-input-with-vue-js-with-v-model-17d6260cc1f7>

![](img/256a64588e66f649eb2786732471fbe7.png)

照片由[克里斯蒂娜·戈塔迪](https://unsplash.com/@cristina_gottardi?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在本文中，我们将看看如何用 Vue.js 为`v-model`指令处理表单输入。

# 基本用法

我们可以使用`v-model`指令为表单`input`、`textarea`和`select`元素创建双向绑定。

它从表单中获取数据，在 Vue 实例或组件中设置数据，并相应地更新视图。

它将忽略表单元素上的一些初始值、选中或选定的属性。它会将 Vue 实例数据视为事实的来源。因此，我们应该在组件的`data`选项中设置初始数据。

`v-model`为不同的输入元素使用不同的属性并发出不同的事件:

*   `text`和`textarea`元素使用`value`属性和`input`事件
*   复选框和单选按钮使用`checked`属性和`change`事件
*   选择字段使用`value`属性和`change`作为事件

`v-model`在 IME 合成期间没有更新。我们可以监听`input`事件来获取这些更新。

# 文本输入

一个简单的例子如下:

`src/index.js`:

```
new Vue({
  el: "#app",
  data: {
    message: "Hello"
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
  </head> <body>
    <div id="app">
      <input type="text" v-model="message" />
      <p>Message is {{message}}</p>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

然后，当我们在输入框中输入一些东西时，我们将得到下面显示的同样的东西。

# 多行文本

它也适用于多行文本。例如，我们可以写:

`src/index.js`:

```
new Vue({
  el: "#app",
  data: {
    message: "Hello"
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
  </head> <body>
    <div id="app">
      <textarea type="text" v-model="message"> </textarea>
      <pre>{{message}}</pre>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

注意只有`v-model`对模型绑定有效，写`<textarea>{{message}}</textarea>`不行。

# 检验盒

我们可以在复选框上使用`v-model`,如下所示:

`src/index.js`:

```
new Vue({
  el: "#app",
  data: {
    checked: false
  }
});
```

`index.js`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>App</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head> <body>
    <div id="app">
      <input type="checkbox" id="checkbox" v-model="checked" />
      <label for="checkbox">{{ checked }}</label>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

然后，当我们切换复选框时，我们将根据复选框是否被选中来显示`true`或`false`。

绑定到同一模型的多个复选框将被绑定到同一数组。

例如，我们可以编写以下内容:

`src/index.js`:

```
new Vue({
  el: "#app",
  data: {
    persons: []
  }
});
```

注意`persons`必须是数组。

`index.html`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>App</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head> <body>
    <div id="app">
      <label>
        Joe
        <input type="checkbox" v-model="persons" value="Joe" />
      </label>
      <label>
        Jane
        <input type="checkbox" v-model="persons" value="Jane" />
      </label>
      <label>
        Mary
        <input type="checkbox" v-model="persons" value="Mary" />
      </label>
      <p>{{ persons.join(", ") }}</p>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

在上面的代码中，我们必须绑定到同一个模型，然后当复选框被选中时，我们将在`persons`数组中填充`value`属性值。

如果这三项都被选中，我们会得到如下结果:

```
Jane, Joe, Mary
```

取决于它们被检查的顺序。

![](img/d0e5407dd02f53cd7528be8054059278.png)

保罗·吉尔摩在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 收音机

我们也可以在单选按钮上使用`v-model`。要使用它，我们可以写:

`src/index.js`:

```
new Vue({
  el: "#app",
  data: {
    person: ""
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
  </head> <body>
    <div id="app">
      <input type="radio" value="Jane" v-model="person" />
      <label>Jane</label>
      <br />
      <input type="radio" value="Mary" v-model="person" />
      <label>Mary</label>
      <br />
      <p>{{ person }}</p>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

然后，当我们单击一个单选按钮时，我们得到单选按钮被选中时显示的`value`属性值。

# 挑选

我们可以如下使用`select`上的`v-model`:

`src/index.js`:

```
new Vue({
  el: "#app",
  data: {
    selected: ""
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
  </head> <body>
    <div id="app">
      <select v-model="selected">
        <option disabled value="">Select One</option>
        <option>foo</option>
        <option>bar</option>
        <option>baz</option>
      </select>
      <br />
      <span>Selected: {{ selected }}</span>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

然后，当我们选择一个选项时，就会显示所选的选项。

如果`v-model`表达式的初始值与任何选项都不匹配，它将以未选中状态呈现。

多选也可以。例如，我们可以如下使用它:

`src/index.js`:

```
new Vue({
  el: "#app",
  data: {
    selected: []
  }
});
```

注意，它必须是一个数组。

`index.html`:

```
<!DOCTYPE html>
<html>
  <head>
    <title>App</title>
    <meta charset="UTF-8" />
    <script src="[https://cdn.jsdelivr.net/npm/vue/dist/vue.js](https://cdn.jsdelivr.net/npm/vue/dist/vue.js)"></script>
  </head> <body>
    <div id="app">
      <select v-model="selected" multiple>
        <option>foo</option>
        <option>bar</option>
        <option>baz</option>
      </select>
      <br />
      <span>Selected: {{ selected.join(', ') }}</span>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

我们将`multiple`属性添加到`select`元素中，以允许多重选择。然后，当我们按住 shift 键单击多个选项时，将会显示多个选项。

我们还可以动态呈现选项，如下所示:

`src/index.js`:

```
new Vue({
  el: "#app",
  data: {
    selected: undefined,
    options: [
      { text: "One", value: "one" },
      { text: "Two", value: "two" },
      { text: "Three", value: "three" }
    ]
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
  </head> <body>
    <div id="app">
      <select v-model="selected">
        <option v-for="option in options" v-bind:value="option.value">
          {{ option.text }}
        </option>
      </select>
      <br />
      <span>Selected: {{ selected }}</span>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

将显示选择`option`元素的`value`属性值。

# 结论

我们可以在`input`、`textarea`和`select`元素上使用`v-model`指令。

它对单个和多个选择元素都有效。将数据绑定到多个选择元素时，它必须绑定到一个数组。

我们还可以使用`v-for`为具有多个选择的元素动态呈现选择。