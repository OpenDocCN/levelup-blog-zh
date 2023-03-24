# 避免在代码中使用这样的 switch 语句

> 原文：<https://levelup.gitconnected.com/avoid-using-switch-statements-like-this-in-your-code-b3dedea66d9>

![](img/33792e45c2701ca92f9318633b65f0f3.png)

切换语句( ***或 if)..else…else if block for the matter***)是每个程序员都熟悉的任何编程语言中教授的最基本的概念。在这篇文章中，我们将学习这个简单的概念如何在你的源代码中被误用，导致一个糟糕的代码设计，以及我们如何修复它。我们走吧！！🚶

考虑一个简单的 OOP 应用程序，其中我们有一个`Employee`类。

```
class Employee
{
  ...............
  ...........

  function calculateSalary()
  {
    switch(this.type)
    {
      case EMPLOYED    :  return this.totalSalary - 10% of this.totalSalary;
      case FULL_TIME   :  return this.salary;
      case FLEXIBLE    :  return this.working hours * 10;
      default          :  throw new Exception();
    }
  }

  ................
  ........
}
```

您认为这段代码有什么问题吗？可能不是现在，对吗？

现在假设同一个类需要添加另一个函数来计算雇员的税款。

```
class Employee
{
  ...............
  ...........
  //somewhere in the same Employee class
  function calculateTax()
  {
    switch(this.type) // the same switch statement used here
    {
      case EMPLOYED    :  // return something
      case FULL_TIME   :  // return something else
      case FLEXIBLE    :  // return something else again
      default          :  throw new Exception();
    }
  }

  ................
  ........
}
```

在这个类中如何使用 switch 语句有几个问题。首先，显而易见的是:*方法中的代码重复*使得类变大了(当您必须添加新类型的雇员时，*或者将来会变大)。这个类中有明显的代码重复，因为同一个 switch 语句在多个方法中被重用。这里明显违反了***【SRP】***。*

第二，这个类也违反了 ***开闭原则(OCP)*** ，因为当你在应用程序中添加新类型的雇员时，你将不得不在两个不同的函数中做同样的改变:在`calculateTax`和`calculateSalary`方法中添加一个`case-return`对。当你需要在你的应用程序中删除一个雇员类型时也是如此，即从两个方法中删除`case-return`对。甚至不要让我开始思考在类`Employee`上怎么会有像`calculateEligibleLeave`和`calculateBonus`这样的方法可以一次又一次地重用同一个 switch 语句。😩 😩

引用罗伯特·马丁在他的名著 [**中的干净代码**](https://www.oreilly.com/library/view/clean-code-a/9780136083238/) :

> 我对 switch 语句的一般规则是，如果它们只出现一次，被用来创建多态对象，并且隐藏在继承关系之后，系统的其他部分看不到它们，那么它们是可以容忍的。

那么，有什么解决办法呢？解决方法就是简单的把 switch 语句埋在一个 ***抽象工厂*** 的地下室里。工厂将使用 *switch* 语句来创建`Employee`派生的适当实例，各种功能将通过`Employee`接口进行多态调度。

```
//a new class that is just responsible to take a employee and return an apropriate instance via its employee type
class EmployeeFactory
{
  public Employee makeEmployee(Employee emp)
  {
    switch(emp.type)
    {
      case EMPLOYED    :  return new EmployedEmployee();
      case FULL_TIME   :  return new FulltimeEmployee();
      case FLEXIBLE    :  return new FlexibleEmploye();
      default          :  throw new Exception();
  }
}
```

看到所有新的职业，像`EmployedEmployee`和`FulltimeEmployee`。他们现在要有方法:`calculateTax`和`calculateSalary`。

```
class EmployedEmployee extends Employee
{
  function calculateTax()
  {
    // return something
  }

  function calculateSalary()
  {
    return this.totalSalary - 10% of this.totalSalary;
  }
}
```

现在，在计算任何员工的税或工资时，只需这样做:

```
//a better solution
class Employee
{
  ...............
  ...........
  function calculateTax()
  {
    return EmployeeFactory.makeEmployee(this).calculateTax();
  }

  function calculateSalary()
  {
    return EmployeeFactory.makeEmployee(this).calculateSalary();
  }

  ................
  ........
} 
```

## 那么这段代码怎么比前面的好呢？

1.  该准则是否仍然违反*单一责任原则*？**否**。因为 switch 语句只负责一件事:返回一个雇员的多态实例，仅此而已。
2.  代码是否仍然违反*开闭原则(OCP)* ？**否**。因为当增加新员工时，我们必须在一个地方做出改变。在哪里？当然是在`EmployeeFactory`的`switch`语句里面。如果您的应用程序需要添加新类型的雇员，请在`EmployeeFactory`的`makeEmployee`方法中添加新的`case: return`对。像`EmployedEmployee`和`FullTimeEmployee`一样，为新员工类型添加一个新类。就是这样！不需要改变任何现有的类。

所以下次你写一个`switch`或者`if … else … elseif`语句的时候，试着想想*相同的 switch 语句*是否会被写在你源代码的另一个地方。如果是这样，你可能要考虑应用**抽象工厂设计模式**。

感谢您阅读文章并坚持到最后。😃

**参考文献**

[1]单一责任原则[https://en . Wikipedia . org/wiki/Single-respons ibility _ Principle](https://en.wikipedia.org/wiki/Single-responsibility_principle)

[2]开闭原理[https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle)

[3]抽象工厂模式[https://en.wikipedia.org/wiki/Abstract_factory_pattern](https://en.wikipedia.org/wiki/Abstract_factory_pattern)

[4]罗伯特·塞西尔·马丁的《干净的代码》