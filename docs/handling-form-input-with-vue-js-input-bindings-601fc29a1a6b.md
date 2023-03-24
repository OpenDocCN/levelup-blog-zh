# 使用 Vue.js 处理表单输入—输入绑定

> 原文：<https://levelup.gitconnected.com/handling-form-input-with-vue-js-input-bindings-601fc29a1a6b>

![](img/f1a79f52a521baba9051e8559fbe60a2.png)

克里斯汀娜·戈塔迪在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在本文中，我们将研究如何以各种方式绑定数据并自动修改输入。

# 值绑定

通常将值绑定为静态字符串，或者复选框的布尔值。

例如，对于 checkbox，我们可以将其绑定到一个字符串，如下所示:

`src/index.js`:

```
new Vue({
  el: "#app",
  data: {
    selected: undefined
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
      foo
      <input type="radio" v-model="selected" value="foo" />
      <br />
      bar
      <input type="radio" v-model="selected" value="bar" />
      <br />
      <span>Selected: {{ selected }}</span>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

复选框绑定到布尔值:

```
<input type="checkbox" v-model="toggle">
```

选择被绑定到在`value`属性中设置的所选字符串:

```
<select v-model="selected">   
  <option value="foo">Foo</option> 
</select>
```

# 检验盒

我们可以用`true-value`和`false-value`属性来改变复选框的 true 和 false 值，如下所示:

`src/index.js`:

```
new Vue({
  el: "#app",
  data: {
    selected: undefined
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
      Toggle
      <input
        type="checkbox"
        v-model="selected"
        true-value="yes"
        false-value="no"
      />
      <br />
      <span>Selected: {{ selected }}</span>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

然后，当我们选中复选框时，我们得到`yes`，否则得到`no`。

`true-value`和`false-value`不会影响输入的`value`属性，因为浏览器不会在表单提交中包含未选中的框。

我们应该使用无线电输入来保证至少选择一个项目。

# 单选按钮

我们可以用`v-bind:value`将`value`属性绑定到所选的值。

例如，我们可以这样写:

`src/index.js`:

```
new Vue({
  el: "#app",
  data: {
    pick: "",
    foo: "foo",
    bar: "bar"
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
      Foo
      <input type="radio" v-model="pick" v-bind:value="foo" />
      <br />
      Bar
      <input type="radio" v-model="pick" v-bind:value="bar" />
      <br />
      <p>Selected: {{ pick }}</p>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

然后`v-bind:value=”foo”`渲染为`value='foo'`并且`v-bind:value=”bar”`渲染为`value='bar'`。

当我们选择一个值时，根据点击的单选按钮，我们得到`foo`或`bar`。

# 选择选项

我们可以将值绑定到对象，如下所示:

`src/index.js`:

```
new Vue({
  el: "#app",
  data: {
    selected: undefined
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
        <option v-bind:value="{ number: 123 }">123</option>
        <option v-bind:value="{ number: 456 }">456</option>
      </select>
      <br />
      <p>Selected: {{ selected }}</p>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

然后我们根据选择的内容显示`{ number: 123 }`或`{ number: 456 }`。

如果我们想显示`number`属性的值，那么我们必须编写以下代码:

`src/index.js`:

```
new Vue({
  el: "#app",
  data: {
    selected: {}
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
        <option v-bind:value="{ number: 123 }">123</option>
        <option v-bind:value="{ number: 456 }">456</option>
      </select>
      <br />
      <p>Selected: {{ selected.number }}</p>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

因为将初始值设置为`undefined`会给我们一个错误，应用程序不会运行。

![](img/1caa3d3c2dad0bde598be9deb62da3fb.png)

塞巴斯蒂安·拉瓦雷在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 修饰语

Vue 附带了一些`v-model`的修改器。

## 。懒惰的

我们可以使用`v-model.lazy`来同步`change`事件的数据，而不是`input`事件。这意味着只有当输入不在焦点上时，更改才会被同步。

例如，我们可以如下使用它:

`src/index.js`:

```
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
  </head> <body>
    <div id="app">
      <input v-model.lazy="msg" />
      <br />
      <p>Value: {{ msg }}</p>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

当我们将光标移出输入时，就会显示输入值。

## 。数字

`number`修饰符将把输入的内容转换成数字。这很有用，因为用`type='number'`输入仍然会返回一个字符串。

例如，我们可以如下使用它:

`src/index.js`:

```
new Vue({
  el: "#app",
  data: {
    age: 0
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
      <input v-model.number="age" type="number" />
      <br />
      <p>Age: {{ age }}</p>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

## 。整齐

我们可以用`.trim`修饰符自动修剪空白。为了使用它，我们可以编写以下代码:

`src/index.js`:

```
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
  </head> <body>
    <div id="app">
      <input v-model.trim="msg" type="text" />
      <br />
      <p>Value: "{{ msg }}"</p>
    </div> <script src="src/index.js"></script>
  </body>
</html>
```

然后，当我们键入带有开始和结束空格的内容时，它们不会显示出来。

所以:

```
" a b c "
```

会变成`"a b c"`。

# 结论

我们可以用`v-bind:value`属性动态绑定值。它让我们将值属性绑定到原始值和对象。

此外，我们可以在`v-model`上使用各种修饰符，以各种方式自动更改输入值，如修整输入并将其转换为数字。