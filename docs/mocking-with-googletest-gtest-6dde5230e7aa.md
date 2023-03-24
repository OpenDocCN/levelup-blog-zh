# 用 GoogleTest (gtest)嘲讽

> 原文：<https://levelup.gitconnected.com/mocking-with-googletest-gtest-6dde5230e7aa>

## 用 C++进行良好的单元测试(第二部分)

在之前的一篇文章中，我介绍了用 gtest 进行测试的[基础。在这里，我们将研究模仿，这是许多用例的一个强大特性。](https://medium.com/gitconnected/introduction-to-googletest-gtest-1ece21f55d0e)

模拟——顾名思义——描述了模拟某些功能以简化单元测试:主要的例子是昂贵的、难以测试的功能，例如数据库访问、涉及睡眠、等待用户输入的功能等。我们没有将不合理的工作放到单元测试中，而是简单地替换(模仿)有问题的函数。这也有助于封装，并允许“真正的”单元测试，而不是集成测试。

![](img/d295b23ad687f810c230c34b0f2ebbb1.png)

[Freepik 上的 vectorjuice](https://www.freepik.com/free-vector/bug-fixing-software-testing-computer-virus-searching-tool-devops-web-optimization-antivirus-app-magnifier-cogwheel-monitor-design-element_12083098.htm#query=software%20testing&position=2&from_view=keyword) 图片

# 介绍性示例

我们将使用与上一篇文章类似的项目设置，使用 Bazel 作为构建系统——但是请随意使用您喜欢的任何东西:

**构建**

```
cc_library(
    name = "gtest_example",
    srcs = ["gtest_example.cc"],
    hdrs = ["gtest_example.h"],
    visibility = ["//visibility:public"],
    deps = [
        "@com_github_google_glog//:glog",
    ],
)

cc_test(
    name = "gtest_example_test",
    srcs = ["gtest_example_test.cc"],
    deps = [
        ":gtest_example",
        "@com_google_googletest//:gtest_main",
    ],
)
```

**gtest_exampe.h**

```
#pragma once

class ExampleClass {
 public:
  void mock_op();

 private:
  void foo();
};
```

**gtest_example.cc**

```
#include "gtest_samples/gtest_example.h"

#include <chrono>
#include <thread>

#include <glog/logging.h>

void ExampleClass::mock_op() { foo(); }

void ExampleClass::foo() {
  LOG(INFO) << "Calling foo ...";
  std::this_thread::sleep_for(std::chrono::seconds(10));
  LOG(INFO) << "... calling foo done.";
}
```

`ExampleClass`定义了一个公共函数`mock_op()`，内部调用私有函数`foo()`。粗略地看一下，我们假设这个函数是“有问题的”:在这个函数中，我们在返回之前要休眠 10 秒钟，这是我们不想在单元测试中做的事情→我们想要模拟它。

但是，首先，这里是常规的单元测试:

```
#include "gtest_samples/gtest_example.h"

#include <gmock/gmock.h>
#include <gtest/gtest.h>

TEST(ExampleClassTest, TestMockOp) {
  ExampleClass example = ExampleClass();
  example.mock_op();
}
```

# 模拟功能

现在，为了模仿`foo()`，我们在一个继承自`ExampleClass`的类中使用宏`MOCK_METHOD`:这将使 gtest 覆盖`ExampleClass`的`foo`，并且每当我们调用子类的`mock_op`时，我们将调用这个修补的函数(现在，它什么也不做):

```
#include "gtest_samples/gtest_example.h"

#include <gmock/gmock.h>
#include <gtest/gtest.h>

class ExampleClassMocked : public ExampleClass {
 public:
  MOCK_METHOD(void, foo, (), (override));
};

TEST(ExampleClassTest, TestMockOp) {
  ExampleClassMocked example = ExampleClassMocked();
  example.mock_op();
}
```

为了实现这些改变，我们需要将基类的`foo`变成虚拟的，因此也要声明一个虚拟析构函数:

```
#pragma once

class ExampleClass {
 public:
  virtual ~ExampleClass(){};

  void mock_op();

 private:
  virtual void foo();
};
```

运行时，我们看到单元测试立即结束，而不是休眠 10 秒钟。恭喜你，你嘲笑了你的第一个函数！

# 定制模拟功能

在这一节中，我们将加入更多的味道，看看如何测试被模仿的函数被正确调用，以及修改它的行为。

## 适当地调用检查函数

通过`EXPECT_CALL`可以检查被模仿的函数是否确实被调用。这是很常见的，因为这种单元测试通常会全面测试一个类的完整接口及其与其他接口的交互——我们希望确保调用了适当的函数。或者，我们可以指定我们期望函数被调用的频率:

```
TEST(ExampleClassTest, TestMockOp) {
  ExampleClassMocked example = ExampleClassMocked();
  EXPECT_CALL(example, foo).Times(1);
  example.mock_op();
}
```

我们可以进一步检查是否用正确的参数调用了一个函数。为此，我们扩展`foo`以期待一个参数:

**gtest_example.cc:**

```
#include "gtest_samples/gtest_example.h"

#include <chrono>
#include <thread>

#include <glog/logging.h>

void ExampleClass::mock_op() { foo(10); }

void ExampleClass::foo(int x) {
  LOG(INFO) << "Calling foo with ..." << x;
  std::this_thread::sleep_for(std::chrono::seconds(10));
  LOG(INFO) << "... calling foo done.";
}
```

**gtest_example_test.cc:**

```
#include "gtest_samples/gtest_example.h"

#include <gmock/gmock.h>
#include <gtest/gtest.h>

class ExampleClassMocked : public ExampleClass {
 public:
  MOCK_METHOD(void, foo, (int x), (override));
};

TEST(ExampleClassTest, TestMockOp) {
  ExampleClassMocked example = ExampleClassMocked();
  EXPECT_CALL(example, foo(10)).Times(1);
  example.mock_op();
}
```

## 从模拟函数返回值

此外，我们可以修改被模仿函数的行为。让我们稍微改变一下我们的设置:我们想要单元测试`mock_op`，它现在返回一个值——即`foo`的返回。为了跳过睡眠，但仍然达到正确的结果，我们告诉戏弄`foo`简单地返回预期:

**gtest_example.cc:**

```
#include "gtest_samples/gtest_example.h"

#include <chrono>
#include <thread>

#include <glog/logging.h>

int ExampleClass::mock_op() { return foo(); }

int ExampleClass::foo() {
  LOG(INFO) << "Calling foo ...";
  std::this_thread::sleep_for(std::chrono::seconds(10));
  LOG(INFO) << "... calling foo done.";
  return 10;
}
```

**gtest_example_test.cc:**

```
#include "gtest_samples/gtest_example.h"

#include <gmock/gmock.h>
#include <gtest/gtest.h>

class ExampleClassMocked : public ExampleClass {
 public:
  MOCK_METHOD(int, foo, (), (override));
};

TEST(ExampleClassTest, TestMockOp) {
  ExampleClassMocked example = ExampleClassMocked();
  EXPECT_CALL(example, foo()).Times(1).WillOnce(::testing::Return(10));
  EXPECT_EQ(example.mock_op(), 10);
}
```

如果我们想让这个例子更有趣/更真实，让我们改变代码 s.t. `foo`接受一个参数作为输入并返回它——相应地，我们修改单元测试中的预期返回:

**gtest_example.cc:**

```
#include "gtest_samples/gtest_example.h"

#include <chrono>
#include <thread>

#include <glog/logging.h>

int ExampleClass::mock_op(int x) { return foo(x); }

int ExampleClass::foo(int x) {
  LOG(INFO) << "Calling foo with ..." << x;
  std::this_thread::sleep_for(std::chrono::seconds(10));
  LOG(INFO) << "... calling foo done.";
  return x;
}
```

**gtest_example_test.cc:**

```
#include "gtest_samples/gtest_example.h"

#include <gmock/gmock.h>
#include <gtest/gtest.h>

class ExampleClassMocked : public ExampleClass {
 public:
  MOCK_METHOD(int, foo, (int x), (override));
};

TEST(ExampleClassTest, TestMockOp) {
  ExampleClassMocked example = ExampleClassMocked();
  EXPECT_CALL(example, foo(11)).Times(1).WillOnce(::testing::ReturnArg<0>());
  EXPECT_EQ(example.mock_op(11), 11);
}
```

# 将示例重构到专业软件级别

我们上面的例子希望有助于引入嘲讽，但这是不好的测试风格，也不会在专业软件项目中以这种形式使用。

单元测试应该只覆盖类的公共接口——我们测试类维护它们的契约，并隐藏内部实现。因此，我们不会模仿一个私有函数并测试它是否被正确调用(作为旁注，请注意上面看到的一点“好奇”:[在继承的类中改变函数的可见性实际上是正确的 C++](https://softwareengineering.stackexchange.com/questions/308158/why-does-the-overriding-rule-of-c-not-care-about-visibility-changes) )。

实际上，我们可能会模仿另一个类的公共函数——这带来了一些其他有趣的挑战/后果，这也是本节的目标。

因此，让我们首先重构我们的项目，以反映一个“更好”的设计(“更好”解释为:在专业项目中更容易看到——对于我们的玩具示例来说，将类分开实际上没有意义，但是如上所述，我们不会在实践中编写这样的单元测试):

**gtest_example.h**

```
#pragma once

class ExampleClassB {
 public:
  int foo(int x);
};

class ExampleClassA {
 public:
  ExampleClassA(const ExampleClassB& example_class);
  int mock_op(int x);

 private:
  ExampleClassB example_class_b_;
};
```

**gtest_example.cc**

```
#include "gtest_samples/gtest_example.h"

#include <chrono>
#include <thread>

#include <glog/logging.h>

ExampleClassA::ExampleClassA(const ExampleClassB& example_class_b)
    : example_class_b_(example_class_b){};

int ExampleClassA::mock_op(int x) { return example_class_b_.foo(x); }

int ExampleClassB::foo(int x) {
  LOG(INFO) << "Calling foo with ..." << x;
  std::this_thread::sleep_for(std::chrono::seconds(10));
  LOG(INFO) << "... calling foo done.";
  return x;
}
```

这是一种常见的模式:通过[组合](https://stackify.com/oop-concepts-composition/#:~:text=Composition%20is%20one%20of%20the,regularly%20in%20the%20real%20world.) `ExampleClassA`管理`ExampleClassB`的一个实例，在`mock_op`内部调用它的公共方法`foo`。

例如，这可以在主文件中使用:

```
#include <glog/logging.h>

#include "gtest_samples/gtest_example.h"

int main() {
  ExampleClassA example_class_a = ExampleClassA(ExampleClassB());
  LOG(INFO) << example_class_a.mock_op(10);
}
```

现在，我们又要嘲弄`foo`。作为第一次尝试，我们可以简单地这样做:

```
#include "gtest_samples/gtest_example.h"

#include <gmock/gmock.h>
#include <gtest/gtest.h>

class ExampleClassBMocked : public ExampleClassB {
 public:
  MOCK_METHOD(int, foo, (int x), (override));
};

TEST(ExampleClassTest, TestMockOp) {
  ExampleClassBMocked example_class_b = ExampleClassBMocked();
  ExampleClassA example_class_a = ExampleClassA(example_class_b);
  EXPECT_CALL(example_class_b, foo(11))
      .Times(1)
      .WillOnce(::testing::ReturnArg<0>());
  EXPECT_EQ(example_class_a.mock_op(11), 11);
}
```

但是由于 C++中的[切片](https://www.geeksforgeeks.org/object-slicing-in-c/)，这实际上会调用基类的‘方法，即`ExampleClassB::foo`！原因是，当进行赋值`example_class_b_(example_class_b)`时，会产生一个副本，我们最终得到的对象是类型`ExampleClassB`。这种模式通常是令人讨厌的，会导致一些奇怪和意想不到的行为，因此应该避免。

为了解决这个问题，我们改变属性`example_class_b_`来保存一个指针——这样就避免了切片的问题:

```
#pragma once

class ExampleClassB {
 public:
  virtual ~ExampleClassB(){};

  virtual int foo(int x);
};

class ExampleClassA {
 public:
  ExampleClassA(ExampleClassB* example_class);
  int mock_op(int x);

 private:
  ExampleClassB* example_class_b_;
};
```

```
#include "gtest_samples/gtest_example.h"

#include <chrono>
#include <thread>

#include <glog/logging.h>

ExampleClassA::ExampleClassA(ExampleClassB* example_class_b)
    : example_class_b_(example_class_b){};

int ExampleClassA::mock_op(int x) { return example_class_b_->foo(x); }

int ExampleClassB::foo(int x) {
  LOG(INFO) << "Calling foo with ..." << x;
  std::this_thread::sleep_for(std::chrono::seconds(10));
  LOG(INFO) << "... calling foo done.";
  return x;
}
```

```
#include "gtest_samples/gtest_example.h"

#include <gmock/gmock.h>
#include <gtest/gtest.h>

class ExampleClassBMocked : public ExampleClassB {
 public:
  MOCK_METHOD(int, foo, (int x), (override));
};

TEST(ExampleClassTest, TestMockOp) {
  ExampleClassBMocked example_class_b = ExampleClassBMocked();
  ExampleClassA example_class_a = ExampleClassA(&example_class_b);
  EXPECT_CALL(example_class_b, foo(11))
      .Times(1)
      .WillOnce(::testing::ReturnArg<0>());
  EXPECT_EQ(example_class_a.mock_op(11), 11);
}
```

## 观点

这就结束了对用 gtest 模仿的介绍。作为一个离开的评论，我想指出的是`[ExampleClassB](https://en.cppreference.com/w/cpp/language/abstract_class)`经常是一个抽象基类——这通常是由于所选软件设计的需要，并且进一步帮助组织所使用的类的关系。

我希望你喜欢这篇文章，如果你喜欢的话——希望你能回来看更多。