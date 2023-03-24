# 单元测试—使用 Mockito 的参数捕捉器

> 原文：<https://levelup.gitconnected.com/unit-tests-argument-captor-with-mockito-bd70db7555f4>

![](img/72a502effc504e2906a4898e2738314e.png)

[**Mockito**](https://www.google.com/search?sxsrf=ALeKk03lXNrz7SO1qhTZX590Sjv2j7jwkQ%3A1597239131285&ei=W-8zX4n7EJLmU_bGugg&q=Mockito+github&oq=Mockito+github&gs_lcp=CgZwc3ktYWIQAzIECCMQJzIECCMQJzIFCAAQywEyBggAEBYQHjIGCAAQFhAeMgYIABAWEB4yBggAEBYQHjIGCAAQFhAeMgYIABAWEB4yBggAEBYQHjoECAAQRzoGCCMQJxATOgQIABBDUPgVWOQaYJ0baABwAngAgAF_iAGHBJIBAzIuM5gBAKABAaoBB2d3cy13aXrAAQE&sclient=psy-ab&ved=0ahUKEwjJlKq445XrAhUS8xQKHXajDgEQ4dUDCAw&uact=5) 是一个流行的 mock 框架，可以与 JUnit 结合使用。Mockito 允许创建和配置模拟对象。使用 Mockito 极大地简化了具有外部依赖的类的测试开发。

在本文中，使用的语言将是 Kotlin，嘲讽框架将是 Mockito 的变体，[。这是一个围绕 Mockito 的包装器，适应 Kotlin 的特性，如可空类型。](https://github.com/nhaarman/mockito-kotlin)

参数捕获器是一个 Mockito 特性，用于从测试中调用的函数中捕获参数。本文将展示这种方法的几种用法。

# 验证参数的内部值

有效使用参数捕获器的一种方法是验证传递给函数的参数的内部值。

在这个例子中，在被测试的函数中，一个依赖项的函数被一个参数调用。使用参数捕获器，可以访问作为参数使用的整个对象，以断言其属性。

这个功能对 Mockito 用户来说并不陌生，因为使用[参数匹配器](https://github.com/nhaarman/mockito-kotlin/wiki/Mocking-and-verifying#argument-matchers)的更简单的语法也可以实现同样的功能。

在以前的 Java 版本中，要创建一个参数匹配器，需要创建一个新的类并覆盖“matches”函数，这不是最清晰的语法。随着 Java 8 和 lambda 函数的引入，可读性变得更好了。

尽管这两个特性可以做类似的事情，但还是会有一个比另一个更好的情况。

*   在需要多次验证同一个类的情况下，最好创建一个自定义的 ArgumentMatcher。
*   当参数类型为基本类型时，建议使用默认的 ArgumentMatcher。
*   如果在测试的后面需要使用作为参数发送的对象，应该使用参数捕获器。
*   任何其他情况都应该留给开发者来决定。

注意，如果参数匹配器或参数捕获器用于函数的一个参数，那么它需要用于所有的参数。有关更详细的解释，请查看此[解释](https://medium.com/r?url=https%3A%2F%2Fstackoverflow.com%2Fa%2F22822514%2F3340404)。

更多两者的对比，请看[这个](https://www.baeldung.com/mockito-argument-matchers)和[这个](https://www.planetgeek.ch/2011/11/25/mockito-argumentmatcher-vs-argumentcaptor/)。

# 验证循环中的参数

参数捕获器能够根据被验证的函数被调用的次数来捕获各种参数。这使我们能够验证捕获的每个参数。

# 验证回调

在这个例子中，一个参数捕获器被用来捕获一个高阶函数。可以调用这个函数，以便可以测试回调中的代码。在这种情况下，验证是否调用了另一个函数。这同样适用于接口。这可以增加代码覆盖率。

# 其他语言中的参数捕捉器

在 C#和 PHP 中，模拟依赖关系时，可以定义一个回调函数来调用相应的参数。在这个回调中，允许发送参数，完成类似于参数捕获器的事情。虽然它更混乱，可读性更差。

*   对于 C#，使用 [Moq](https://github.com/Moq/moq4/wiki/Quickstart#callbacks) 和[回调](https://github.com/Moq/moq4/wiki/Quickstart#callbacks)，按照此[条](https://www.derpturkey.com/validate-mock-arguments-with-moq/)执行。
*   对于 PHP，使用 [PHPUnit](https://github.com/sebastianbergmann/phpunit) 和[回调](https://phpunit-document-english.readthedocs.io/en/7.2/test-doubles.html)，参见[例 1](https://stackoverflow.com/questions/7822036/phpunit-get-arguments-to-a-mock-method-call/23284877#23284877) 和[例 2](https://stackoverflow.com/questions/19255171/phpunit-testing-with-closures/19278447#19278447) 。

除了 Java/Kotlin，还有其他语言的 Mockito 改编版。

如果你知道在其他语言中实现与 Argument Captor 相同功能的其他方法，请在评论中留下。