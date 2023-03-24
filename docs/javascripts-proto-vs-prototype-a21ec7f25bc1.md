# Javascript 的 __proto__ vs 原型

> 原文：<https://levelup.gitconnected.com/javascripts-proto-vs-prototype-a21ec7f25bc1>

## Javascript 很混乱。在所有神秘的东西中，`__proto__`和`prototype`的区别是独一无二的。这让大多数试图学习该语言面向对象方面的程序员感到困惑。

![](img/2f55f14040b50a2df3bc842b49f680c2.png)

本文面向对面向对象编程有基本了解并探索 Javascript 实现这一点的开发人员。

让我们从以下几点重新开始

1.  Javascript 中的一切都是对象(除了基本类型)
2.  每个物体都有原型
3.  一个对象的默认原型是`Object.prototype`
4.  当访问对象的属性时，对象会查看自己的属性。如果没有找到，那么它会查看其原型的属性。并且它继续这样做，除非找到该属性或者它到达了`Object.prototype`。如果在`Object.prototype`中没有找到该属性，则返回 undefined。

如果您对这些不清楚，您可以查看 [MDN 文档中的 Javascript](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Object_prototypes) 原型。

1.  **什么是** `**__proto__**` **其目的是什么？**

为了更好地理解这一点，让我们继续看一些例子。

![](img/4f198746ecbc2c95f45f75807587b311.png)

`person`是一个简单的对象，它有三个属性`name`、`age`和`gender`。

![](img/cc3651fe2a21a3781b75f86be4f6777e.png)

在对那个`person`对象使用`console.dir` 时，我们可以看到它有一个`__proto__`属性。

`__proto__`属性是添加到每个对象的默认属性。这个属性指向对象的`prototype`。

每个对象的默认`prototype`为`Object.prototype`。因此，`person`对象的`__proto__`属性指向`Object.prototype`。

如果我说的是真的，那么插图看起来会像这样。

![](img/30e1544cbbdfd3488adb061b8269bcdc.png)

让我们验证一下我所说的是不是真的。

![](img/779c1262c369a3169093603c25a19521.png)

正如你所看到的，`person`对象的`__proto__`属性确实等于`Object.prototype`。

现在，我想创建另一个对象`teacher`。该对象将具有与`person`对象相同的属性和值。只有一个额外的属性—— `subject`——将被添加。

![](img/0c3763266db1b87ea4b4d61772248910.png)

默认情况下，`teacher`的`__proto__`属性会指向`Object.prototype`对象。

插图看起来会像这样。

![](img/b6546645fab56f57fc60be804470013a.png)

你能看出这里的问题吗？`teacher`和`person`有三个相同值的公共属性。如果我们能以某种方式从`person`继承`teacher`的属性，那就太好了。

为此，让我们首先放弃当前的`teacher`对象。并创建一个只有`subject`属性的新对象，因为我们将从`person`对象继承其他属性。

![](img/6f04a21d69c858bd5cf11f6d8ea87579.png)

现在，这个结构看起来就像这样。

![](img/dd89575018d24fb7c8f688b2e2c35449.png)

注意，新的`teacher`的`__proto__`属性仍然指向`Object.prototype`对象。为了继承，我们必须使`teacher`的`__proto__`属性指向`person`。

我们可以使用`Object.setPrototypeOf`功能。

![](img/aac4e747842a84958e356e764bfd8335.png)

现在，插图看起来像这样。

![](img/cf7e32dd24a11a50f6be5e6a34bc2b48.png)

`teacher`的`__proto__`属性指向`person`对象，`person`的`__proto__` 属性指向`Object.prototype`对象。

让我们验证一下`teacher`的`__proto__`属性是否确实指向了`person`对象。

![](img/3f6cd013611c82758717b561a69f7663.png)

我们已经通过原型链接成功地继承了`person`对象的属性。

现在，我们可以从`teacher`对象中访问属性。如果在对象本身中找到该属性，它将返回。否则它会问它的原型等等。

![](img/a5e7f23de8faad080d873981249a8326.png)

需要注意的一件重要事情是，`__proto__`只是对原型的引用，而不是实例化。因此，如果我们修改原型对象中的任何属性，它也会影响子对象。

![](img/8aba97b6cd3d94a3b8146a370792f5b2.png)

看看修改`person`的属性是如何改变`teacher`的`__proto__`属性的。

因此，可以有把握地说，术语**原型继承**并不完全准确。与其说是实际继承，不如说是委托！

简而言之，每个对象的`__proto__`属性都指向对象的原型。

**2。什么是构造函数中的** `**prototype**` **属性，它的用途是什么？**

让我们从例子开始，因为它有助于更好地理解这个主题。

如果我们必须创建一堆属性相同但值不同的`person`对象会怎么样？

我们可以很容易地创建一个构造函数来满足这个目的。

构造函数是一种特殊类型的函数。它就像一个物体的蓝图。您可以使用构造函数创建具有相同属性但不同值的对象。

构造函数看起来会像这样。

![](img/b9b8a959e14170c82064412bdf785a51.png)

因为函数也是对象，所以它会有一个指向函数原型的`__proto__`属性。

函数是特殊类型的对象。函数的`__proto__`属性指向`Function.prototype` 而不是`Object.prototype`。

![](img/973082ae65e853c399464a96c2e18cae.png)

然而，除了`__proto__`属性，构造函数还有一个`prototype`属性。

注意嵌套在`Person`构造函数的`prototype`属性中的`__proto__`属性。

![](img/5babe20954862b43d864f8de55a72064.png)

原来嵌套的`__proto__` 属性其实指向的是`Object.prototype`。

图表看起来就像这样，

![](img/341487064adbd048e4ede973ea083356.png)

让我们验证一下图表是否正确。

![](img/f28d1a86b744778b9da3d894aedb7960.png)

但是`Person`构造函数中的`prototype`属性的用途是什么呢？

原来，每当我们用`Person`构造函数实例化一个对象时，构造函数都会使新对象的`__proto__`属性指向与它的`prototype`属性相同的对象。

这可能没什么意义。

让我们来看一些真实的例子。

让我们使用`Person`构造函数实例化一个名为`mySelf`的新对象。

![](img/8e4b7de0e16e9548e3c617b720796c94.png)

注意构造函数是如何让`mySelf`的`__proto__`属性指向`Person.prototype`的。

最后一幅插图看起来像这样，

![](img/abe111c885a85082068c070bb44f3a11.png)

因此，构造函数的`prorotype`属性的唯一目的是初始化使用该构造函数实例化的对象的`__proto__`属性。

**结论**

希望我没有让你更加困惑。现在你对`__proto__`和`prototype`的区别有了更多的了解。

这无疑是一个糟糕的设计，让很多程序员感到困惑。但是如你所知，Javascript 更像是一种旋转的语言，而不是一种设计良好的语言。所以，我们迟早要与它和平共处。

我很高兴你已经读完了这篇文章。我真的希望你能就你的想法说几句话。✏️