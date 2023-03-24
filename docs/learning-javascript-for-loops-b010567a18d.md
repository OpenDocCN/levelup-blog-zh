# 学习 JavaScript: for 循环

> 原文：<https://levelup.gitconnected.com/learning-javascript-for-loops-b010567a18d>

![](img/7438e683705b95ac6e846494e3ade97c.png)

照片由[普里西拉·杜·普里兹](https://unsplash.com/@priscilladupreez?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本文中，我将介绍 JavaScript 中不同形式的`for`循环。有普通的`for`回路、`for..in`回路和`for..of` 回路。我将描述每个循环是如何工作的，以及何时是使用每个循环类型的正确时机。

我省略了`Array.forEach`循环，因为它是专门针对数组的，并且需要一些我还没有涉及到的函数知识。

# 通用 for 循环

我想讨论的第一种形式的`for`循环是大多数编程语言中标准的通用`for`循环。与`while` 循环相反，这种循环主要用在编写程序时迭代次数未知的情况下，例如当您处理文件中未知数量的数据或记录时。

通用 for 循环的语法模板是:

*for(循环变量初始化；条件；循环变量修改){
语句；
}*

循环变量是一个被初始化、测试、然后修改的变量，直到它导致条件变为假。下面是一个简单的`for`循环的例子，它打印数字 1 到 10:

```
for (let i = 1; i <= 10; i++) {
  putstr(i + " ");
}
```

这个程序的输出是:

```
1 2 3 4 5 6 7 8 9 10
```

让我们看看这个循环是如何处理数组的:

```
let names = ["Terri", "Meredith", "Allison", "Mason"];
for (let i = 0; i < names.length; i++) {
  putstr(names[i] + " ");
}
```

该程序输出:

```
Terri Meredith Allison Mason
```

循环体中可以有多个语句。以下程序接受用户的输入，并显示输入的 5 个数字的总和:

```
let total = 0;
let number = 0;
const numEntries = 5;
for (let i = 1; i <= numEntries; i++) {
  putstr("Enter a number: ");
  number = parseInt(readline());
  total += number;
}
print("The total is: " + total);
```

下面是这个程序运行一次的输出:

```
Enter a number: 1
Enter a number: 2
Enter a number: 3
Enter a number: 4
Enter a number: 5
The total is: 15
```

还有其他方法可以编写一般的`for`循环，尽管我不支持它们。例如，您可以在循环外初始化循环控制变量，这将变量的范围从块级更改为程序级。下面是一个使用 shell 的示例:

```
js> let i = 1;
js> for (; i <= 10; i++) {
      putstr(i + " ");
    }1 2 3 4 5 6 7 8 9 10 js> i
11js> for (let i = 1; i <= 10; i++) {
      putstr(i + " ");
    }
1 2 3 4 5 6 7 8 9 10 js> i
typein:4:1 ReferenceError: i is not defined
Stack:
@typein:4:1
js>
```

大多数情况下，您不希望 for 循环控制变量在循环本身之外可见。因此，您应该避免在循环体外部初始化循环控制变量。

您可以做的另一件事是通过将修改步骤放在循环体中来更改循环控制变量的修改位置，如下例所示:

```
js> for (let i = 1; i <= 5;) {
      putstr(i + " ");
      i++;
    }
1 2 3 4 5 5
js> for (let i = 1; i <= 5; i++) {
      putstr(i + " ");
    }
1 2 3 4 5 js>
```

这个程序的意义没有改变，但是将修改步骤放在循环体之外被认为是不标准的，你应该避免这样做。

# 森林..在回路中

`for..in`循环用于循环对象的属性。这个循环适用于数组以及更一般的对象，但是不应该用于数组；将用于..我将在下面解释。

下面是`for..in` 循环的语法模板:

*for(数据结构中的变量声明){
语句；
}*

下面是一个使用`for..in`循环显示一个简单对象的属性和值的例子:

```
js> let student1 = {
      name: "John Doe",
      id: 123,
      major: "Computer Science"
    }
js> for (const prop in student1) {
      print(student1[prop]);
    }
John Doe
123
Computer Science
```

虽然这种循环形式在 JavaScript 中是可用的，但大多数专家认为在大多数情况下不应该使用这种循环，尤其是对于数组，除了调试之外。原因是`for..in`循环不一定按照元素存储在数组中的顺序返回数组元素。如果顺序不重要，可以使用它，但是如果顺序很重要，就使用`for..of` 循环，我将在下面讨论。

# 森林..循环的

在讨论这种循环类型之前，我需要定义术语 *iterable* 。一个数据结构是可迭代的，如果你可以从结构的开始到结构的结尾从一个元素移动到另一个元素。数组是可迭代的；地图是可迭代的；像上面 student1 这样的对象是不可迭代的。在内部，使用迭代器遍历可迭代对象，我将在另一篇文章中讨论。

`for..of`循环是遍历可迭代对象(如数组或地图)的首选方式。这种循环类型从结构的第一个元素移动到最后一个元素，按照存储的顺序访问元素。

下面是`for..of`循环的语法模板:

*for(数据结构的变量声明){
语句；
}*

下面是一个使用数组的`for..of`循环的例子:

```
let numbers = [1,2,3,4,5];
for (const number of numbers) {
  putstr(number + " ");
}
```

这个程序的输出是:

```
1 2 3 4 5
```

这种类型的数组遍历优于使用一般的`for`循环，因为如果循环控制变量值赋值错误，很容易在数组开始之前或结束之后意外访问数组。

字符串也是可迭代的对象。下面是一个使用`for..of`循环单独显示单词字母的程序:

```
js> let word = "extraordinary";
js> for (const letter of word) {
      putstr(letter + " ");
    }
e x t r a o r d i n a r y js>
```

另一个使用`for..of`循环的可迭代 JavaScript 结构是地图，但是我还没有讨论过地图，我将把这个例子留到我关于地图的文章中。

# 学习循环很重要

要成为一名优秀的 JavaScript 程序员，你需要熟悉并习惯使用`for`循环，并知道何时以及为何使用每种形式。为了证明`for`循环的重要性，编程语言 Go(由 Google 开发)除了`for`循环之外没有任何其他循环类型，这使得选择循环类型变得很容易。`for`循环可以用于任何迭代问题，尽管`while`循环和`do while`循环有时从可读性的角度来看更有意义。

感谢您的阅读，请给我发电子邮件提出意见和建议。如果你对我的在线编程课程感兴趣，请访问[https://learningcpp.teachable.com](https://learningcpp.teachable.com)。