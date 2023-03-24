# JavaScript 问题—要求、分组数组项、类等等

> 原文：<https://levelup.gitconnected.com/javascript-problems-require-group-array-items-classes-and-more-d3a6269f6cd3>

![](img/c98bc83e52f632d93cdfbea381cc2aac.png)

照片由 [Dragos Gontariu](https://unsplash.com/@dragos126?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何类型的应用程序一样，当我们编写 JavaScript 应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些常见 JavaScript 问题的解决方案。

# 要求的目的

不是标准 API 的一部分。

它用于从 Node.js 中的其他模块导入成员。

模块是一种将应用程序分割成单独文件的方式，而不是将所有应用程序放在一个文件中。

浏览器和节点应用程序的结构是不同的。

浏览器应用程序是从脚本标签加载的。

脚本可以直接访问全局名称空间。

在节点应用程序中，每个模块都有自己的作用域。除非使用`modulee.exports`或`exports`导出并且是必需的，否则不能访问中的成员。

为了导入模块，我们使用了`require`函数。

我们可以从自己的应用程序中获取模块，或者从节点包中获取。

大多数节点软件包是从 NPM 安装的。

这些包在`node_modules`文件夹里。

# 检查对象是否为空

有几种方法可以检查对象是否为空。

例如，我们可以写:

```
obj === null
```

检查对象是否为`null`。

我们也可以写:

```
typeof obj !== 'object'
```

检查`obj`是否不是一个对象。

同样，我们可以使用`getOwnPropertyNames`以数组的形式获取对象的非继承字符串属性名。

所以我们可以写:

```
Object.getOwnPropertyNames(obj).length === 0
```

检查返回的数组是否为空。

同样，我们可以使用`Object.keys`来做同样的事情:

```
Object.keys(obj).length === 0
```

# 按键对对象数组进行分组

我们可以通过将数组条目的值作为新对象的键，按键对对象数组进行分组。

然后，我们从属性中获取数组条目，属性的值在中，给定的键在对象中。

例如，我们可以写:

```
const groupBy = (items, key) => items.reduce(
  (result, item) => ({
    ...result,
    [item[key]]: [
      ...(result[item[key]] || []),
      item,
    ],
  }), 
  {},
);
```

现在我们有了下面的数组:

```
const arr = [{
    foo: 'dog',
    bar: 2
  },
  {
    foo: 'cat',
    bar: 2
  },
  {
    foo: 'dog',
    bar: 3
  },
  {
    foo: 'cat',
    bar: 4
  },
]
```

我们可以通过编写来调用`groupBy`方法:

```
const result = groupBy(arr, 'foo');
```

那么`result`将是:

```
{
  "dog": [
    {
      "foo": "dog",
      "bar": 2
    },
    {
      "foo": "dog",
      "bar": 3
    }
  ],
  "cat": [
    {
      "foo": "cat",
      "bar": 2
    },
    {
      "foo": "cat",
      "bar": 4
    }
  ]
}
```

我们按照`foo`属性对数组进行了分组。

并且每个键的值是具有由该键给出的值为`foo`的条目的数组。

# 用多个分隔符拆分字符串

我们可以使用带有所有分隔符的 regex 的`split`方法，通过所有列出的分隔符来分割一个字符串。

例如，我们可以写:

```
"foo bar, baz".split(/[\s,]+/)
```

然后我们得到:

```
["foo", "bar", "baz"]
```

因为正则表达式中有空白模式和逗号。

# 类方法和类原型方法之间的区别

以下两者之间存在差异:

```
Class.method = function () { 
  //...
}
```

并且:

```
Class.prototype.method = function () {
  //...
}
```

`Class.method`表示`method`方法是`Class`的静态方法。

所以我们可以直接调用它，而不用创建它的新实例。

例如，我们可以写:

```
Class.method();
```

另一方面，`Class.prototype.method`是`Class`的一个实例方法。

这意味着我们必须创建一个`Class`的实例来使用它。

例如，我们可以写:

```
const obj = new Class();
```

然后我们通过写来调用`Class.prototype.method`:

```
obj.method();
```

# 从文件名中获取文件扩展名

如果我们有一个文件名字符串，我们可以调用`split`将文件名除以点号来得到文件名和扩展名。

返回数组的最后一项是扩展名。

所以我们可以写:

```
const ext = filename.split('.').pop();
```

我们调用`pop`返回由`split`返回的数组的最后一个条目来获得扩展。

![](img/f63b45aa4f841b0c1de8f66e4b1a5966.png)

[David Clode](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们可以通过使用`split`从文件名中获取文件扩展名。

附加到`prototype`的方法和直接附加到类的方法是有区别的。

通过使用数组方法，我们可以根据条目的键值对数组条目进行分组。