# 成为一名更好的程序员必须忘记的 4 件事

> 原文：<https://levelup.gitconnected.com/4-things-you-have-to-unlearn-to-become-a-better-programmer-547adf476445>

*摘下编码训练轮！*

![](img/03d30979c81aa98a1f9c13363fe76b1a.png)

丹尼尔·施鲁迪在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我的搭档最近参加了一个虚拟编码训练营。当我看着她开始学习她的第一堂编程课时，我惊讶地发现我在学校学到的东西有多少并没有转化到工作中。

在本文中，我们将确定四种编程结构，它们在您第一次发现编程是如何工作的时候可能是基础，但在您的下一段旅程中会让您慢下来。

# 忘记循环

作为计算机编程的学生，循环是你最先学习的构造之一。一个简单的 while 循环展示了自动化的强大功能，您可以重复执行一组指令，直到满足某个条件。

每当看到一组项目时，您可能会尝试使用 while 循环或 for 循环，但这很少是最好的方法。

```
const groceries = [
  {
    name: 'Face Masks',
    price: 17.50,
  },
  {
    name: 'Disinfecting Wipes',
    price: 24.99,
  },
  {
    name: 'Goggles',
    price: 8.99,
  },
  {
    name: 'Gloves',
    price: 25.99,
  },
  {
    name: 'Hand Sanitizers',
    price: 24.99,
  },
];
```

例如，给定一个对象数组，其中每个对象代表一个杂货项目。如果你想打印出每件杂货的名称，你会怎么做？

```
let index = 0;while (index < groceries.length) {
  console.log(groceries[index].name);
  index = index + 1;
}
```

这个 while 循环当然实现了您想要的东西，但是您必须跟踪一个索引，以便在每次您想要获取一个项目时能够进入杂货阵列。如果忘记增加索引，还有进入无限循环的风险。

考虑这种替代方法:

```
groceries.forEach((item) => {
  console.log(item.name);
});
```

[forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) 是一个[高阶函数](https://en.wikipedia.org/wiki/Higher-order_function)，它接受另一个函数作为参数，并为数组中的每个元素执行一次所提供的函数。通过利用`forEach`，我们删除了使用索引来跟踪和访问数组的无关代码，并放大了我们的逻辑——打印出每个杂货项目的名称。

While 和 for 循环通常过于宽泛，无法捕捉我们代码的意图。如果你想写更好的代码，从用更具体的函数替换循环开始。

下面是几个更相关的列表变异和列表汇总的例子。

```
// Before:let index = 0;
const prices = [];while (index < groceries.length) {
  prices.push(groceries[index].price);
  index = index + 1;
}// After:groceries.map((item) => {
  return item.price;
});
```

```
// Before:let index = 0;
let total = 0;while (index < groceries.length) {
  total = total + groceries[index].price;
  index = index + 1;
}// After:groceries.reduce((sum, item) => {
  return sum += item.price;
}, 0);
```

# 忘记条件句

每次添加一条 else 语句，代码的复杂性就会增加两倍。像 if-else 和 switch 语句这样的条件结构是编程领域的基础。但是当您想要编写干净的、可扩展的代码时，它们也会成为障碍。

幸运的是，有一些技巧可以摆脱条件句。

## 数据结构

考虑以下计算折扣的函数:

```
const discount = (amount, code) => {
  switch (code) {
    case 'DIJFNC':
      return amount * 0.80;
    case 'XPFJVM':
      return amount * 0.75;
    case 'FJDPCX':
      return amount * 0.50;
  }
};
```

每次要添加新的折扣代码时，都必须在 switch 语句中添加新的 case。如果你犯了一个错误，你就破坏了整个计算。

现在让我们用一个对象替换条件。

```
const DISCOUNT_MULTIPLIER = {
  'DIJFNC': 0.80,
  'XPFJVM': 0.75,
  'FJDPCX': 0.50,
};const discount = (amount, code) => {
  return amount * DISCOUNT_MULTIPLIER[code];
};
```

这种重写有效地将数据从核心计算逻辑中分离出来，使得独立修改数据变得更加容易。

## 多态性

替代条件句的第二种方法是利用面向对象编程语言的一个关键特性——多态性。

```
const checkout = (amount, paymentMethod) => {
  switch (paymentMethod) {
    case 'credit-card':
      // Complex code to charge ${amount} to the credit card.
      break;
    case 'debit-card':
      // Complex code to charge ${amount} to the debit card.
      break;
    case 'cash':
      // Complex code to put ${amount} into the cash drawer.
      break;
  }
};const customers = [
  {
    amount: 75.00,
    paymentMethod: 'credit-card',
  },
  {
    amount: 50.00,
    paymentMethod: 'debit-card',
  },
  {
    amount: 25.00,
    paymentMethod: 'cash',
  },
];customers.forEach(({ amount, paymentMethod }) => {
  checkout(amount, paymentMethod);
});
```

用于处理各种支付方式的代码行穿插在 switch 语句中，使得代码难以阅读。雪上加霜的是，任何时候想要修改特定支付方法的逻辑，都有破坏其他两个方法的风险，因为它们都存在于同一个函数中。

```
class CreditCardCheckout {
  static charge(amount) {
    // Complex code to charge ${amount} to the credit card.
  }
}class DebitCardCheckout {
  static charge(amount) {
    // Complex code to charge ${amount} to the debit card.
  }
}class CashCheckout {
  static charge(amount) {
    // Complex code to put ${amount} into the cash drawer.
  }
}const customers = [
  {
    amount: 75.00,
    paymentMethod: CreditCardCheckout,
  },
  {
    amount: 50.00,
    paymentMethod: DebitCardCheckout,
  },
  {
    amount: 25.00,
    paymentMethod: CashCheckout,
  },
];customers.forEach(({ amount, paymentMethod}) => {
  paymentMethod.charge(amount);
});
```

多态帮助我们分解冗长的 switch 语句。每个类只负责一种支付方式。

> 这里需要注意的一点是，即使我们成功地从重构后的代码中移除了条件语句，实际上，我们只是将选择支付方式的决策转移到了上游。创建客户的人现在负责分配付款方式类别。

# 忘记文字变量名

编程教程充斥着糟糕的变量和函数名，部分原因是代码示例通常不需要提供完整的上下文来说明它试图解释的任何一点。例如:

```
const arr = [
  'Breakfast Cereal',
  'Candy and Snack',
  'Dairy',
  'Paper Products and Cleaning Supplies',
];const func = (n) => {
  const i = arr.findIndex(i => i === n);
  console.log(i);
};func('Dairy');
```

这段代码很好地演示了 [findIndex](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex) 的用法，但是完全不适用于实际项目。变量名没有告诉我们它们为什么存在。对于读者来说很难，当我们不知道它们的目的是什么时，修改就更难了。

要想写出更好的名字，就得从了解上下文开始。有一个常见的杂货店过道名称列表。通道名以一种有意义的方式排序，这样当我们用一个给定的通道名调用函数时，我们将返回它在这个列表中的位置，代表通道号。我们可以将代码重写如下:

```
const aisles = [
  'Breakfast Cereal',
  'Candy and Snack',
  'Dairy',
  'Paper Products and Cleaning Supplies',
];const printAisleNumber = (name) => {
  const number = aisles.findIndex((aisleName) => {
    return aisleName === name;
  }); console.log(number);
};printAisleNumber('Dairy');
```

当您想到正确的变量名时，这表明您真正掌握了代码所解决问题的上下文。有目的的变量名将把你的代码从单纯的计算指令提升为用户指南，帮助读者了解你的工作。

# 忘记全局范围

当你第一次接触编程时，你可能会从一个简单的`hello world`程序开始。从那里，你学习在一个文件中写代码，并观察程序从上到下一行一行地执行你的代码。你永远不会想到你在文件开头声明的变量会在别的地方变得不可用。

你所写的一切都生活在一个所有人都可以访问的全球空间中——这是有效进行抽象工作的一个障碍。您没有动力创建封装代码的抽象。

我的建议是忘记全球范围。将每个函数、对象和类视为一个新的宇宙。关注如何创建摘要来表达你的想法，以及这些想法是如何相互作用的。

# TL；速度三角形定位法(dead reckoning)

*   用**高阶函数**替换**循环**
*   将**条件**替换为**数据结构**和**多态**
*   用**有目的的变量名**替换**文字变量名**
*   忘记**全局范围**的存在

# 其他提示

[](https://codeburst.io/8-rules-for-coding-with-style-25ae4c11f22d) [## 用风格编码的 8 条规则

### 改进编码风格的技巧

codeburst.io](https://codeburst.io/8-rules-for-coding-with-style-25ae4c11f22d) [](/how-to-improve-your-coding-skills-through-exercises-5f38d969a6a4) [## 如何通过练习提高你的编码技能

### 通过有意识的练习提升你的技能。

levelup.gitconnected.com](/how-to-improve-your-coding-skills-through-exercises-5f38d969a6a4) [](/3-coding-practices-for-solving-the-right-problem-526b188241a2) [## 解决正确问题的 3 种编码实践

### 解决错误的问题往往比构建错误的解决方案代价更高。

levelup.gitconnected.com](/3-coding-practices-for-solving-the-right-problem-526b188241a2) 

# 分级编码

感谢您成为我们社区的一员！ [**订阅我们的 YouTube 频道**](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1) 或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题

### 掌握编码面试的过程

技术开发](https://skilled.dev)