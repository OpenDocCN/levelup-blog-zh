# JavaScript 问题——用 React 显示/隐藏元素，发出自定义事件，等等

> 原文：<https://levelup.gitconnected.com/javascript-problems-show-hide-elements-with-react-emitting-custom-events-and-more-f9f1868611d9>

![](img/8d7928a30bc5edb2e7624c274d4a5f37.png)

卡洛斯·阿方索在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

像任何类型的应用程序一样，当我们编写 JavaScript 应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些常见 JavaScript 问题的解决方案。

# 在 React 中显示或隐藏元素

要在 React 中显示或隐藏元素，我们可以设置显示或隐藏状态。

然后我们可以写一个布尔表达式来显示结果。

例如，我们可以写:

```
const Results = () => (
  <div>
    Some Results
  </div>
)const Search = () => {
  const [showResults, setShowResults] = React.useState(false)
  const onClick = () => setShowResults(true)
  return (
    <div>
      <button onClick={onClick}>show results</button>
      { showResults ? <Results /> : undefined }
    </div>
  )
}
```

我们已经有了我们想要显示的组件。

然后我们有了带有开关的`Search`组件来显示`Results`组件。

只有当`showResults`是`true`时才会显示，因为我们有:

```
showResults ? <Results /> : undefined
```

设置某个东西到`undefined`会让它消失。

`useState`返回一个数组，第一个条目保存状态值，第二个条目包含设置状态的函数。

# 获取字符串的最后一个字符

要获得字符串的最后一个字符，我们可以使用`slice`方法。

例如，我们可以写:

```
str.slice(-1);
```

获取字符串的最后一个字符。

-1 表示字符串的最后一个索引。

我们也可以写:

```
str.charAt(str.length - 1)
```

或者:

```
str[str.length - 1];
```

获取最后一个字符。

我们也可以用`endWith`方法检查最后一个字符是否是给定的字符:

```
str.endsWith("e")
```

# 按关键字对 JavaScript 对象排序

我们可以在遍历键时对对象的键进行排序。

使用`Object.keys`，我们得到一个包含对象的所有非继承字符串键的数组。

因此，我们可以对它调用`sort`方法，按照我们喜欢的方式进行排序。

例如，我们可以写:

```
Object.keys(unordered)
.sort()
.forEach((key) => {
  ordered[key] = unordered[key];
});
```

由于非数字键是按照插入顺序排序的，我们可以像以前一样使用`forEach`将它们插入到一个新的对象中，这个对象的键是有序的。

对象键的排序顺序是所有数组索引优先。

然后所有不是数组的字符串键按照它们被插入的顺序进行索引。

然后我们就有了所有符号键的插入顺序。

# 触发一个事件

我们可以通过使用`CustomEvent`构造函数来触发一个自定义事件。

我们可以传入数据，用它来发出事件

例如，我们可以写:

```
const event = new CustomEvent("cat", {
  detail: {
    isFluffy: true
  }
});
obj.dispatchEvent(event);
```

我们用带有第二个参数的对象发出了`cat`事件。

然后我们可以通过写来听:

```
document.addEventListener("cat", (e) => {
  console.log(e.detail); 
});
```

我们为带有`addEventListener`的`cat`事件添加了一个事件监听器。

第二个参数是一个回调函数，我们将事件对象作为第二个参数传递给了`CustomEvent`构造函数。

所以我们可以用`detail`属性获取值。

# 检查某个数组索引处是否存在值

我们可以通过使用`typeof`操作符来检查一个值是否存在于给定的数组索引中。

例如，我们可以写:

```
if (typeof array[index] !== 'undefined') {
  //...
}
```

检查给定索引处的项目是否不是`'undefined'`。

我们还可以通过写下以下内容来检查它是否是`undefined`而不是`null`:

```
if (typeof array[index] !== 'undefined' && array[index] !== null) {
  //...
}
```

现在我们知道数组在给定的`index`处有一个值，在我们运行任何东西来处理那个项目之前。

# 计算一个字符在字符串中出现的次数

我们可以用几种方法来搜索字符串中某个字符出现的次数。

一种方法是使用`match`方法。

例如，我们可以写:

```
"foo,bar.baz".match(/,/g)
```

然后，我们搜索逗号出现的次数，因为我们使用`/,/g`作为模式。

`g`表示我们搜索一个模式的所有实例。

同样，我们可以使用`RegExp`构造函数:

```
"foo,bar.baz".match(new RegExp(',', 'g')))
```

我们得到了同样的结果。

![](img/51fb08da7b10a8ed06a34ab4e6844588.png)

照片由 [Kosuke Noma](https://unsplash.com/@kkk7799?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 结论

我们可以通过设置一个状态并添加一个布尔表达式来显示或隐藏 React 元素。

要获得字符串的最后一个字符，我们可以使用`slice`或`charAt`。

也有一些方法可以检查一个元素是否存在于给定的数组索引中。