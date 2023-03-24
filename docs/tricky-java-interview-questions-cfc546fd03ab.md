# 棘手的 Java 面试问题

> 原文：<https://levelup.gitconnected.com/tricky-java-interview-questions-cfc546fd03ab>

## 你知道答案吗？

![](img/25c49ea3c5bebfc3870a4b32d873bc33.png)

照片由[布鲁斯·马尔斯](https://unsplash.com/@brucemars?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

根据 2019 年 stack overflow 开发者调查结果显示，Java 在最受欢迎的编程语言中排名第四。根据[事实上](https://www.indeed.com/)的说法，Java 是美国要求第二高的编程语言。因为这种受欢迎程度，你可以理解 Java 的就业市场竞争非常激烈。面试官经常会问应聘者一些刁钻的问题来测试他们的 Java 知识。所以你必须准备好回答这些问题，确保你的工作职位。

在本文中，您可以了解 Java 面试中一些棘手问题的答案。

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev) 

## Q1——给定 Java 代码的输出是什么？

```
public class Test { public static void main(String[] args) {
  method(null);
 }
 public static void method(Object o) {
  System.out.println("Object method");
 }
 public static void method(String s) {
  System.out.println("String method");
 }}
```

## 回答

它将打印“字符串方法”。首先，null 不是 Java 中的对象。但是我们知道，在 Java 中，我们可以将 null 赋给任何对象引用类型。Java String 也是类`java.lang.String`的对象。这里，Java 编译器选择用最具体的参数调用重载方法。因为字符串类比对象类更具体。

## Q2——给定 Java 代码的输出会是什么？

```
public class Test{public static void main(String[] args){
  Integer num1 = 100;
  Integer num2 = 100; if(num1==num2){
   System.out.println("num1 == num2");
  }
  else{
   System.out.println("num1 != num2");
  }
 }
}
```

## 回答

它将打印“num1 == num2”。每当使用“==”比较两个不同的对象引用时，值总是“false”但是在这里，由于整数缓存，num1 和 num2 是自动装箱的。因此`num1==num2`返回“真”。整数缓存仅适用于-128 到 127 之间的值。

## Q3 —垃圾收集如何防止 Java 应用程序内存不足？

## 回答

Java 垃圾收集器不会阻止 Java 应用程序内存不足。当一个对象超出范围并且不再需要时，它只是清理未使用的内存。因此，垃圾收集不能保证防止 Java 应用程序内存不足。

## Q4—Java 是“按引用传递”还是“按值传递”？

## 回答

Java 总是“按值传递”。然而，当我们传递一个对象的值时，我们传递的是对它的引用，因为变量存储的是对象引用，而不是对象本身。但这不是“引用传递”这可能会让初学者感到困惑。

## Q5 —下面的代码创建了多少个字符串对象？

```
public class Test{
 public static void main(String[] args){
   String s = new String("Hello World");
 }
}
```

## 回答

创建了两个字符串对象。当使用 new 操作符创建 String 对象时，如果 Java String 池中不存在该对象，将首先在其中创建该对象，然后在堆内存中创建。你可以在我下面的文章中简单地了解 Java 字符串。

[](https://medium.com/javarevisited/string-stringbuilder-and-stringbuffer-do-you-know-the-difference-6a53429dcf) [## String、StringBuilder 和 StringBuffer 你知道区别吗？

### Java 字符串完全指南

medium.com](https://medium.com/javarevisited/string-stringbuilder-and-stringbuffer-do-you-know-the-difference-6a53429dcf) 

## Q6 —以下 Java 代码的输出是什么？

```
public class Test{
 public static void main(String[] arr){
    System.out.println(0.1*3 == 0.3);
    System.out.println(0.1*2 == 0.2);
 }
}
```

## 回答

第一个 print 语句打印“false”，第二个打印“true”。这仅仅是因为浮点数中的舍入误差。只有 2 的幂的数字才能用简单的二进制表示精确地表示出来。不对应于 2 的幂的数字必须四舍五入以适合有限的位数。这里，因为 Java 使用 double 来表示十进制值，所以只有 64 位可用于表示数字。因此，`0.1*3`不等于`0.3`。

## 问题 7——在 Java 中有可能覆盖或重载静态方法吗？

## 回答

可以重载静态 Java 方法，但不可能覆盖它们。您可以在子类中编写另一个具有相同签名的静态方法，但是它不会覆盖超类方法。Java 里叫方法隐藏。

## Q8 —测试两个 double 值是否相等的最可靠方法是什么？

## 回答

确定两个 double 值是否相等的最可靠、最准确的方法是使用`Double.compare()`，并针对 0 进行测试。

`Double.compare(d1, d2) == 0`

## Q9 —如果 try 或 catch 块执行 return 语句，是否会执行 finally 块？

## 回答

是的，即使在 try 或 catch 块中执行了 return 语句，finally 块仍将被执行。这是一个非常流行和棘手的 Java 问题。我们阻止 finally block 被执行的唯一方法是使用`System.exit()`方法。

## Q10 —当我们运行下面的 Java 代码时会发生什么？

```
public class Test{ 
 public static void main(String[] args){ 
  System.out.println("main method");
 } 
 public static void main(String args){ 
  System.out.println("Overloaded main method");
 } 
}
```

## 回答

它打印“主方法”。因为 main 方法可以在 Java 中重载，所以不会出现错误或异常。它必须从 main 方法中调用，才能像任何其他方法一样执行。

本文到此为止。我希望你学到了新东西。如果您想阅读更多类似本文的 Java 文章，请查看下面的内容。

感谢您的阅读和快乐编码！

[](https://manushacheti.medium.com/membership) [## 通过我的推荐链接加入 Medium-Manusha Chethiyawardhana

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

manushacheti.medium.com](https://manushacheti.medium.com/membership) 

# 了解更多信息

[](/how-to-use-generics-in-java-aeca75d03a6c) [## 如何在 Java 中使用泛型

### Java 泛型完全指南

levelup.gitconnected.com](/how-to-use-generics-in-java-aeca75d03a6c) [](/how-to-use-synchronization-in-java-aed6758aeff6) [## 如何在 Java 中使用同步

### 同步 Java 代码的完整指南

levelup.gitconnected.com](/how-to-use-synchronization-in-java-aed6758aeff6) [](/new-and-exciting-features-of-java-15-you-should-know-about-d23c1c175176) [## 您应该了解的 Java 15 的令人兴奋的新特性

### Java 15 中新特性的指南及代码示例

levelup.gitconnected.com](/new-and-exciting-features-of-java-15-you-should-know-about-d23c1c175176) 

# 参考

*   [十大棘手的 Java 面试问答](https://www.java67.com/2012/09/top-10-tricky-java-interview-questions-answers.html)
*   [甲骨文 Java 文档](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/javadoc.html#:~:text=Description,constructors%2C%20methods%2C%20and%20fields.)

# 分级编码

感谢您成为我们社区的一员！[订阅我们的 YouTube 频道](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1)或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## Python | Skilled.dev 中的编码面试课程

### 掌握编码面试的过程

技术开发](https://skilled.dev)