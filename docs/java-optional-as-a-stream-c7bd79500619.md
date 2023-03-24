# Java 可选为流

> 原文：<https://levelup.gitconnected.com/java-optional-as-a-stream-c7bd79500619>

## 可选类型和流类型的唯一关系。

![](img/cbce320c2313a96c12e17e3ecce8bc1b.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=204398) 的 [Christian_Birkholz](https://pixabay.com/users/christian_birkholz-76800/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=204398)

最近，我一直在用[项目反应堆](https://projectreactor.io/)和它们的两种主要类型`Mono`和`Flux`做很多工作。一个`Mono`和一个`Flux`的主要区别在于一个`Mono`只能表示零个或一个元素，而一个`Flux`可以表示零个或无限个元素。最近，我真正理解了`Mono`和`Flux`以及 Java `Optional`和`Stream`类型之间的相似性。事实上，从 Java 9 开始，您可以使用`stream()`方法将一个`Optional`转换成一个`Stream`。

对于我们这些无法完全按照预期使用`Optional`类型的人来说，这种相似性非常有帮助。自从 Java 8 第一次问世以来，我就接受了`Stream`类型，但是`Optional`类型我是后来才采用的。我认为这是因为它没有像 streams 那样被视为突破。

`Optional`型与`Stream`型、`filter`、`map`和`flatMap`共有几种方法。在每种情况下，它们的行为都像`Stream`一样，如果“流”是空的，它们都返回空的。在 Java 9 中，使用`Optional`类型的`stream()`方法带来了整个`Stream`方法套件，其中一些有意义，而另一些则没那么有意义。

在我最近的一篇文章中，[所有的循环都是一种代码味道](https://medium.com/swlh/all-loops-are-a-code-smell-6416ac4865d6)，我建议(有点夸张地)我们为一个表示循环的函数消除日常循环，我们只使用流提供循环内部发生的事情。我还谈到了类似地表示 if 语句，但是我演示的唯一例子是使用`Optional.ofNullable`删除普遍存在的`if(x != null)`语句。但是进一步思考一下，我可以看到 if 语句*是 while 语句*，但是有零次或一次迭代，我可以看到模式。

如果我们能够使用`Optional`而不是 If 语句，我们也可以返回一个类似于 Scala 从 if 语句返回值的值。但是我们需要一个接受测试值和谓词函数的方法。如果谓词函数返回 true，将返回测试值(可能已转换)，否则，将返回空值。你可以用一个`Optional`来做这件事:

```
Optional<T> ret = Optional.of(test)
  .filter(t -> t.equals(expected));
  .map(t -> transform(t)); // add a possible transformation
```

这相当于:

```
T ret = null;
if(test.equals(expected)) {
  ret = transform(test);
}
```

但是没有了`null`的生意。

由于`Optional`没有类似`ofNullable()`的方法`ifConditional()`，我们必须创建一个:

```
static <T, R> Optional<R> ifConditional(T test, 
            Predicate<T> conditional, 
            Function<T, Optional<R>> map) {
  return conditional.test(test) 
          ? map.apply(test) 
          : Optional.empty();
}
```

给定该函数，您可以更改此`if/else`链:

```
static String getSoundWithIf(Pet pet) {
  if(pet == Pet.dog) {
    return "bark";
  } else if(pet == Pet.cat) {
    return "meow";
  } else if(pet == Pet.bird) {
    return "squawk";
  } else if(pet == Pet.mouse) {
    return "squeek";
  } else {
    return "beep";
  }
}
```

相当于:

```
static String getSoundWithOptional(Pet pet) {
  return ifConditional(pet, 
    p1 -> p1 == Pet.dog, p1 -> Optional.of("bark"))
      .orElseGet(() -> ifConditional(pet,
    p1 -> p1 == Pet.cat, p1 -> Optional.of("meow"))
      .orElseGet(() -> ifConditional(pet,
    p1 -> p1 == Pet.bird, p1 -> Optional.of("squawk"))
      .orElseGet(() -> ifConditional(pet,
    p1 -> p1 == Pet.mouse, p1 -> Optional.of("squeak"))
      .orElse("beep"))));
}
```

当然，我并不是建议你去掉所有的 if/else 链或者 switch 语句。if/else 链是惯用的，看起来很舒服。这仅仅是一个关于`Optional`和`Stream`以及`if`和`while`语句之间对称性的思想实验。以这种方式展示，它展示了我们如何使用函数式编程在这两者之间移动。

也许有一天，函数构造会像结构化编程构造一样惯用，编译器会了解它们并为它们做更好的优化。任何 Scala 程序员都可以更简洁地给出最后一个解决方案，但是我在这里用 Java 给出它，因为许多 Java 开发人员仍然需要学习函数式编程。学习从谓词和功能、供应商和消费者类型的角度进行思考是非常有用的。