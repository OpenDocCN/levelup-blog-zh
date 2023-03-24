# 我编写简单代码的四大模式

> 原文：<https://levelup.gitconnected.com/my-top-4-patterns-for-writing-simple-code-466705ac0b97>

![](img/d73ce67126426829be9879cd55bff642.png)

凯利·西克玛在 [Unsplash](https://unsplash.com/s/photos/building-blocks?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

说到写代码，我的目标是写简单的代码。鲍伯·马丁所说的干净代码。其他人所说的可读性或可维护性。在许多方面，它们都指同一件事。

但是很难**！**

**编写简单的代码需要深思熟虑。它需要几轮重构，直到代码恰到好处。它通常涉及同行评审或结对编程。**

**但是我在职业生涯中发现的一些模式帮助我编写了简单的代码。不一定更快或更容易，但更简单。当我面前有新问题时，我会求助于这些模式，它们几乎总能让复杂的事情变得简单一点。**

## **关于模式**

**作为一个简短的介绍，当我谈到模式时，我通常指的是您可能听说过的一组 [OOP 模式](https://en.wikipedia.org/wiki/Software_design_pattern)。我知道 OOP 在很多方面已经过时了——但是不管你喜欢哪种模式，其中一些模式仍然适用。他们每个人都更喜欢简单的组合而不是继承，这正是大多数人讨厌 OOP 的地方。**

**我在这里提到的大多数模式都来自于四人组的开创性著作:[设计模式](https://en.wikipedia.org/wiki/Design_Patterns)。我将只对每种模式做一个简单的介绍，所以我强烈建议您通过提供的链接来更详细地阅读它们。**

## **模式 1:抽象工厂**

**[工厂](https://en.wikipedia.org/wiki/Factory_(object-oriented_programming))本质上是一个对象，它唯一的工作就是生成其他对象。工厂可以以不同的方式出现，但是在我看来抽象工厂模式是最强大的。**

**[抽象工厂](https://en.wikipedia.org/wiki/Abstract_factory_pattern)不仅允许你在运行时改变生成或构建的对象，还可以在运行时改变工厂。虽然这听起来有些不可思议，但对于像 Spring 或 Unity 这样的控制框架的反转来说，这确实很有效。**

**从代码的角度来看，通常看起来有点像:**

```
interface Factory<T> {T build(Metadata d)}class ClientFactory implements Factory<Client> {Client build(Metadata d) {
        // Build actual object and return      
    }}
```

**每当我需要构建一个与基于配置的简单接口相匹配的具体对象时，我都会尝试使用抽象工厂，并且我不希望使用该对象的每个其他类知道发生了什么变化。是的，那是一个很长的句子。核心思想与其他软件工程原则是相同的经典思想:信息隐藏、做一件事的类和小接口。更直接地说，抽象工厂有助于隐藏构建对象的乏味。**

## **模式 2:委托人**

**我敢肯定，我们都在从事某个项目(代码或非代码)，在这个项目中，我们决定将工作委托给其他人，而不是自己做某个方面的工作。这通常发生在你在项目中处于更高的位置——例如，一个项目协调员可能会将工作委托给一组助理协调员，这些助理协调员将工作委托给志愿者领导，等等。等等。**

**代码中的[委托者](https://en.wikipedia.org/wiki/Delegation_pattern)模式完全相同:高阶类要求低阶类为它们完成工作。这有助于高阶类保持简单，并且对其下面的结构知道得更少。**

**从代码的角度来看，它看起来有点像:**

```
interface Validator {

    bool validate(Object o)}class ValidatorHelper implements Validator {Set<Validator> delegates;

    bool validate(Object o) { 
        for (Validator v : delegates) { 
            if (!v.validate(o)) return false
        }
        return true
    }}class RestController {ValidationHelper helper;Response addObject(Object o) {
        if (helper.validate(o)) return ErrorResponse// Normal processing
    }}
```

**我发现委托器的使用对于验证、排序、规范化等非常有用。可能特定于特定数据形式的常见事物，但是对该数据做出决策的类可能不需要知道委托工作的全部复杂性。它只需要知道工作已经完成。**

## **模式 3:构建器/命名参数**

**在所有改变我写代码方式的模式中，[构建器模式](https://en.wikipedia.org/wiki/Builder_pattern)是我最喜欢的一个。我从一开始就用构建器编写我的每一个 DTO(数据传输对象)。实际上，构建器不需要太多的工作就能生成灵活且可扩展的代码，而且如果你需要的话，它们还具有不变性的优势！**

**其他语言可能没有(甚至不需要)builder 模式，因为它们在构造函数中使用相同的默认值命名参数。本质上，这是一样的:只声明你希望被设置为特定值的东西，不要担心其他的。**

**在代码中，它看起来像这样:**

```
class Dto {String s
    int iprivate Dto(String s, int i) {
        this.s = s
        this.i = i
    }public DtoBuilder builder() {
        return new DtoBuilder()
    }public static class DtoBuilder {

        private String s = "some string"
        private int i = 0public DtoBuilder withString(String s) {
            this.s = s
            return this
        }public DtoBuilder withInt(int it) {
            this.i = i
            return this
        }public Dto build() {
            return new Dto(s, i)
        }
    }}
```

***注意:在 java 中，我们使用* [*Lombok*](https://projectlombok.org/) *来完成所有繁琐的编码工作。***

**这种模式让我的代码如此简单的原因是，当所有的**对象都使用构建器时，创建一个新的是自动的。在我们的代码库中，我们总是在我们想要构建的类中添加一个静态工厂方法来返回构建器。从那里，我们简单地遵循 fluent API 的链，传入您的变量，然后键入`.build()`。完成了。你不会花时间去看构造函数。你甚至不用花时间去看构建器代码。它就在那里，你在写作的时候用它。在现代的 ide 中，自动补全功能告诉你可以设置什么样的变量。简单。****

## **模式 4:浓缩器**

**这种模式在 G-o-4 的书中没有，但它与责任链和模板方法关系最密切。在这种模式中，每个“链”丰富或扩充一个对象，并将丰富的对象返回给调用者。它可以为链中的每个富集器这样做，或者如果需要，链可以决定跳过链的其余部分。**

**您可能会认为您违反了干净代码中关于函数副作用的规则。我的理由是*没有*违反这些原则，因为充实器**必须**将充实后的对象返回给调用者，所以在很多方面，它声明对象可能会改变。对于调用者所知道的，它可能是一个新对象(特别是在不可变约束的情况下)。**

```
interface Enricher<T> {T enrich(T thing);}class HeadersEnricher implements Enricher<Headers> {

    Headers enrich(Headers headers) {
        headers.add("x-header", "something")
        return headers
    }}
```

**我发现这个特别有用的地方是当你需要用新的状态来丰富一个对象的时候。例如，如果您有一个来自 Kafka 流的对象，需要在将它存储到数据仓库之前添加一些数据，那么 enricher 模式可以很好地工作。**

**这些只是我编写简单代码时最喜欢的一些模式。无论如何，这不是一个详尽的列表，但是我每天都使用这些模式来解决编码问题。只不过是将更多的工具添加到您的编码工具带上。**

**编码快乐！**