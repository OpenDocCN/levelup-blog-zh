# 学习 JavaScript:队列数据结构

> 原文：<https://levelup.gitconnected.com/learning-javascript-the-queue-data-structure-152dd0f2fde>

![](img/a9e969f4fe9010d5a9c355f2c610a2cc.png)

[澳门图片社](https://unsplash.com/@macauphotoagency?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在上一篇文章中，我讨论了堆栈数据结构。栈是数据结构的一个例子，其中最后进入栈的数据是第一个从栈中出来的数据。我用的一个例子是自助餐厅里的一堆托盘，顾客只能从上面拿走一个托盘，洗碗机也只能把一个干净的托盘放在上面。换句话说，一切都发生在栈顶。

另一方面，队列数据结构就像你在杂货店排队一样。排队时，你从后面排队，从前面排队。因此，进入队列的第一个数据就是离开队列的第一个数据。这是队列和堆栈的主要区别。

像堆栈一样，队列只有一些操作。队列实现对这些操作使用相似的名称。向队列中添加新数据是一种推送操作，从队列中删除数据是一种弹出操作。与只能查看堆栈顶部的堆栈不同，使用队列可以查看队列的前面和后面。

了解了这些背景知识之后，让我们来看看 JavaScript 队列实现。

# 设计队列类

我已经定义了四个`Queue`类行为:`push`、`pop`、`front`和`back`。我需要一个方法来告诉我队列中有多少元素——`size`方法。我需要一个方法来测试队列是否为空——`empty`方法，并且我需要一个方法来从队列中移除所有元素(当然你已经在杂货店排队并且在轮到你之前关闭了收银机)—`clear`方法。

我还需要决定使用什么数据结构来存储我的队列元素。对于这个实现，我将使用一个数组。

下面是我的`Queue`类定义:

```
class Queue {
  constructor() {
    this.dataStore = [];
  } push(element) {
    this.dataStore.push(element);
  } pop() {
    this.dataStore.shift();
  } front() {
    return this.dataStore[0];
  } back() {
    return this.dataStore[this.dataStore.length-1];
  } empty() {
    return this.dataStore.length == 0;
  } size() {
    return this.dataStore.length;
  } clear() {
    while (this.dataStore.length != 0) {
      this.dataStore.pop();
    }
  }
}
```

下面是一个测试这个定义的简短程序:

```
let line = new Queue()
line.push("Mike");
line.push("Cynthia");
putstr("Front of line: ");
print(line.front());
putstr("Back of line: ");
print(line.back());
line.push("Jonathan");
putstr("Number of people in line: ");
print(line.size());
putstr("Back of line is now: ");
print(line.back());
line.pop();
putstr("Front of line: ");
print(line.front());
putstr("Back of line: ");
print(line.back());
print("Closing line.");
line.clear();
if (line.empty()) {
  print("Line is now empty.");
}
else {
  putstr("Number of people in line: ");
  print(line.size());
}
```

这个程序的输出是:

```
Front of line: Mike
Back of line: Cynthia
Number of people in line: 3
Back of line is now: Jonathan
Front of line: Cynthia
Back of line: Jonathan
Closing line.
Line is now empty.
```

# 一些队列应用程序

队列有几个有用的应用。队列通常用于模拟研究，其中研究顾客流。例如，一家杂货店需要多少个自助检查岛来防止顾客队伍在购物高峰期间积压过多？模拟程序将使用队列数据结构来表示商店中的每一行。

对于第一个例子，我将编写一个小程序，实现一个队列调度程序来跟踪一个主管的日常约会。行政助理收集第二天的新约会，并将它们存储在约会队列中。

在第二天开始时，访问约会队列并显示第一个约会。完成后，它将被删除，并显示下一个预约时间。这一直持续到队列为空。

我还创建了一个新的类来存储时间。程序是这样的:

```
class Time {
  constructor(hour, minute) {
    this.hour = hour;
    this.minute = minute;
  } show() {
    return this.hour + ":" + this.minute;
  }
}let appts = new Queue();
for (let i = 1; i <= 6; i++) {
  putstr("Enter appointment hour: ");
  let hour = parseInt(readline());
  putstr("Enter appointment minute: ");
  let minute = parseInt(readline());
  let theTime = new Time(hour, minute);
  appts.push(theTime);
}
print("Appointments for the day: ");
let nextAppt = "n";
while (!appts.empty()) {
  putstr("Time: ");
  print(appts.front().show());
  appts.pop();
}
```

这个程序运行一次的输出是:

```
C:\js>js queue.js
Enter appointment hour: 8
Enter appointment minute: 15
Enter appointment hour: 9
Enter appointment minute: 20
Enter appointment hour: 10
Enter appointment minute: 25
Enter appointment hour: 11
Enter appointment minute: 25
Enter appointment hour: 12
Enter appointment minute: 30
Enter appointment hour: 1
Enter appointment minute: 15
Appointments for the day:
Time: 8:15
Time: 9:20
Time: 10:25
Time: 11:25
Time: 12:30
Time: 1:15
```

为了进行第二次演示，我将借用 Timothy Budd 所著的《C++ 中的*数据结构》一书中的一个模拟示例。这个模拟是一个银行出纳员模拟，可以帮助银行确定在任何一个时间应该开放多少个出纳员柜台，以最大限度地减少客户等待帮助的时间。*

该程序创建了两个类——一个`Customer`类和一个`Teller`类。`Customer`类跟踪两条数据:顾客排队到达的时间和他们与出纳员相处的时间。`Teller`类还必须跟踪两条数据:他们是否在帮助客户，以及他们开始帮助客户的时间。

以下是这两个类别的定义:

```
class Customer {
  constructor(arrival) {
    this.arrivalTime = arrival;
    this.processTime = 2 + parseInt(Math.random() * (6-1) + 1);
  } done() {
    return --this.processTime < 0;
  } arrival() {
    return this.arrivalTime;
  }
}class Teller {
  constructor() {
    this.free = true;
  } isFree() {
    if (this.free) { return true };
    if (this.customer.done()) {
      this.free = true;
      return this.free;
    }
  } addCustomer(c) {
    this.customer = c;
    this.free = false;
  }
}
```

`Customer`物体排成一行，然后离开。`Teller`对象要么有空见新客户，要么正忙着帮助客户。

下面是一个程序，使用这些类来执行一个简单的银行出纳员模拟。您可以通过更改柜员数量来改变平均等待时间，从而决定您的银行应该有多少柜员队伍。

代码如下:

```
let tellers = 2;
let minutes = 60;
let totalWait = 0;
let customers = 0;
let line = new Queue();
let teller = new Array(tellers);
for (let i = 0; i < tellers; i++) {
  teller[i] = new Teller();
}
for (let time = 0; time < minutes; time++) {
  if (parseInt(Math.random() * (10-1) + 1) < 9) {
    let newCustomer = new Customer(time);
    line.push(newCustomer);
  }
  for (let i = 0; i < tellers; i++) {
    if (teller[i].isFree() && !line.empty()) {
      let frontCustomer = line.front();
      customers++;
      totalWait += (time - frontCustomer.arrival());
      teller[i].addCustomer(frontCustomer);
      line.pop();
    }
  }
}
print("Average wait: " + (totalWait / customers));
```

下面是将出纳员数量设置为 2 的程序的一次运行:

```
Average wait: 16.571428571428573
```

下面是当我将出纳员的数量增加到 5 时，平均等待时间的变化情况:

```
Average wait: 2
```

队列可以用于其他应用，但模拟是它们最受欢迎的用途。

另一种类型的队列是优先级队列，其中存储在队列中的元素具有优先级权重，并且那些具有较高优先级的元素比具有较低优先级的元素更快地从队列中弹出。优先排队的一个很好的例子是医院急诊部的候诊室。断腿比发烧更重要，会更快得到医生的治疗。我将在以后的文章中详细介绍优先级队列的实现。

感谢您的阅读，请回复本文或给我发电子邮件，提出您的意见和建议。