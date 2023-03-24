# 比较 4 种编程语言的编程语言效率:时序选择排序

> 原文：<https://levelup.gitconnected.com/comparing-programming-language-efficiency-in-4-programming-languages-timing-selection-sort-29badc8a744f>

![](img/7538d74d0ebb14d460b95cfc95caf366.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Veri Ivanova](https://unsplash.com/@veri_ivanova?utm_source=medium&utm_medium=referral) 拍摄的照片

计算机科学家使用[大 O 符号](https://en.wikipedia.org/wiki/Big_O_notation)来确定有效的算法。在这篇文章中，我更感兴趣的是计算机编程语言如何执行简单的排序算法。我将通过使用系统时钟来测量使用选择排序对存储在一个数组中的一百万个随机生成的数字进行排序所花费的时间(以毫秒为单位),来进行这种比较。

我将在比较中使用的语言是 C++、Java、JavaScript 和 Python。事不宜迟，我们开始吧。

# 测试中使用的计算机

以下是我的戴尔笔记本电脑的规格，这是我在这次性能测试中使用的计算机:

处理器:英特尔酷睿 i5–8250 u CPU @ 1.60 GHz 1.80 GHz

安装的内存:8.00 GB (7.90 GB 可用)

系统类型:64 位操作系统，基于 x64 的处理器

# 选择排序算法

[选择排序](https://en.wikipedia.org/wiki/Selection_sort)是实现起来最简单的排序算法。当然，它不是最有效的算法，但我的目标是测量编程语言的效率，而不是算法的效率。与使用更有效的排序算法(如 QuickSort)相比，使用次优算法会更突出编程语言的效率或低效率。

以下是选择排序算法的伪代码实现:

*设置 n 为数组大小。
For i from 1 to n — 1:
将最小值设置为 I
For j = I+1 to n:
If array[j]<array[minimum]:
将最小值设置为 j
End If
End For
Swap(array[I]，array[minimum]
End for*

现在让我们讨论一下如何测量时间，然后当我演示如何在每种语言中实现选择排序时，我将演示如何测量算法的运行时间。

# 测量系统时间

大多数编程语言都有捕捉系统日期和时间的功能。对于许多语言来说，同一个函数同时包含了两者。我将在这里提供两个例子:一个是有专门的库来记录时间的语言(C++)，另一个是有一个类来提供记录时间的方法(JavaScript)。

JavaScript 有一个处理时间的类——`Date` 类。这个类存储关于日期和时间的数据。在我的程序中，我将只使用这个类中的一个方法，即`getMilliseconds` 方法。下面是一个简短的程序，演示了我将如何用 JavaScript 测量系统时间:

```
let numbers = [];
const NUM_ELEMENTS = 1000;
let start = new Date();
for (let i = 1; i <= NUM_ELEMENTS; i++) {
  numbers.push(i);
}
let stop = new Date();
let duration = stop.getMilliseconds() - start.getMilliseconds();
print("Time in milliseconds for loop to run:",duration);
```

这个程序的输出是:

```
Time in milliseconds for loop to run: 13
```

在 C++中，`chrono`库包含了我测量系统时间所需的函数。下面是一个演示如何使用这个库的程序:

```
#include <iostream>
#include <chrono>
using namespace std;int main()
{
  const int NUM_ELEMENTS = 1000;
  int numbers[NUM_ELEMENTS];
  auto start = chrono::system_clock::now();
  for (int i = 1; i <= NUM_ELEMENTS; i++) {
    numbers[i-1] = i;
  }
  auto stop = chrono::system_clock::now();
  auto lapsed_time =
    chrono::duration_cast<chrono::milliseconds>
                          (stop - start).count();
  cout <<  "It took " << lapsed_time
       <<  " milliseconds to populate the array."  << endl;
  return 0;
}
```

这个程序的输出是:

```
It took 0 milliseconds to populate the array.
```

每种语言都有自己捕捉系统时间的方式，所以我们将在本文中看到五种不同的方法。我在这里给出了两个例子，只是为了让你对如何在程序中捕获系统时间有一点感觉。

有了这些预备知识，让我们开始在执行选择排序时对编程语言进行计时，看看哪种语言是最有效的。

# C++中的选择排序

下面是我写的程序，用来测量 C++中选择排序的性能:

```
#include <iostream>
#include <chrono>
#include <cstdlib>
#include <ctime>
using namespace std;void selectionSort(int arr[], int numElements) {
  int minimum;
  for (int i = 0; i < numElements-1; i++) {
    minimum = i;
    for (int j = i+1; j < numElements; j++) {
      if (arr[j] < arr[minimum]) {
        minimum = j;
      }
    }
    int temp = arr[i];
    arr[i] = arr[minimum];
    arr[minimum] = temp;
  }
}void setArray(int arr[], int numElements) {
  int randNum;
  for (int i = 0; i < numElements; i++) {
    randNum = rand() % 1000000 + 1;
    arr[i] = randNum;
  }
}int main()
{
  srand(time(0));
  const int NUMELEMENTS = 10000;
  const int MID = NUMELEMENTS / 2;
  const int END = NUMELEMENTS - 1;
  int numbers[NUMELEMENTS];
  setArray(numbers, NUMELEMENTS);
  auto start = chrono::system_clock::now();
  selectionSort(numbers, NUMELEMENTS);
  auto stop = chrono::system_clock::now();
  cout << numbers[0] << ", " << numbers[MID]
       << ", " << numbers[END] << endl;
  auto lapsed_time =
    chrono::duration_cast<chrono::milliseconds>
                        (stop – start).count();
  cout <<  "It took " << lapsed_time
       << " milliseconds to sort the array."  << endl;
  return 0;
}
```

为了验证数组已经排序，我显示了第一个元素、位于数组中点的元素和位于数组末尾的元素。

这个程序的输出是:

```
1, 16568, 32765
It took 122 milliseconds to sort the array.
```

现在让我们看看 Java 版本。

# Java 中的选择排序性能

即使您以前从未用 Java 编程，按照我的 Java 程序测试选择排序算法的性能也不会有什么困难。程序如下:

```
import java.util.Random;public class SSort {
  static void selectionSort(int[] arr) {
    int minimum;
    for (int i = 0; i < arr.length-1; i++) {
      minimum = i;
      for (int j = i+1; j < arr.length; j++) {
        if (arr[j] < arr[minimum]) {
          minimum = j;
        }
      }
      int temp = arr[i];
      arr[i] = arr[minimum];
      arr[minimum] = temp;
    }
  } static void buildArray(int[] arr) {
    Random rand = new Random();
    for (int i = 0; i < arr.length; i++) {
      arr[i] = rand.nextInt(1000000);
    }
  } public static void main(String args[]) {
    final int NUM_ELEMENTS = 10000;
    final int MID = NUM_ELEMENTS/2;
    final int END = NUM_ELEMENTS-1;
    Random rand = new Random();
    int [] numbers = new int[NUM_ELEMENTS];
    buildArray(numbers);
    long start = System.currentTimeMillis();
    selectionSort(numbers);
    long stop = System.currentTimeMillis();
    System.out.println(numbers[0] + "," +
                       numbers[MID] + "," + numbers[END]);
    System.out.println("It took " + (stop - start) +
                       " milliseconds to sort the array.");
  }
}
```

下面是这个程序运行一次的输出:

```
99,500997,999990
It took 50 milliseconds to sort the array.
```

C++版本用了 122 毫秒，所以与 C++相比，Java 并不像有些人说的那样低效。

现在让我们转到 JavaScript 版本的性能比较。

# JavaScript 中的选择排序性能

JavaScript 是我们测试的第一种解释语言。同样，您不需要了解 JavaScript 就能理解这段代码，因为 JavaScript 使用了大量 C 风格的语法。JavaScript 中的数组是不同的，因为 JavaScript 是不编译的，我将使用 push 方法向数组添加数据，而不是索引。否则，我的 JavaScript 代码看起来很像前两个程序。

下面是 JavaScript 代码:

```
function buildArray(arr) {
  for (let i = 1; i <= 10000; i++) {
    arr.push(Math.floor(Math.random() * Math.floor(1000000)));
  }
}function selectionSort(arr) {
  let minimum = 0;
  for (let i = 0; i < arr.length-1; i++) {
    minimum = i;
    for (let j = i+1; j < arr.length; j++) {
      if (arr[j] < arr[minimum]) {
        minimum = j;
      }
    }
    let temp = arr[i];
    arr[i] = arr[minimum];
    arr[minimum] = temp;
  }
}let numbers = [];
const NUM_ELEMENTS = 10000;
const MID = NUM_ELEMENTS/2;
const END = NUM_ELEMENTS-1;
buildArray(numbers);
let start = new Date();
selectionSort(numbers);
let stop = new Date();
let duration = stop.getMilliseconds() - start.getMilliseconds();
print(numbers[0], numbers[MID], numbers[END]);
print("It took",duration,"milliseconds to sort the array.");
```

下面是这个程序运行一次的输出:

```
179 508179 999945
It took 83 milliseconds to sort the array.
```

这个程序比我的 Java 程序需要更长的时间来对数组进行排序，但是时间仍然比我的 C++版本少 40 毫秒。

现在让我们转到 Python 版本。

# Python 中的选择排序性能

和 JavaScript 一样，Python 和其他语言的主要区别是用于存储数字的数据结构。Python 没有数组，但是它有列表，可以以同样的方式使用。Python 生成随机数的方式也有所不同，但并没有大到让代码看起来令人困惑的程度。

以下是我的 Python 选择排序程序的代码:

```
from random import seed
from random import randint
from time import timedef selectionSort(nums):
  for i in range(0, len(nums)-1):
    minimum = i
    for j in range(i+1, len(nums)):
      if (nums[j] < nums[minimum]):
        minimum = j
    temp = nums[i]
    nums[i] = nums[minimum]
    nums[minimum] = tempdef buildList(nums):
  seed(1)
  for i in range(1,10000):
    nums.append(randint(1,1000000))

numbers = []
mid = 5000
end = 9999
buildList(numbers)
start = int(time() * 1000)
selectionSort(numbers)
stop = int(time() * 1000)
duration = stop-start
print(numbers[0], numbers[mid], numbers[len(numbers)-1])
print("It took", duration, "milliseconds to sort the list.")
```

这个程序运行一次的输出是:

```
233 498036 999720
It took 2755 milliseconds to sort the list.
```

# 绩效结论

以下是每种语言使用选择排序算法对 10000 个随机生成的数字进行排序所用的时间，从最快到最慢排列:

Java: 50 毫秒

JavaScript: 83 毫秒

C++: 122 毫秒

Python: 2755 毫秒

# 讨论

我有点惊讶于 Java 比 C++快，但我更惊讶于 JavaScript 比 C++快。看到这个结论，我决定研究一下 C++性能差是否是由于我使用的编译器(Code::Blocks)造成的。

为了测试这一点，我将程序转移到我的 Linux 系统上，并使用我的 Linux 发行版中的 gcc 编译器运行它。我收到的输出是:

```
114, 490695, 999987
It took 118 milliseconds to sort the array.
```

显然，改变编程环境并不能提高选择排序的 C++性能。

我计划进一步调查这些不同语言之间的性能差异，并将我的发现写下来。我的下一个研究将是字符串处理。

感谢您的阅读，请回复这篇文章或发邮件给我，告诉我您的意见和建议。