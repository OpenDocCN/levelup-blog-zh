# 通过一个实际的例子了解如何用 Javascript 创建可链接的方法

> 原文：<https://levelup.gitconnected.com/learn-how-to-create-chainable-methods-in-javascript-with-a-practical-example-da8ed81d560d>

![](img/2c28f61b08ebc7ce0ed026edf9e84eac.png)

来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=3481377) 的[类比](https://pixabay.com/users/analogicus-8164369/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=3481377)图像

> 这篇文章最初出现在我的博客上

链接是编程中一个强大的概念。当您在返回的对象上调用方法而没有重新赋值时，就会发生这种情况。假设您有一个`users`对象:

```
const users = [
  {fname: ‘Joseph’, lname: ‘Ada’, age: 12}, {fname: ‘Bernard’, lname: ‘Rutsell’, age: 81}, {fname: ‘Thompson’, lname: ‘Josaih’, age: 41}, {fname: ‘Dayo’, lname: ‘Adeoye’, age: 17}]
```

你的任务是过滤掉 18 岁以上的用户。我们可以用下面不使用方法链接的代码做到这一点:

```
// get all the ages from the user objects
const ages = users.map(user => user.age)// only retain ages that are less than 18
const under18 = ages.filter(age => age < 18);console.log(ages); // [ 12, 81, 41, 17 ]console.log(under18) // [ 12, 17 ]
```

(如果你不知道`map`或`filter`是做什么的，请查看我的另一个帖子，在那里我用简单的英语解释了这些方法，[Javascript 数组方法的常识解释](https://solathecoder.hashnode.dev/a-common-sense-explanation-of-javascript-array-methods-ck2gq53m200jjwks1hedlpeb5)。)

Javascript 数组方法可以链接在一起，这意味着我们可以执行`map`操作和`filter`操作，而不必先重新分配返回值:

```
const under18Chained = users
                         .map(user => user.age)
                         .filter(age => age < 18);console.log(under18Chained); // [ 12, 17 ]
```

由于`map`返回一个数组，我们可以在返回的数组上调用`filter`,而不必重新分配`map`返回的内容。

最流行的 JavaScript 框架也实现了链接。例如，我们可以在 jQuery 中这样做:

```
_$(‘button’)
         .mousover(function () { console.log(‘mouse over’) }) .click(() => alert(‘clicked’));
```

在这篇文章中，我们将编写一个`Validator`函数，它也允许方法链接，在这篇文章结束时，我们将能够做到这一点:

```
Validator(40)
   .num().min(0).max(5)
```

validator 函数接受一个数字(40)并检查它是否是一个数字，是否不小于 0，以及是否不大于 5。在这种情况下，`40`无法通过第二次测试(`max(5)`)

**注意**:要跟进，你可以在任何现代浏览器的控制台中输入代码，或者使用 JS Bin。或者您所知道的任何其他执行 JavaScript 代码的方式:)。

# **方法链接是如何工作的**

当我们在上面的例子中链接`map`和`filter`` 时。我们可以这样做，因为`map`函数返回了一个数组——所有的数组都有一个可以在其上调用的`filter` 方法。`filter`也返回一个数组，我们也可以将其他数组方法链接到它。所以链接方法的关键是方法返回什么。

JavaScript 中的每个数组都从`Array.prototype`对象继承方法，所以我们可以访问这些数组方法(`filter`、`map`、`reduce`等等)，即使我们没有定义它们！JavaScript 中的数组也是一个对象。

```
users
  .map(user => user.age) // returns an array with `filter` as a property .filter(age => age < 18); // returns an array too!
```

如果我们想实现我们的验证器函数，用一个参数(在本例中是一个数字)调用`Validator`应该返回一个属性为`num()`的*对象*。当我们调用`num()`时，它也应该返回一个属性为`min()` 的对象，而`min()`应该返回一个属性为`max()`的对象。这里的技巧是让它们返回同一个对象，这个对象将有`min`、`num`和`max`作为它的属性！

```
Validator(40) // returns an object with ‘num’ as a property
   .num() // returns an object with ‘min’ as a property .min(0) // returns an object with ‘max’ as a property .max(5)
```

# **分步实施**

让我们定义一个`Validator`函数，它返回一个将`num` 作为方法的对象:

这个`num`方法返回真或假，这实际上没有什么帮助。我们希望`num` 返回与`Validator`相同的对象。为此，重新编写`Validator`如下:

现在我们可以链`num`而不破坏东西！—但这仅发生在第一个`num`通过时，我们目前正在返回失败时的`false`，调用`false.num()`将导致错误。要理解我的意思，试试这个:

```
// the first num() returns false, the second `num` call results in an error
console.log(Validator(“A”).num().num());// TypeError: Validator(…).num(…).num is not a function…
```

为了解决这个问题，当测试失败时，我们也需要返回`actions`——但是当测试失败时，我们也会包含错误消息。

酷毙了。任何使用我们函数的人都可以检查`errors`数组是否为空以进行验证。例如，假设我们使用这个函数来验证用户输入:

将变量 age 更改为类似于`const age = ‘hello’`的字符串，然后运行代码，您会得到:

```
Error: Expected elem to be a number!
```

现在你明白了，让我们实现`min`方法。

酷:)

你能实施`max`方法吗？它与`min`方法非常相似。在检查以下解决方案之前，请先试用一下:

你现在明白了。我们已经成功地编写了可链接的方法！我们正在成为 JavaScript 忍者！:)

# **增加更多功能**

在约鲁巴语中，`jara`这个词的意思是添加超过严格必要的东西，我们已经完成了这个帖子想要做的，但我们还可以做得更多。再来补充一下`jara`:)。

我们将让该函数的使用者能够指定一个可选的错误消息，如下所示:

```
Validator(5)
 .num(‘Please input a number’) .min(5, ‘Make sure the number is at least 5’) .max(20, ‘Too big! The number should not be more than 20’)
```

如果没有给出错误消息，那么我们返回默认的错误消息。请在检查解决方案之前尝试实现这一点！一..2..走吧。

你想出了同样的解决方案吗？还是用了条件语句(`if…else`)？Javascript 现在支持默认参数，我已经在上面使用过了！条件语句也解决了这个问题，但是缺省参数方法更简洁。

# **结论**

感谢您读到这里！我打赌这是值得的。如果你像我喜欢写这篇文章一样喜欢读这篇文章，(打字…对吗？:) )请在社交媒体上分享，你可以在 twitter 上关注我 [@solathecoder](https://twitter.com/solathecoder) 。

## 介意加点捷瑞吗？

你能扩展`Validator`函数来验证电子邮件、布尔或/和字母数字吗？试试看，你可以在下面的评论区放下你的代码，我会很高兴看到你想出什么。

黑客快乐！