# Beyond console.log():用 JavaScript 格式化控制台输出的 3 种方法

> 原文：<https://levelup.gitconnected.com/beyond-console-log-3-ways-to-format-console-outputs-in-javascript-a345918519aa>

![](img/b174320e721b3fe82b80a167ae6be2ec.png)

[Artem Sapegin](https://unsplash.com/@sapegin?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

作为 JavaScript 开发人员，我们直观地使用`console.log()`来调试、打印变量和记录当前操作的结果，以确保我们处于正确的编程路径上。

的确， *console.log()* 看起来足够强大，但是你知道在[控制台 API](https://developer.mozilla.org/en-US/docs/Web/API/Console) 中还有其他很酷的方法可以让你的生活变得更简单吗？

最近，我在一个教程中偶然发现了 *console.table()* ，这促使我研究简单的 *console.log()* 的替代方法。下面是我添加到调试工具包中的 3 个格式化工具:

# 1.`console.table()`

顾名思义，`console.table()`将输出打印在一个格式良好的表格中，而不是来自`console.log()`的一串文本。

假设我们有一个对象数组，每个对象包含多个键值对:

```
const inventors = [
  { first: 'Albert', last: 'Einstein', year: 1879, passed: 1955 },
  { first: 'Isaac', last: 'Newton', year: 1643, passed: 1727 },
  { first: 'Galileo', last: 'Galilei', year: 1564, passed: 1642 },
  { first: 'Marie', last: 'Curie', year: 1867, passed: 1934 },
  { first: 'Johannes', last: 'Kepler', year: 1571, passed: 1630 },
  { first: 'Nicolaus', last: 'Copernicus', year: 1473, passed: 1543 },
  { first: 'Max', last: 'Planck', year: 1858, passed: 1947 },
  { first: 'Katherine', last: 'Blodgett', year: 1898, passed: 1979 },
  { first: 'Ada', last: 'Lovelace', year: 1815, passed: 1852 },
  { first: 'Sarah E.', last: 'Goode', year: 1855, passed: 1905 },
  { first: 'Lise', last: 'Meitner', year: 1878, passed: 1968 },
  { first: 'Hanna', last: 'Hammarström', year: 1829, passed: 1909 }
]
```

下面是他们和`console.table(inventors)`在一起的样子:

```
┌─────────┬─────────────┬───────────────┬──────┬────────┐
│ (index) │    first    │     last      │ year │ passed │
├─────────┼─────────────┼───────────────┼──────┼────────┤
│    0    │  'Albert'   │  'Einstein'   │ 1879 │  1955  │
│    1    │   'Isaac'   │   'Newton'    │ 1643 │  1727  │
│    2    │  'Galileo'  │   'Galilei'   │ 1564 │  1642  │
│    3    │   'Marie'   │    'Curie'    │ 1867 │  1934  │
│    4    │ 'Johannes'  │   'Kepler'    │ 1571 │  1630  │
│    5    │ 'Nicolaus'  │ 'Copernicus'  │ 1473 │  1543  │
│    6    │    'Max'    │   'Planck'    │ 1858 │  1947  │
│    7    │ 'Katherine' │  'Blodgett'   │ 1898 │  1979  │
│    8    │    'Ada'    │  'Lovelace'   │ 1815 │  1852  │
│    9    │ 'Sarah E.'  │    'Goode'    │ 1855 │  1905  │
│   10    │   'Lise'    │   'Meitner'   │ 1878 │  1968  │
│   11    │   'Hanna'   │ 'Hammarström' │ 1829 │  1909  │
└─────────┴─────────────┴───────────────┴──────┴────────┘
```

# 2.`console.group()`和`console.groupEnd()`

这一对控制台方法允许您创建输出的层次结构。如果你有一组相关的数据，你可以把它们包装在`console.group()`和`console.groupEnd()`里面，就像这样:

```
console.group('Meat')
console.log('Chicken')
console.log('Pork')
console.log('Beef')
console.groupEnd('Meat')console.group('Veggies')
console.log('Corn')
console.log('Spinach')
console.log('Carrots')
console.groupEnd('Veggies')console.group('Fruits')
console.log('Apple')
console.log('Banana')
console.log('Tomato')
console.groupEnd('Fruits')
```

…您将看到一组漂亮的输出(请随意在 Chrome 或 Firefox 控制台上尝试，在我看来它看起来更漂亮):

```
Meat
  Chicken
  Pork
  Beef
Veggies
  Corn
  Spinach
  Carrots
Fruits
  Apple
  Banana
  Tomato
```

# 3.`console.dir()`

这个可能有用，也可能没用，取决于你用的浏览器。实际上，`console.dir()`打印出指定 JavaScript 项目中属性的层次列表。

例如，打开浏览器控制台，通过传入以下对象来尝试`console.dir()`:

```
const food = {
  'Meat': {
    'Chicken': ['grilled', 'fried'],
    'Pork': ['BBQ', 'steamed'],
    'Beef': ['medium', 'rare']
  },
  'Veggies': {
    'Corn': ['white', 'yellow'],
    'Spinach': ['regular', 'baby-size'],
    'Carrots': ['regular', 'bite-size']
  },
  'Fruits': {
    'Apple': ['green', 'red'],
    'Banana': ['raw', 'ripe'],
    'Tomato': ['big', 'small']
  },
}
```

在 Chrome 控制台中，你可以看到`console.dir(food)`比`console.log(food)`更容易识别数据类型。而在 Firefox 中，两种输出看起来是一样的。

尽管如此，如果您单击输出开始处的三角形，`console.dir()`和`console.log()`都会显示格式良好的 JavaScript 对象，这些对象按照属性类型及其值整齐地组织起来。

如果您已经熟悉这三种方法，我建议您查看以下资源，尝试文章中提到的每种方法，并让我知道您计划经常使用哪种方法。

调试愉快！

## 资源:

*   [如何充分利用 JavaScript 控制台](https://www.freecodecamp.org/news/how-to-get-the-most-out-of-the-javascript-console-b57ca9db3e6d/)
*   【JavaScript 开发者控制台备忘单
*   [超越 console.log() —调试 JavaScript 和节点时应该使用的 8 种控制台方法](/moving-beyond-console-log-8-console-methods-you-should-use-when-debugging-javascript-and-node-25f6ac840ada)