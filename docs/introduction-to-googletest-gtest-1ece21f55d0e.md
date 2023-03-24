# GoogleTest (gtest)简介

> 原文：<https://levelup.gitconnected.com/introduction-to-googletest-gtest-1ece21f55d0e>

## 用 C++进行良好的单元测试(第一部分)

单元测试对于任何好的软件项目都是必不可少的。在这篇文章中，我不想激发或介绍单元测试本身，而是希望读者熟悉基础。相反，这篇文章重点介绍了 [GoogleTest (gtest)](http://google.github.io/googletest/) 框架——这是许多人用 C++编写单元测试的首选框架。我发现它类似于 [Python 的 pytest](https://medium.com/@hrmnmichaels/unit-testing-with-pytest-5c59cdf89529) :易于使用，但功能强大。

![](img/d295b23ad687f810c230c34b0f2ebbb1.png)

[Freepik 上的 vectorjuice](https://www.freepik.com/free-vector/bug-fixing-software-testing-computer-virus-searching-tool-devops-web-optimization-antivirus-app-magnifier-cogwheel-monitor-design-element_12083098.htm#query=software%20testing&position=2&from_view=keyword) 图片

在这里，我们将通过一些实际的例子来介绍 gtest，并深入其中:我们使用 [Bazel](https://bazel.build/) 作为我们项目的构建系统，但是我想鼓励读者使用他们熟悉的任何东西。因此，我们的项目包含 4 个文件:

*   构建(bazel 构建文件)
*   gtest_example.h /。cc(样本类的头文件/正文文件)
*   gt est _ example _ test . cc(gt est 测试文件)

让我们从实现一个简单的加法函数开始，为上面提到的文件生成以下文件内容，测试文件将在后面填充:

**构建**

```
cc_library(
    name = "gtest_example",
    srcs = ["gtest_example.cc"],
    hdrs = ["gtest_example.h"],
    visibility = ["//visibility:public"],
    deps = [
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

**gtest_example.h**

```
#pragma once

class ExampleClass {
 public:
  int sum(int summand1, int summand2);
};
```

**gtest_example.cc**

```
#include "gtest_samples/gtest_example.h"

int ExampleClass::sum(int summand1, int summand2) { return a + b; }
```

在我们的单元测试文件中，我们只需要包含正确的 gtest 头文件，并且可以用`EXPECT_EQ`检查两个值是否相等，从而测试正确的加法结果:

```
#include "gtest_samples/gtest_example.h"

#include <gtest/gtest.h>

TEST(ExampleClassTest, TestSum) {
  ExampleClass example = ExampleClass();
  EXPECT_EQ(example.sum(1, 2), 3);
}
```

`TEST`的第一个参数是测试套件的名称(描述多个测试集合的通用名称)，第二个参数是这个测试用例中具体测试运行的名称。

我们可以通过`bazel test gtest_samples:gtest_example_test`来运行这个测试

## 固定装置

如果我们像上面一样编写多个测试，我们会发现自己需要重新输入几行代码——在本例中是`example`的声明和实例化。在这里，夹具来拯救:它们允许定义基本测试类，为所有实现的测试用例共享`SetUp()`和`TearDown()`代码。公平地说，我们的一行类声明/实例化可能还不能证明 fixtures 是正确的——但是我希望这个原则变得清晰。

让我们引入一个除法函数，用这样的 fixture 重写上面的测试。我们给`ExampleClass`增加如下除法功能:

```
float ExampleClass::divide(float dividend, float divisor) {
  CHECK_NE(divisor, 0) << "Can't divide by 0!";
  return dividend / divisor;
}
```

注意，`CHECK_NE`是 Google 日志模块的一部分，因此需要`#include <glog/logging.h>`，以及构建文件中的依赖关系`@com_github_google_glog//:glog`。

现在我们修改测试文件如下:

```
#include "gtest_samples/gtest_example.h"

#include <gtest/gtest.h>

class GTestExampleTest : public ::testing::Test {
 protected:
  void SetUp() override { example_ = ExampleClass(); }
  void TearDown() override {}

  ExampleClass example_;
};

TEST_F(GTestExampleTest, TestSum) { EXPECT_EQ(example_.sum(1, 2), 3); }
TEST_F(GTestExampleTest, TestDivision) {
  EXPECT_EQ(example_.divide(1, 2), 0.5);
}
```

我们注意到，使用 fixture 的测试是用关键字`TEST_F`定义的，第一个参数是测试的基类的名称。在我们的示例中，我们在`SetUp()`步骤中实例化了一个示例类，但是将`TearDown()`留空。在这里，我们可以添加每次测试后运行的代码，比如清理磁盘上的文件。

## 检查终止

聪明的是，我们添加了一个检查来防止除数为 0。但是我们如何在单元测试中验证正确的行为呢？为此，我们可以使用`EXPECT_DEATH` —该函数验证流程是否如预期的那样终止，并产生预期的错误消息:

```
TEST_F(GTestExampleTest, TestDivision) {
  EXPECT_EQ(example_.divide(1, 2), 0.5);
  EXPECT_DEATH(example_.divide(1, 0), "Can't divide by 0!");
}
```

## 自定义比较运算符

让我们定义一个自定义结构，一个返回它的函数，以及一个检查返回的结构是否正确的单元测试:

**gtest_example.h**

```
#pragma once

struct CustomStruct {
  int value;
};

class ExampleClass {
 public:
  int sum(int summand1, int summand2);
  float divide(float dividend, float divisor);
  CustomStruct custom_op(int x);
};
```

**gtest_example.cc**

```
CustomStruct ExampleClass::custom_op(int x) { return CustomStruct{.value = x}; }
```

**gtest_example_test.cc**

```
bool operator==(const CustomStruct& lhs, const CustomStruct& rhs) {
  return lhs.value == rhs.value;
}

TEST_F(GTestExampleTest, TestCustomOp) {
  EXPECT_EQ(example_.custom_op(10), CustomStruct{.value = 10});
}
```

注意为`CustomStruct`实现一个定制的`==`操作符的必要性，否则 gtest 不能检查相等性。

这就完成了对 gtest 的基本介绍。我希望你已经发现这是有用的。感谢您的阅读。

在下一篇文章中，我们将探讨更高级的话题，比如嘲讽。