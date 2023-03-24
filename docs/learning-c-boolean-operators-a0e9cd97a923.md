# 学习 C++:布尔运算符

> 原文：<https://levelup.gitconnected.com/learning-c-boolean-operators-a0e9cd97a923>

![](img/e13d863a73122dd3f68a2d2311c2e5a4.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

布尔运算符用于组合关系表达式，以在 C++程序中执行更复杂的逻辑。在这篇文章中，我将解释如何在 C++中使用布尔运算符。

# 布尔运算符

C++中有三种布尔运算符:

`&&` —与
`||` —或
`!` —不

这些运算符用于组合关系表达式来执行复杂的逻辑运算。为了理解它们是如何工作的，让我们回顾一下这些运算符的真值表(欢迎你带你去上高中代数课，尽管我直到大学数学课才开始学习逻辑)。

真值表描述了用布尔运算符组合两个关系表达式的结果。以下是`&&`操作员的真值表:

真和真产生真
真和假产生假
假和真产生假
假和假产生假

例如，如果`x == 100`和`y == 100` ，那么这两个表达式的结果就是`true`。任何其他组合都会产生一个`false`结果。

以下是`||`运算符的真值表:

真或真产生真
真或假产生真
假或真产生真
假或假产生假

例如，如果`x == 100`或`y == 200`，那么两个表达式的结果就是`true`。产生错误结果的唯一组合是两个表达式都是`false`。

以下是`!`操作符的真值表:

非真产生假
非假产生真

例如，如果`x == 100`那么`!(x == 100)`产生一个`false`结果，如果`x != 100`那么`!(x!=100)`就是`true`。

# 使用布尔运算符

现在让我们看一些使用布尔运算符的例子。第一个例子是一个简单的汽车经销商贷款批准申请。要获得贷款，客户必须每年至少挣 20000 美元，而且他们必须从事同样的工作至少两年。下面是我们如何在 C++程序中表达这种逻辑:

```
#include <iostream>
using namespace std;int main ()
{
  bool approved;
  int salary;
  int yearsOnJob;
  cout << "What is the applicant's salary? ";
  cin >> salary;
  cout << "How many years have they been on the job? ";
  cin >> yearsOnJob;
  if (salary >= 20000) && (yearsOnJob >= 3) {
    approved = true;
  }
  else {
    approved = false;
  }
  if (approved) {
    cout << "Go ahead with the loan." << endl;
  }
  else {
    cout << "Explain to the customer why the loan was declined."
         << endl;
  }
  return 0;
}
```

下面是该程序的三次运行:

```
What is the applicant's salary? 25000
How many years have they been on the job? 5
Go ahead with the loan.What is the applicant's salary? 45000
How many years have they been on the job? 1
Explain to the customer why the loan was declined.What is the applicant's These examples demonstrate how the && operator works. For the result to be true, the customer must make at least $20000 and be on the job for at least 3 years. That is true for the first example but not for the other two, which is why the loan is declined in those cases.Now let's modify the problem to work with the || operator. The loan application approval terms change so that if the applicant makes at least $20000 or they have been on the job for more than 5 years, their loan application will be approved.Here is the program that uses this logic:salary? 19000
How many years have they been on the job? 5
Explain to the customer why the loan was declined.
```

这些例子演示了`&&`操作符是如何工作的。为了使结果真实，客户必须至少挣 20000 美元，并且至少工作 3 年。对第一个例子来说是这样，但对其他两个例子来说不是，这就是为什么贷款在这些情况下被拒绝。

现在让我们修改这个问题，使用`||`操作符。贷款申请批准条款发生了变化，如果申请人至少挣 20000 美元，或者他们已经工作了 5 年以上，他们的贷款申请将被批准。

下面是使用此逻辑的程序:

```
int main ()
{
  bool approved;
  int salary;
  int yearsOnJob;
  cout << "What is the applicant's salary? ";
  cin >> salary;
  cout << "How many years have they been on the job? ";
  cin >> yearsOnJob;
  if ((salary >= 20000) || (yearsOnJob > 5)) {
    approved = true;
  }
  else {
    approved = false;
  }
  if (approved) {
    cout << "Go ahead with the loan." << endl;
  }
  else {
  cout << "Explain to the customer why the loan was declined."
       << endl;
  }
  return 0;
}
```

下面是这个程序的几次运行，这样我们可以看到逻辑是如何工作的:

```
What is the applicant's salary? 21000
How many years have they been on the job? 3
Go ahead with the loan.What is the applicant's salary? 19500
How many years have they been on the job? 6
Go ahead with the loan.What is the applicant's salary? 19999
How many years have they been on the job? 4
Explain to the customer why the loan was declined.
```

在前两个例子中，满足了贷款的条件之一，因此贷款被批准。在最后一个例子中，贷款的两个条件都没有满足，所以贷款被拒绝。

# 短路

如果你记得前面的 Or 真值表，你会记得一个完整的布尔表达式为真，只有一个条件必须为真。C++编译器中内置了一个特殊的规则，如果用`||`运算符编写一个布尔表达式，并且第一个条件为真，则不会计算第二个条件，因为完整的表达式必须为真。

这个特性被称为短路，它为编译器提供了一个构建更高效程序的机会。在上面的`||`操作符程序中，每当第一个条件被评估为真时，编译器跳过评估第二个条件，直接进入下面的语句块。

当设计使用`||`操作符的布尔表达式时，考虑将最可能的条件放在第一位，以利用短路。

# 否定运算符

求反运算符(`!`)反转关系表达式的求值结果。换句话说，如果关系表达式为真，并且对表达式应用了求反运算符，则表达式的计算结果为假。如果关系表达式为假，并且对其应用了求反运算符，则求值的结果为真。

下面是一个在程序中使用`!`操作符的例子:

```
int main ()
{
  string password = "letmein";
  string pwd = "";
  while (!(pwd == password)) {
    cout << "Enter your password: ";
    cin >> pwd;
  }
  cout << "You are logged in." << endl;
  return 0;
}
```

请注意，关系表达式必须在一对括号中，然后应用`!`操作符。省略内括号会导致错误。

# 使用布尔运算符

布尔运算符使你能够在程序中使用更复杂的逻辑，你需要熟练使用它们。当使用布尔运算符时，在设计关系表达式以利用短路提供的效率时，一定要记住短路。

感谢您的阅读，请给我发电子邮件提出意见和建议。