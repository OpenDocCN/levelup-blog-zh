# 学习 JavaScript:使用对象进行计算

> 原文：<https://levelup.gitconnected.com/learning-javascript-computing-with-objects-92328ce755f6>

![](img/c74b9cb5edb1eb39655b672c6dff0c2c.png)

埃里克·普劳泽特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

对象是 JavaScript 的一个重要部分，因为你在语言中做的任何事情都涉及到某种类型的对象。在本文中，我将讨论为什么基于对象的计算很重要，以及如何在 JavaScript 中使用对象进行计算。

# 你为什么需要物品

假设您正在教授一门 JavaScript 入门课程，这门课程有十名学生，您想记录他们的所有成绩，以便计算每个学生的平均成绩和全班的平均成绩。如果你需要处理的只是变量，那么每个学生的测试都需要一个变量，也就是说一个有三个测试的十个学生的班级需要三十个变量。

你可以把这个简化一点，把考试成绩放在一个列表里，但是你需要跟踪的其他信息呢，比如姓名、身份证号、专业等等？

解决方案是创建一个可以存储多种类型数据的复合数据类型。这就是 JavaScript 对象的用武之地。使用一个对象，你可以在一个对象中存储学生的名字，他们的 id 号，他们的成绩列表，以及你想要的任何其他数据。这将使您的数据保持有序，并使访问这些数据的程序更易于阅读、调试和提高效率。

# 如何创建 JavaScript 对象

在向您展示如何创建对象之前，让我定义几个术语。对象是定义一组属性及其值的一段数据。对象属性可以是任何 JavaScript 数据类型，大多数对象包含多种数据类型的属性。

使用以下语法模板声明对象:

*var | let object-name = {属性列表}；*

该语法指示您可以使用 var 关键字或 let 关键字来声明新对象。

接下来，我需要定义属性列表。以下是属性列表的语法模板:

*{
属性名:值，
属性名:值，
…
属性名:值
}*

在这个模板中有一些事情需要注意。首先，省略号表示可以为一个对象定义多个属性，您还需要注意，最后一个属性的值后面没有逗号。

下面是一个具有三个属性的学生对象的示例:

```
let student1 = {
  "name": "Paul McCartney",
  id: 123456,
  grades: [81, 83, 77, 92]
};
```

您也可以定义创建对象的函数:

```
function createStudent(name, id, grades) {
  return {
    name: name,
    id: id,
    grades: grades
  }
}
```

您可以像这样调用这个函数:

```
let student2 =
  createStudent("John Lennon", "234567", [81, 93, 74]);
```

您也可以通过调用函数来创建对象。这是一个创建学生对象的函数的例子，就像我一直在演示的那样，还有一个使用它的例子:

```
js> function createStudent(name, id, grades) {
      return {
        name: name,
        id: id,
        grades: grades
      };
    }
js> let stu3 = createStudent("Ringo Starr", 987654,
[91, 92, 94]);
js> stu3
({name:"Ringo Starr", id:987654, grades:[91, 92, 94]})
```

您可以像这样缩短函数定义:

```
function createStudent(name, id, grades) {
  return {
    name,
    id,
    grades
  };
}
```

这不仅缩短了函数定义，而且使定义更加清晰。使用与对象属性匹配的参数名是很常见的，阅读函数定义的人看到这样的代码可能会感到困惑:

```
name: name,
id: id,
grades: grades
```

这不仅缩短了函数定义，而且使定义更加清晰。使用与对象属性匹配的参数名是很常见的，阅读函数定义的人看到这样的代码可能会感到困惑:

# 如何访问对象属性

有两种方法可以访问存储在对象中的属性。第一种技术是通过使用`[]`操作符来使用“数组符号”。以下是这项技术的语法模板:

*对象名称[“属性名称”]*

下面是一个演示这种技术的简短程序:

```
let student1 = {
  name : "Paul McCartney",
  id : "123456",
  grades : [81, 77 , 91, 85]
};
print("Student1's name: " + student1["name"]);
print("Student1's id: " + student1["id"]);
print("Student1's grades: " + student1["grades"]);
```

以下是该程序的输出:

```
Student1's name: Paul McCartney
Student1's id: 123456
Student1's grades: 81,77,91,85
```

访问对象属性的另一项技术是使用点运算符。以下是这项技术的语法模板:

*对象名称.属性名称*

下面是上面用点运算符重写的提供属性值访问的程序:

```
let student1 = {
  name : "Paul McCartney",
  id : "123456",
  grades : [81, 77, 91, 85]
};
print("Student1's name: " + student1.name);
print("Student1's id: " + student1.id);
print("Student1's grades: " + student1.grades);
```

# 在对象的属性上循环

您可以使用特定版本的 JavaScript `for`循环，即`for-in`循环来访问对象的属性值。下面是这种类型的`for-in`循环的语法模板:

*for(对象中的键声明){
语句；
}*

让我们对`student1`对象使用这个循环:

```
for (let property in student1) {
  print(property + ": " + student1[property]);
}
```

这段代码的输出是:

```
name: Paul McCartney
id: 123456
grades: 81,77,92,80
```

当您以这种方式访问对象的属性时，您必须使用数组表示法来提取值。如果您尝试使用点运算符，您将得到每个属性值的`undefined`。

对于这个对象，您可以使用常规数组表示法来访问学生的成绩。这是一个计算学生平均成绩的程序:

```
js> let student1 = {
      name: "George Harrison",
      id: 234567,
      grades: [83, 75, 91, 98]
    };
js> let total = 0;
js> for (let i = 0; i < student1.grades.length; i++) {
      total += student1.grades[i];
    }
347
js> let average = total / student1.grades.length;
js> print(average)
86.75
```

# 对象属性可以是函数

如果需要，可以将对象属性定义为函数。例如，如果我们想让`student`对象报告它的平均成绩，我们可以将该属性写成一个函数，这样它就可以从 grades 属性中的当前成绩列表中计算出平均值。

下面是用这样一个函数重写的`student`对象，以及一个简短的测试程序:

```
function createStudent(name, id, grades) {
  return {
    name,
    id,
    grades,
    gradeAverage: function() {
      let total = 0;
      for (const grade of grades) {
        total += grade;
      }
      return total / grades.length;
    }
  };
}
let stu4 = createStudent("George Harrison", 456123, [88, 87, 90]);
print(stu4.name + " " + stu4.id + " " + stu4.grades);
print(stu4.gradeAverage());
```

这个程序的输出是:

```
George Harrison 456123 88,87,90
88.33333333333333
```

# 还有更多

关于 JavaScript 中基于对象的编程，我还有很多可以说的，我将在以后的文章中介绍。现在，如果您不熟悉对象，请熟悉这种编程范式，您可以使用它进入更高级的对象编程形式。

感谢您的阅读，请发送电子邮件至[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)向我提出意见和建议。如果您对我的在线编程课程感兴趣，请访问我的课程网站—【https://learningcpp.teachable.com 。