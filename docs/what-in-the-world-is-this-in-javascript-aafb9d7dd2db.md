# Javascript 中的‘this’到底是什么？

> 原文：<https://levelup.gitconnected.com/what-in-the-world-is-this-in-javascript-aafb9d7dd2db>

![](img/03608be2ad088a70f42562a4b70839eb.png)

照片由[悉尼·瑞伊](https://unsplash.com/@srz?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/this?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

Javascript 中另一个经常混淆的概念是' *this'* 关键字。什么是'*这个*，什么是'*这个*所指？

答案是 ***看情况*** 。在我回答这个“T15”指的是什么之前，让我先问另一个问题。

# “这”是什么？

我说的不是一个好奇的 2 岁小孩指着所有的东西问，“这是什么？那是什么？”我说的是 Javascript 的关键字' *this* '。

Javascript 使用' *this'* 关键字来引用单个对象。从你作为程序员的角度来看，这个“T21”可以让你精确定位你关注的对象。就像一个孩子指着书架问，“我能要一本书吗？”你拿起书(在这种情况下是一个特定的对象)并问:“这是你想要的吗？”

# 你说看情况是什么意思？

回到原来的问题，那么'*这个*指的是什么？这取决于这个是如何被调用的。有四种类型的绑定，它决定了如何引用' *this* '。

1.  新绑定
2.  显式绑定
3.  隐式结合
4.  默认绑定

上面的顺序是绑定的优先级。然而，如果我不按顺序解释绑定，会更有意义。

# 隐式结合

在隐式绑定中，' *this* '是函数调用期间点左边的对象。

以下是'*这个*'将如何根据左边的对象而变化的例子。

隐式结合

如你所见，根据 sayNames 函数左边的对象，“this”指的是 Jasmine 或 Jello。

# 显式绑定

在显式绑定中，' *this'* 是您使用 call()、apply()或 bind() Javascript 方法显式定义的对象。

对于以下显式绑定方法，让我们假设我们有以下对象:

```
let john = {name: 'John',age: 30,hobbies: ['painting', 'cooking', 'biking']}
```

**使用 call()方法的显式绑定**

使用。call()方法，可以手动调用一个以特定对象作为参数的函数:

```
myFunction.call(thisArgument, arg1, arg2, ... );
```

下面是一个示例，说明如何将一个' *this* '值和参数提供到。call()方法。

使用 call()方法的显式绑定

正如你所看到的，'*约翰*'作为'*这个'*参数被传入，附加参数被分别传入。

**使用 apply()方法的显式绑定**

正如您在 call()示例中所看到的，手动传递每个参数会非常繁琐。这就是我们使用 apply()方法的原因。

使用 apply()方法的绑定与使用 call()方法的绑定几乎完全相同，只是您传入的是一个数组:

```
myFunction.apply(thisArgument, [argumentArray]);
```

下面使用与 call 方法相同的示例，但显示了如何使用数组参数调用函数。

使用 apply()方法的显式绑定

如您所见，' *john* 仍然作为'*this '【T3]参数传入，但是其他参数作为单个数组传入。*

**使用 bind()方法的显式绑定**

现在，如果您想做 call()方法所做的事情，但不调用函数，该怎么办呢？这就是使用 bind()方法的地方。bind()方法将使用您提供的特定对象返回一个新函数，并且只有在您调用返回的函数时才会被调用。

这里有一个例子。如您所见，bind()方法返回一个函数，并且在调用 myFunction()之前，console.log 不会出现。

使用 bind()方法进行显式绑定

# 新绑定

当您使用' *new* 关键字创建新对象时，'*这个*将引用新对象。

使用新关键字绑定

# 默认绑定

如果上述所有情况都为真，那么‘this’将指向一个全局对象。

# 结论

简单回顾一下，有四种类型的调用和应用' *this* '的优先级顺序:

1.  新绑定
2.  显式绑定
3.  隐式结合
4.  默认绑定

# 引用

我使用了以下资源来帮助我理解这些主题:

[1].泰勒·麦金尼斯。*高级 Javascript 课程。* (2020)。[*https://tylermcginnis.com/courses/*](https://tylermcginnis.com/courses/)

[2].奥萨马·埃尔马沙德。 *Javascript 的‘this’关键字，它是如何工作的？*。(2020).[https://alligator.io/js/finally-understand-reduce/T21](https://medium.com/tech-tajawal/javascript-this-4-rules-7354abdb274c)

[3].MDN Web 文档。(2020).[https://developer.mozilla.org/](https://developer.mozilla.org/)

[4].扎克·卡塞雷斯。*如何在 Javascript 中挖掘你的“This”上下文。* (2020)。[https://gist . github . com/zcaceres/2 a4 AC 91 f 9 f 42 EC 0 ef 9 CD 0d 18 E4 e 71262](https://gist.github.com/zcaceres/2a4ac91f9f42ec0ef9cd0d18e4e71262)

[5].NC 帕特罗。 *JavaScript —关于“这个”关键字的一切*。(2020).[https://code burst . io/all-about-this-and-new-keywords-in-JavaScript-38039 f 71780 c](https://codeburst.io/all-about-this-and-new-keywords-in-javascript-38039f71780c)