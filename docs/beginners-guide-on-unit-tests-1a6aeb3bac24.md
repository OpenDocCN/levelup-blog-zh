# 单元测试初学者指南

> 原文：<https://levelup.gitconnected.com/beginners-guide-on-unit-tests-1a6aeb3bac24>

## 什么，为什么，以及如何编写单元测试

![](img/ae78a018e21fa2c3acacd6a4332af0dd.png)

我将传递这一块，但谢谢你提交它！照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[agency followeb](https://unsplash.com/@olloweb?utm_source=medium&utm_medium=referral)拍摄

# 什么是单元测试

单元测试是基于[测试金字塔](https://martinfowler.com/bliki/TestPyramid.html)的最细粒度的测试类别。通常，它专注于类、函数或 UI 组件的功能，并与外部系统(如数据库和第三方 API)相隔离。

# 为什么要写单元测试呢

## 你的软件的安全网

大多数时候，编写/重构代码的挑战是确保不破坏现有的功能。在进行单元测试之前，开发人员需要以黑盒方式手动测试更改后的类/函数，以确保不会出错。手工操作容易出错。开发人员可能会忘记一些测试用例，有问题的代码会被交付到生产环境中。拥有单元测试并在您的部署管道上正确地配置它们将使您免于这样的场景。因此，这将增加您对发货到生产的信心。

## 尽早发现错误

单元测试是(也应该是)以一种隔离的方式编写的，因此它可以在无需启动外部服务和运行像[木偶师](https://github.com/puppeteer/puppeteer)这样的工具的情况下执行。与端到端测试相比，它运行速度更快，占用的内存更少。单元测试的这一独特属性使得开发人员在开发过程中尽可能多地执行测试成为可能。

一些测试运行程序，如 [jest](https://jestjs.io/) 提供了一个 watch 选项，每次代码有变化时运行测试，这使得在开发过程中发现错误更加容易。

## 证明文件

精心编写的测试可以作为文档，因为它描述了特定软件的期望行为。我还发现测试在代码审查过程中非常有帮助。它提供了关于软件行为的指导，并且消除了通过实现细节来理解功能的需要。

# 测试驱动开发简介

不提到测试驱动开发(TDD ),谈论单元测试是不完整的。简而言之，TDD 可以概括为软件开发的红绿重构方法。

1.  您从编写一个测试来覆盖一个需求开始。测试应该会失败，因为您没有系统的工作实现(红色)。
2.  您编写实现以使其通过测试(绿色)。
3.  重构您的代码(如有必要)。
4.  继续下一个需求，并回到步骤 1

TDD 的关键是让测试驱动你的设计，而不是相反。

# 编写单元测试的实践

我将使用用 javascript + jest 编写的示例，因为这是我最熟悉的语言和测试框架。例如，我们使用一个名为 *dateFormatter* 的类，规范如下:

*   这个类有一个名为 *format* 的公共函数，它接受 Javascript Date 对象作为输入，并返回一个 dd-mm-yyyy 格式的日期字符串。
*   如果输入是一个无效的日期对象，它将抛出一个异常。

## 编写有意义的测试名称

```
// Bad
test('format should format date correctly', function (){
  ...
})// Good
test('format should return date with dd-mm-yyyy format given a valid date object input', function (){
  ...
})
```

有意义的测试名称的黄金法则是输出和输入的清晰描述。读者应该能够理解期望的行为，而不必阅读被测系统的实现细节。

## 每个测试应该只涵盖一个场景

```
describe('DateFormatter', function() {
  // Bad
  test('format should return the date with following format:dd-mm-yyyy given valid date object and throw exception if the input is invalid date object', function(){
    ...
  }) // Good
  test('dateFormatter should return the date with following format:dd-mm-yyyy given valid date object', function() {
    ...
  })  test('dateFormatter should throw exception given invalid date object', function() {
    ...
  })
})
```

应该避免在一个测试中测试两个功能。这个原则背后的原因是，如果测试碰巧失败了，我们不知道哪个功能失败了。您将需要检查两个功能，即使其中只有一个碰巧没有通过测试。

## 使用 AAA 模式

Arrange，Act，Assert 模式是一种常见的模式，可以通过分离测试的 Arrange，Act 和 Assert 部分来提高测试的可读性，通常使用换行符。

*   Arrange 正在准备必要的设备、模型、存根和被测系统。
*   Act 正在执行测试中的功能
*   断言是针对期望值断言执行结果

```
describe('DateFormatter', function() {
  test('format should return the date with following format:dd-mm-yyyy given valid date object', function() {
    // Arrange
    const sut = DateFormatter();
    const date = new Date('2020-01-01');

    // Act 
    const result = sut.format(date); // Assert
    expect(result).toBe('01-01-2020')
  })
})
```

## 将你的单位从外部依赖中隔离出来

假设我们在这个类的基础上又增加了一个功能。每次执行 format 方法时，它都会使用一个名为*的函数将结果记录到第三方 API 中。*

```
import { logToExternalAPI } from './third-party-services'; class DateFormatter() {
  format(date) {
    ...
    logToExternalAPI(result);
    return result;
  }
}
```

我们如何为这个新功能编写测试呢？一种方法是重构类，并使用依赖注入来避免对另一个单元的直接依赖。然后，我们可以很容易地断言反对注入的模拟。使用这种技术，我们还通过将类从 logger 实现中分离出来来改进类的设计。

```
class DateFormatter {
  constructor(logger){
    this.logger = logger;
  } format(date) {
    ...
    this.logger(result);
    return result;
  }
}describe('DateFormatter', function(){
  test('format should call logger with the formatted given valid date object', function() {
    // Arrange
    const loggerMock = jest.fn();
    const sut = DateFormatter(loggerMock);
    const date = new Date('2020-01-01');

    // Act 
    sut.format(date); // Assert
    expect(loggerMock).toBeCalledWith('01-01-2020')
  })
})
```

## 避免测试实现细节

测试实现细节的例子如下:

*   断言函数调用序列
*   断言类的内部状态

应该避免测试实现细节，因为这会在测试和实现之间产生紧密耦合。例如，如果您编写测试来断言类的方法内部的函数调用序列，并且您决定更改顺序，则测试将会失败，即使它实际上并不影响类的用户。

## 处理遗留代码:我应该先重构还是先写测试？

看情况。在某些情况下，您想要使用的特定类/组件是以一种首先很难测试的方式编写的。

一般来说，我建议在重构之前写一个测试，除非很难做到。

# 结论

编写单元测试是一种重要且被广泛采用的实践，它通过提供一种确保软件正确性并允许开发人员尽早发现错误的方法来提高软件质量。

最后，这项技术(就像其他技术一样)需要一些练习和训练才能掌握。我希望这篇文章能给你一些关于如何为你的软件写好测试的基本想法。

编码快乐！