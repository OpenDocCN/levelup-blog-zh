# JavaScript 中的位屏蔽

> 原文：<https://levelup.gitconnected.com/bit-masking-in-javascript-831eb26f04a3>

![](img/29d670cffcd75ca8c2cf22323ea02f5d.png)

照片由[亨利&公司](https://unsplash.com/@hngstrm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

通过位屏蔽，多个标志被组合成一个变量，可以通过应用屏蔽进行测试。

这使得单个变量可以存储多个值。

# 功能参数

例如，假设您想向函数传递任意数量的选项，而不定义单个参数或配置动态对象:

```
const OPTION_1 = 0x0001;
const OPTION_2 = 0x0010;
const OPTION_3 = 0x0100;
const OPTION_4 = 0x1000;function fn(options) {
  if (options & OPTION_1) { console.log("1"); }
  if (options & OPTION_2) { console.log("2"); }
  if (options & OPTION_3) { console.log("3"); }
  if (options & OPTION_4) { console.log("4"); }
}
```

上面定义了四个选项，可以任意传递给函数，例如:

```
fn(OPTION_1);                       // outputs: 1
fn(OPTION_2 | OPTION_4);            // outputs: 2, 4
fn(OPTION_3 | OPTION_1);            // outputs: 1, 3
fn(OPTION_1 | OPTION_2 | OPTION_3); // outputs: 1, 2, 3
```

这是通过定义常量的枚举来实现的，这些常量的值是 2 的幂(1、2、4、8、16、32、64、128 等等)。这些成为我们可以传递给函数的标志，就像我们可以切换来设置我们的选项的开关。

在函数内部，我们测试该标志是否存在，主要是检查开关是开还是关。

另一个例子是确认对话框——假设您想要显示一条消息，其中包含一组任意选项，如“是”、“否”、“确定”或“取消”

```
const YES    = 1;
const NO     = 2;
const OK     = 4;
const CANCEL = 8;function showDialog(message, options) {
  let msg = message; if (options & YES)    { msg += " [YES]"}
  if (options & NO)     { msg += " [NO]"}
  if (options & OK)     { msg += " [OK]"}
  if (options & CANCEL) { msg += " [CANCEL]"} console.log(msg);
}showDialog("Continue?", YES | NO);
showDialog("Save file?", OK | CANCEL);
```

上面如果我们用`YES | NO`调用`showDialog`，我们会看到:

> 继续吗？[是][否]

或者，如果我们用`OK | CANCEL`调用`showDialog`，我们会看到:

> 保存文件？[确定][取消]

# 存储状态

另一种用法是存储状态，其中多个状态值可以存储在单个变量中。

在这个例子中，让我们定义一组任务和一个名为`flags`的变量来存储我们的状态。

```
const TASK_1 = 0x001
const TASK_2 = 0x010
const TASK_3 = 0x100let flags = 0;
```

## 设置标志

要将任务添加到我们的标志列表中，我们可以使用按位 OR 赋值来设置标志:

```
flags |= TASK_1;
```

让我们激活两个任务；然后，检查它们是否处于活动状态:

```
const TASK_1 = 0x001
const TASK_2 = 0x010
const TASK_3 = 0x100let flags = 0;// Activate task 1 and 2:
flags |= TASK_1;
flags |= TASK_2;// Check which tasks are running:
if(flags & TASK_1) { console.log("Task 1 running...")}
if(flags & TASK_2) { console.log("Task 2 running...")}
if(flags & TASK_3) { console.log("Task 3 running...")}
```

执行上述操作将输出:

> 任务 1 正在运行…
> 任务 2 正在运行…

## 清除标志

要清除标志，请在要清除的标志上使用带反位的按位 AND 赋值:

```
flags &= ~TASK_1;
```

让我们从这三个任务都处于活动状态开始；然后，明确第一个任务:

```
const TASK_1 = 0x001
const TASK_2 = 0x010
const TASK_3 = 0x100// Set all tasks and check status
let flags = TASK_1 | TASK_2 | TASK_3;
checkStatus();// Clear the first task and recheck status
flags &= ~TASK_1;
checkStatus();function checkStatus() {
  if(flags & TASK_1) { console.log("Task 1 running...")}
  if(flags & TASK_2) { console.log("Task 2 running...")}
  if(flags & TASK_3) { console.log("Task 3 running...")}
}
```

第一次检查状态时，我们看到三个任务都是活动的。

清除第一个任务标志后，重新检查状态仅显示两个活动任务。

运行上面的代码会输出:

> 任务 1 正在运行…
> 任务 2 正在运行…
> 任务 3 正在运行…
> 
> 任务 2 正在运行…
> 任务 3 正在运行…

## 切换旗帜

要切换标志，使用按位 XOR 赋值来翻转该位，就像打开和关闭开关一样:

```
flags ^= TASK_1;
```

如果标志是活动的，它现在将是不活动的；否则，如果之前处于非活动状态，将变为活动状态。

让我们从激活任务 1 开始，然后在任务 1 和任务 2 之间每秒切换一次，在每次循环后检查状态:

```
const TASK_1 = 0x001
const TASK_2 = 0x010
const TASK_3 = 0x100let flags = TASK_1;setInterval(() => {
  flags ^= TASK_1;
  flags ^= TASK_2; checkStatus();
}, 1000);function checkStatus() {
  if(flags & TASK_1) { console.log("Task 1 running...")}
  if(flags & TASK_2) { console.log("Task 2 running...")}
  if(flags & TASK_3) { console.log("Task 3 running...")}
}
```

执行上述操作将在任务之间切换一次，每秒输出:

> 任务 2 正在运行…
> 任务 1 正在运行…
> 任务 2 正在运行…
> 任务 1 正在运行…

## 检查旗帜

有多种方法可以断言当前是否设置了一个标志。

如果设置了标志，该值将大于零:

```
const TASK_1 = 0x001
const TASK_2 = 0x010
const TASK_3 = 0x100let flags = TASK_1;if ((flags & TASK_1) > 0) {
  console.log("Task 1 is active");
}
```

如果未设置标志，该值将为零:

```
const TASK_1 = 0x001
const TASK_2 = 0x010
const TASK_3 = 0x100let flags = TASK_2;if ((flags & TASK_1) === 0) {
  console.log("Task 1 is not active");
}
```

要检查是否是多个标志中的一个，请用按位 OR 运算符将选项组合在一起，并断言该值大于零。下面测试是否设置了任务 1 或任务 3—因为任务 1 是活动的，所以为真:

```
const TASK_1 = 0x001
const TASK_2 = 0x010
const TASK_3 = 0x100let flags = TASK_1 | TASK_2;if ((flags & (TASK_1 | TASK_3)) > 0) {
  console.log("Task 1 or task 2 active");
}
```

要检查多个标志中是否有一个没有设置，请用按位 OR 运算符将选项组合在一起，并断言该值等于零。下面测试任务 2 或任务 3 是否未设置—这是真的，因为任务 2 和任务 3 都不活动:

```
const TASK_1 = 0x001
const TASK_2 = 0x010
const TASK_3 = 0x100let flags = TASK_1;if ((flags & (TASK_2 | TASK_3)) === 0) {
  console.log("Neither task 2 or task 3 are active");
}
```

要一次检查多个标志，请断言所需标志相等。以下是正确的，因为任务 1 和任务 2 都有效:

```
const TASK_1 = 0x001
const TASK_2 = 0x010
const TASK_3 = 0x100let flags = TASK_1 | TASK_2;if ((flags & (TASK_1 | TASK_2)) === (TASK_1 | TASK_2)) {
  console.log("Task 1 and task 2 are active");
}
```