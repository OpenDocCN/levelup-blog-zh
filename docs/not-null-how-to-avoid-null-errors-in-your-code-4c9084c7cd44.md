# Not Null —如何避免代码中的 Null 错误

> 原文：<https://levelup.gitconnected.com/not-null-how-to-avoid-null-errors-in-your-code-4c9084c7cd44>

## 空/未定义的替代

![](img/b0aae8a27b30dd4cef3d0b59afff6bc4.png)

照片由[内特·格兰特](https://unsplash.com/@nateggrant?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

不管我使用哪种语言或框架，我见过的最常见的错误/异常是 [NullPointer 错误](https://www.geeksforgeeks.org/null-pointer-exception-in-java/)或[未定义错误](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined)。不同的名字，但它简单的意思是你试图访问不存在的东西。

为了更好地处理这些错误，语言开始增加对空安全的支持。现在，什么是零安全？空安全意味着如果你使用的变量将会是空的，你必须明确地指出它。在许多语言中，它表示使用`?`。

## 让我们看一个例子

例如，让我们假设下面的类。

```
class Dog {}
```

现在举个例子。在零安全之前

```
Dog dog = new Dog();
```

现在，这个狗变量定义可以是`Dog`或`null`的一个实例。但是请注意，它没有说它也可以是`null`。也就是说，在使用之前，由可变消费者来确定`dog`不是`null`。

现在想象一下，在一个大项目中，有许多变量。而且每个变量都有可能是`null`。

想到我们已经写了这么久的代码。没有表明某样东西可能不存在。这确实令人惊讶。

## 欢迎零安全

[空保险](https://dart.dev/null-safety)来了。也就是说，如果一个变量将要为空，你可以用`?`来表示。

## 例子

```
Dog? dog = new Dog();
```

`dog`变量不能是`Dog`。也有可能是`null`。这就是`?`所指示的。可以说它是一个可空的`Dog`。

这样做的好处是你不能再直接使用`dog`变量。您必须检查可空性或使用空运算符。

## 例子

让我们在`Dog`类上添加一个`eat`方法。

```
class Dog {
  void eat() {}
}
```

说你想在`dog`上调用`eat`方法。早先你必须做`dog.eat();`。现在`dog`是可空的。你得`dog?.eat();`。不同的是，如果狗是`null`。这个程序不会失败并继续运行。与之前的案例`dog.eat();`相比。如果`dog`是`null`。它会失败。

编译器也足够智能，可以帮助您避免对可空性进行双重检查。

## 例子

```
if (dog != null) {
  dog.eat(); // no ? operator
} 
```

即使`dog`在这里可以为空。编译器不会抱怨，因为它看到我们在使用。请注意，这只在`dog`变量被定义在局部范围内时有效。

让我们给`dog`的`eat`方法添加`food`参数。

```
class DogFood {}class Dog {
  void eat(DogFood food) {}
}
```

狗不能吃东西`null`。它需要食物。意思是，我们传给狗的食物不能是`null`。

```
Dog? dog;
DogFood? food;dog?.eat(food); // fails
```

它将失败，因为`food`可以为空。我们需要做一些空值检查来使它工作。

```
if (food != null) {
  dog?.eat(food);
}
```

或者

```
if (dog != null && food != null) {
  dog.eat(food);
}
```

您会注意到代码很快开始变得难看。想象一下有更多的可空变量可以使用。你必须在你的代码中到处散布 if 条件。

零安全是好的。我们可以做得更好。

## 欢迎选项

该选项在不同的编程语言中有不同的名称。像 Java 中的 [Optional、](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html)[可能在 Haskell](https://hackage.haskell.org/package/base-4.16.1.0/docs/Data-Maybe.html) 、dart 的 fpdart 库中的 [Option、typescript 的 fp-ts 库](https://pub.dev/packages/fpdart#optionhttpsgithubcomsandromaglionefpdartblob540431746d616d30fadf36cc9d1a77c14baf35f4libsrcoptiondartl40)中的 [Option。我将使用](https://gcanti.github.io/fp-ts/) [fpdart](https://pub.dev/packages/fpdart) 库的[选项](https://pub.dev/packages/fpdart#optionhttpsgithubcomsandromaglionefpdartblob540431746d616d30fadf36cc9d1a77c14baf35f4libsrcoptiondartl40)。

选项就像是变量的包装器。指示值是否存在。但不仅仅如此。如果只是这样的话。使用 null safety 和 Option 没有太大区别。好处是我们可以用一种可读性更强、更容易理解的方式编写代码，而不会弄乱它。

在我们深入研究期权之前。让我们试着理解选项。

选项来自 [FP](https://en.wikipedia.org/wiki/Functional_programming) 世界。但是我不会用 FP 术语[单子](https://en.wikipedia.org/wiki/Monad_(functional_programming))或者[单子](https://en.wikipedia.org/wiki/Monadic)来吓跑你。相反，我们将从实用的角度来看待它。

把选项想象成一个列表。列表是多个值的容器。同样，Option 是一个只包含 1 个值或不包含任何值的容器。

如果存在一个值。`Option`将是`Some`的一个实例。否则它将成为`None`的一个实例。我们可以更深入地了解`Option`是如何实现的。它必须遵守的法律有哪些？但我会保持简单。

## 例子

就像我们创建一个数字列表

```
List<int> numbers;
```

我们可以创建一个数字选项

```
Option<int> number;
```

我们称之为`number`。它实际上是一个数字选项。也许我们可以称之为`optNumber`或者`maybeNumber`。但还是要保持简单。还是用`number`吧。

您可以如下初始化`number`。

```
number = None(); // or Option.none();
```

这表示该选项为空，不包含值。

```
number = Some(10); // or Option.of(10);
```

这表明该选项有一定价值。这种情况下是`10`。

现在，我们知道了什么是期权的基础。让我们试着把它用于我们的狗和食物的例子。

```
Option<Dog> dog = Option.of(new Dog());
Option<DogFood> food = Option.of(new DogFood());
```

将`food`进给到`dog`。我们可以在不使用 if 条件的情况下做下面的事情。

```
dog.flatMap((d) {
  return food.map((f) => **d.eat(f)**);
});
```

如果这是您第一次使用`flatMap`和`map`或基本功能编程。你可能根本不明白这里发生了什么。

但是让我解释一下。

首先我们来看`map`。和做`array.map`很像。列表中的每一项都被映射到其他东西。但是万一`Option`。如果存在的话，只有一个项目被映射到其他项目。

来到`flapMap`。理解什么是`flapMap`的最好方法之一是查看`map`和`flapMap`的方法签名差异。

FlapMap 签名

```
Option<B> flatMap<B>(Option<B> Function(T t) f)
```

地图签名

```
Option<B> map<B>(B Function(T t) f);
```

如果你看看区别。唯一不同的是`f`函数参数的返回类型。

在`flatMap`情况下。它正在返回`Option<B>`。而且万一`map`。它只返回`B`。

简单来说。`flatMap`允许你消费价值并产生一个新的价值包装选项。

但是万一`map`。它让你消费并产生新的价值。

在上面的代码中。我们正在从选项和消费中解包值。如果任何值(`dog`或`food`)不存在，则根本不会调用函数`flatMap`或`map`。

这与`array.map`的工作原理非常相似。如果在空数组上调用`array.map`。数组中没有要映射的项目。所以，map 函数永远不会被调用。

## 另一个例子

让我们看另一个例子。去理解期权有多强大。

对于我们的例子。假设我们需要获取用户的地址。有 3 个函数可以让你得到用户的地址。

```
Option<String> getAddressFromDevice() {
  ...
}

Option<String> getAddressFromRemote() {
  ...
}

Option<String> getAddressFromThirdParty() {
  ...
}
```

为了简单起见。我不打算实现这些函数。但是请注意。3 个函数如何返回`Option<String>`。表明该函数可能根本不返回任何地址。

要获取用户地址，我们应该按以下顺序尝试这些功能。

*   getAddressFromDevice
*   getAddressFromRemote
*   从第三方获取地址

如果这些函数都不返回地址。我们可以回退到`Hyderabad, India`作为默认地址。

把东西放在一起。代码看起来是这样的。

```
final address = getAddressFromDevice()
    .alt(() => getAddressFromRemote())
    .alt(() => getAddressFromThirdParty())
    .getOrElse(() => "Hyderabad, India");
```

我来解释一下`alt`功能。`alt`代表`alternative`。用于在当前`None`的情况下提供替代`Option`。

所以我们现在做的是。首先我们试试`getAddressFromDevice`，然后是`getAddressFromRemote`，然后是`getAddressFromThirdParty`。如果这些都没有返回任何地址，我们将退回到从`getOrElse`返回的值。

还要注意，如果任何一个函数成功获取了地址。我们不会调用下一个函数。例如，假设`getAddressFromDevice`成功了。它根本不会调用`getAddressFromRemote`和`getAddressFromThirdParty`。

才能理解这有多复杂。尝试在不使用选项的情况下编写上述代码。你会发现这个选择是多么的强大。

这只是皮毛。还有很多东西要学。

如果你想在评论中看到更多这样的文章，请告诉我。我会试着报道更多类似的话题。

我希望你喜欢这篇文章，并学到一些新东西。请为它鼓掌，并跟随我了解更多。

感谢您的阅读。