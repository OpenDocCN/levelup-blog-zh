# 用 JUnit 和 Hamcrest 编写更好的测试用例

> 原文：<https://levelup.gitconnected.com/write-better-test-cases-with-junit-and-hamcrest-45a750a3b223>

## JUnit 是一个简单的开源框架，可以用 Java 编写可重复的测试用例。Hamcrest 是一个以声明方式定义匹配器对象的框架。总之，它们允许我们编写强大、高效的单元测试用例。

![](img/71d15dd8090a5a0e41d4c01049af26fa.png)

图片提供:[https://www . pexels . com/photo/board-chalk-chalk board-exam-459793/](https://www.pexels.com/photo/board-chalk-chalkboard-exam-459793/)

# 概观

J Unit 和 Hamcrest 是两个灵活的单元测试框架，用 Java 编写干净高效的单元测试用例。JUnit 是 Java 中事实上的单元测试框架，拥有开发人员编写单元测试所需的大部分必要工具。在 JUnit 之上，Hamcrest 提供了一些非常强大的 matcher 对象来断言一个测试用例。在本文中，我们将深入 JUnit 和 matcher 对象，看看如何编写有效的测试。

# 属国

我们将使用一个 Apache Maven 项目来演示这两个框架的用法。下面是带有依赖项的 pom 文件:

pom.xml

当前的 JUnit 版本是 JUnit 5。然而，在本文中，我们将关注 JUnit 4，因为这是使用最广泛的 JUnit 版本，并且有成千上万的测试套件在 JUnit 4 上运行。

# 我们的第一个单元测试案例

下面是一个用 JUnit 编写的单元测试用例的例子。是的，就是这么简单:

```
@Test
**public void** testByteArrayEquals(){
    **byte**[] actual = **"junit"**.getBytes();
    **byte**[] expected = **"junit"**.getBytes();
    *assertArrayEquals*(**"bytes should be same"**, expected, actual);
}
```

为了更好地理解，我们来分解一下:

1.  JUnit 测试用例是用`@Test` 注释来注释的。这个注释向 JUnit 表明，这个方法不是普通的方法，必须作为 JUnit 测试来执行。
2.  接下来，我们有一组特定的语句。这些语句通常用于设置和调用我们正在测试的代码。
3.  最后，测试用例以断言语句结束。在我们的例子中，我们断言预期的和实际的结果。JUnit 为断言提供了过多的断言类型。在本文的后面，我们将看到其中的一些。

附加说明:

1.  JUnit 测试用例总是有一个`void` 返回类型。对你来说可能不是很有意义。在 JUnit 测试案例中，您正在对代码进行单元测试。您设置了前提条件，并调用了一段正在测试的代码。最后，用实际结果断言计算结果。因此，JUnit 测试用例没有什么可返回的。此外，这些测试用例是由 JUnit 框架调用的，因此返回值无论如何都会被忽略。
2.  历史上，JUnit 测试用例方法是从`test` 关键字开始的。因此，您应该将测试用例命名为`testGivenWhenThen` 格式的理想方式。这使得任何阅读你的代码的人都能理解测试用例是关于什么的

# 使用断言

T he 心 JUnit 测试用例是我们如何`assert` 预期的和实际的结果。因为这是几乎每个测试用例都会有的语句，所以 JUnit 提供了如此多不同的方法来断言不同的类型。这些断言方法中的大多数都出现在`org.junit.Assert` 类中。

所有的断言方法都是静态的，我们可以使用`Assert.methodName()`来断言。我们还可以使用 Java 的静态导入机制来去掉 Assert 类前缀。

例如:

```
*Assert.assertArrayEquals*(**"bytes should be same"**, expected, actual);
```

和

```
*assertArrayEquals*(**"bytes should be same"**, expected, actual);
```

两者都一样。但是，第二个语句更清晰，更容易阅读。

## 断言的类型

**断言整数数组相等**

```
@Test
**public void** testIntArrayEquals(){
    **int**[] actual = {1,2,3};
    **int**[] expected = {1,2,3};
    *assertArrayEquals*(**"Ints should be same"**, expected, actual);
}
```

上述方法确保两个数组相等。对于这个断言，我们使用了`assertArrayEquals` 。

**断言一个对象不为空**

```
@Test
**public void** testAssertNotNull(){
    *assertNotNull*(**"Should not be null"**, **new** String());
}
```

我们用`assertNotNull` 来断言一个对象不为空。

同理，我们还有其他的断言方法，比如`assertEquals` *、* `assertTrue` *、* `assertFalse` *、* `assertSame` *、*等等。

## **使用 Hamcrest 匹配器**

Hamcrest 提供了一些非常强大的匹配器类型来定义我们的断言。我们使用`assertThat` 断言方法来使用 Hamcrest 匹配器。该方法的语法如下:

```
assertThat(T actual, Matcher<? **super** T> matcher)
assertThat(String reason, T actual, Matcher<? **super** T> matcher)
```

Hamcrest 匹配器可以在`org.hamcrest.CoreMatchers`类中找到。以下是匹配器的几个例子。

使用`is` 匹配器:

```
**import static** org.junit.Assert.*assertThat*;
**import static** org.hamcrest.CoreMatchers.*is*;@Test
**public void** testAddition(){
    *assertThat*(**"Sum is not correct"**, 2+2, *is*(4));
}
```

在上面的例子中，我们使用了`is`匹配器。这个测试用例非常简单，非常容易阅读。听起来更像英语句子。

以下是更多 Hamcrest 匹配器的示例:

```
@Test
**public void** coreHamcrestMathers(){
    *assertThat*(**"good"**, *allOf*(*equalTo*(**"good"**), *startsWith*(**"go"**), *is*(**"good"**)));
    *assertThat*(**"good"**, *not*(*allOf*(*equalTo*(**"bad"**), *startsWith*(**"k"**), *is*(**"bad"**))));

    *assertThat*(**"bad"**, *anyOf*(*equalTo*(**"excellent"**), *equalTo*(**"bad"**)));
    *assertThat*(7, *not*(*either*(*equalTo*(3)).or(*equalTo*(4))));
}
```

匹配器比其他断言方法更容易阅读。例如，第一条语句读作:

> 断言“好”等于“好”的一切，并从“去”开始

在新的测试用例中，由于更好的可读性，建议使用`assertThat` 代替旧的`assert*`方法。

# 包扎

在本文中，我们介绍了如何用 Hamcrest 编写 JUnit 测试用例。这两个框架提供了无数其他有用的特性来编写高效的测试用例。要了解更多，请参考个人文档页面[这里](https://junit.org/junit4/) (JUnit)和[这里](http://hamcrest.org/JavaHamcrest/index) (Hamcrest)。