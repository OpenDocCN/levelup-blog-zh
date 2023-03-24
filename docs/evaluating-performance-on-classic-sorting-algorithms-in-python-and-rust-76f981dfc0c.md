# Python 和 Rust 中经典排序算法的性能评估

> 原文：<https://levelup.gitconnected.com/evaluating-performance-on-classic-sorting-algorithms-in-python-and-rust-76f981dfc0c>

![](img/14e4f75bb40360dada8b6707ce568d4b.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [chuttersnap](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) 拍摄

# 背景

算法性能一直是解决复杂现实世界用例的关键因素之一。我们需要高效的算法，尤其是在对大量数据进行排序的情况下。在本文中，我将带您了解如何在 Python 和 Rust 编程语言中实现冒泡、插入、快速和合并排序算法，以了解编程语言是否对获得性能优势有影响。

[](https://www.eduelk.com/) [## 教育

### 通过我们的练习题为您的下一次技术认证考试树立信心。我们提供课程来加速…

www.eduelk.com](https://www.eduelk.com/) [](https://www.eduelk.com/about-us) [## 关于我们——教育

### 在爱德克教育，我们相信熟能生巧。我们知道准备参加技术认证是…

www.eduelk.com](https://www.eduelk.com/about-us) [](https://www.eduelk.com/shop-for-practice-questions) [## 通过我们负担得起的练习题获得自信——教育

### 编辑描述

www.eduelk.com](https://www.eduelk.com/shop-for-practice-questions) [](https://www.eduelk.com/book-a-lesson) [## 联系方式 1——教育

### 编辑描述

www.eduelk.com](https://www.eduelk.com/book-a-lesson) 

# 先决条件

在继续本文之前，您应该已经，

*   Python 和 Rust 的基本编程经验
*   对一些经典排序算法的基本理解

# 环境

*   Docker 版本 19.03.6
*   Python 3.7.3
*   铁锈 1.43.1

# 冒泡排序

## 计算机编程语言

**源代码:**

```
**import** random
**import** datetime**def** BubbleSortAsc(array):
 no_swap = True

 while no_swap:
  no_swap = False for position in range(0, len(array)-1):
   if array[position] > array[position+1]: # swap
    temp = array[position]
    array[position] = array[position+1]
    array[position+1] = temp
    no_swap = True
```

**运行:**

```
if __name__ == "__main__":
 array = [index for index in range(1, 100001)]
 random.shuffle(array)
 start = datetime.datetime.now()
 BubbleSortAsc(array)
 end = datetime.datetime.now()
 print("It took: ", (end-start).total_seconds()*1000, "ms to sort")
```

**性能:**

*   按升序对随机的 10000 个正整数进行排序花费了 **9953.896999999999** 毫秒
*   按升序对随机的 **100000** 正整数进行排序花费的时间太长。我不够耐心。

## 锈

**源代码:**

```
**use** rand::seq::SliceRandom;
**use** rand::thread_rng;
**use** std::time::Instant;**fn** bubble_sort(vec: &mut Vec<i32>) {
 let mut no_swap = true; while no_swap {
  no_swap = false; for index in 0..vec.len()-1 { if vec[index] > vec[index+1]{

    let temp = vec[index];
    vec[index] = vec[index+1];
    vec[index+1] = temp;
    no_swap = true;
   }
  }
 }
}
```

**运行:**

```
**fn** main() {
 let mut rng = thread_rng();
 let mut int_vec = Vec::new();
 let max_num = 10001;

 for num in 1..max_num {
  int_vec.push(num as i32);
 } println!("Unshuffled: {:?}", int_vec);
 int_vec.shuffle(&mut rng);
 println!("Shuffled: {:?}", int_vec); let start = Instant::now();
 bubble_sort(&mut int_vec);
 let elapsed = start.elapsed();
 println!("It took: {} milliseconds to sort", elapsed.as_millis());
}
```

**表现:**

*   将随机的 10000 个正整数按升序排序花费了 8316 毫秒
*   将随机的 **100000** 正整数按升序排序花费的时间太长。我不够耐心。

# 插入排序

## 计算机编程语言

**源代码:**

```
**import** random
**import** datetime**def** InsertionSortAsc(array):
 for index in range(1, len(array)):

  key = array[index]
  j = index — 1 while j >=0 and key < array[j]:
   array[j+1] = array[j]
   j -= 1

  array[j+1] = key
```

**运行:**

```
if __name__ == "__main__":
 array = [index for index in range(1, 100001)]
 random.shuffle(array)
 start = datetime.datetime.now()
 InsertionSortAsc(array)
 end = datetime.datetime.now()
 print("It took: ", (end-start).total_seconds()*1000, "ms to sort")
```

**性能:**

*   按升序对随机的 **10000** 正整数进行排序花费了 **2714.989** 毫秒
*   将随机的 **100000** 正的整数按升序排序花费的时间太长。我不够耐心。

## 锈

**源代码:**

```
**use** rand::seq::SliceRandom;
**use** rand::thread_rng;
**use** std::time::Instant;**fn** insertion_sort(vec: &mut Vec<i32>) {
 for index in 1..vec.len() {
  let mut j = index;
  while j > 0 && vec[j-1] > vec[j] {
   vec.swap(j — 1, j);
   j -= 1;
  }
 }
}
```

**运行:**

```
**fn** main() {
 let mut rng = thread_rng();
 let mut int_vec = Vec::new();
 let max_num = 10001; for num in 1..max_num {
  int_vec.push(num as i32);
 } println!("Unshuffled: {:?}", int_vec);
 int_vec.shuffle(&mut rng);
 println!("Shuffled: {:?}", int_vec); let start = Instant::now();
 insertion_sort(&mut int_vec);
 let elapsed = start.elapsed();
 println!("sorted: {:?}", int_vec);
 println!("It took: {} milliseconds to sort", elapsed.as_millis());
}
```

**性能:**

*   用了 **1377** 毫秒将随机的 **10000** 正的**整数按升序排序**
*   按升序对随机的 **100000** 正整数进行排序花费的时间太长。我不够耐心。

# 快速排序

## 计算机编程语言

**源代码:**

```
**import** random
**import** datetime**def** partitionAsc(arr, low, high):
 i = low — 1 # index of smaller element
 pivot = arr[high] # pivot: using last element as pivot
 for j in range(low, high): #j = 0,1,2,..,7
  # If current element is smaller than or equal to pivot
  if arr[j] <= pivot:
   # increment index of smaller element
   i = i+1
   arr[i], arr[j] = arr[j], arr[i] #swaps arr[i] and arr[j]

 arr[i+1], arr[high] = arr[high], arr[i+1] #swaps
 return i+1 #index of the partition that is in its correct position**def** quickSortAsc(arr, low, high):
 if low < high:
  # pi is partitioning index, arr[p] is now at right place
  pi = partitionAsc(arr, low, high)
  # Separately sort elements before partition and after partition
  quickSortAsc(arr, low, pi-1)
  quickSortAsc(arr, pi+1, high) return
```

**运行:**

```
if __name__ == "__main__":
 array = [index for index in range(1, 100001)]
 random.shuffle(array)
 start = datetime.datetime.now()
 quickSortAsc(array, 0, len(array)-1)
 end = datetime.datetime.now()
 print("It took: ", (end-start).total_seconds()*1000,  "ms to sort")
```

**性能:**

*   用了 **17.005** 毫秒对随机 **10000** 正整数进行升序排序。
*   用了**230.286999999999998**毫秒对随机 **100000** 正整数进行升序排序。

## 锈

**源代码:**

```
**use** rand::seq::SliceRandom;
**use** rand::thread_rng;
**use** std::time::Instant;**fn** swap(vec: &mut Vec<i32>, i: usize, j: usize ) {
 let temp = vec[i];
 vec[i] = vec[j];
 vec[j] = temp;
}**fn** partition(vec: &mut Vec<i32>, start: usize, end: usize ) -> i32 {
 let mut pivot = vec[end];
 let mut index = start;
 let mut i = start;

 while i < end {

  if vec[i] < pivot {
   swap(vec, i, index);
   index+=1;
  }

  i+=1;
 } swap(vec, index, end);
 return index as i32;
}**fn** quick_sort(vec: &mut Vec<i32>, start: usize, end: usize) {
 if start >= end {
  return;
 } let mut pivot = partition(vec, start, end);
 let min_pivot = if pivot == 0 { 0 } else { pivot — 1 };

 quick_sort(vec, start, min_pivot as usize);
 quick_sort(vec, (pivot + 1) as usize, end);
}
```

Rust 源代码灵感来自 Turreta.com[1]。

**运行:**

```
fn main() {
 let mut rng = thread_rng();
 let mut int_vec = Vec::new();
 let max_num = 10001; for num in 1..max_num {
  int_vec.push(num as i32);
 } println!("Unshuffled: {:?}", int_vec);
 int_vec.shuffle(&mut rng);
 println!("Shuffled: {:?}", int_vec); let start = Instant::now();
 let high = int_vec.len()-1;
 quick_sort(&mut int_vec, 0, high);
 let elapsed = start.elapsed();
 println!("It took: {} milliseconds to sort", elapsed.as_millis());
}
```

**性能:**

*   按升序对随机的 **10000 个**正整数排序花费了 **8** 毫秒
*   将随机的 100000 个正整数按升序排序花费了 **113** 毫秒

# 合并排序

## 计算机编程语言

**源代码:**

```
**import** random
**import** datetime**def** MergeAsc(left,right):
 #Function to merge two lists temp_array = [] #additional memory needed
 while len(left) != 0 and len(right) != 0: if left[0] < right[0]: temp_array.append(left[0])
   left.remove(left[0]) else: temp_array.append(right[0])
   right.remove(right[0])

 if len(left) == 0:
  temp_array += right
 else:
  temp_array += left return temp_array**def** MergeSortAsc(array):
 # Function to sort a list using merge sort algorithm if len(array) == 0 or len(array) == 1:
  return array
 else: middle = int(len(array)/2)
  left = MergeSortAsc(array[:middle])
  right = MergeSortAsc(array[middle:]) return MergeAsc(left,right)
```

**运行:**

```
if __name__ == "__main__":
 array = [index for index in range(1, 100001)]
 random.shuffle(array)
 start = datetime.datetime.now()
 array = MergeSortAsc(array)
 end = datetime.datetime.now()
 print("It took: ", (end-start).total_seconds()*1000, "ms to sort")
```

**性能:**

*   用了 **56.284** 毫秒对随机 **10000** 正整数进行升序排序。
*   用了 **1185.939** 毫秒对随机 **100000** 正整数进行升序排序。

## 锈

**源代码:**

```
**use** rand::seq::SliceRandom;
**use** rand::thread_rng;
**use** std::time::Instant;**fn** merge(original_vec: &mut Vec<i32>, copy_vec: &mut Vec<i32>, low: usize, mid: usize, high: usize) {
 let mut x = low;
 let mut y = low;
 let mut z = mid + 1;

 while y <= mid && z <= high {
  if original_vec[y] < original_vec[z] {
   copy_vec[x] = original_vec[y];
   x+=1;
   y+=1;
  } else {
   copy_vec[x] = original_vec[z];
   x+=1;
   z+=1;
  }
 }

 while y <= mid {
  copy_vec[x] = original_vec[y];
  x+=1;
  y+=1;
 }

 y = low;
 while y <= high {
  original_vec[y] = copy_vec[y];
  y+=1;
 }
}**fn** merge_sort(original_vec: &mut Vec<i32>, copy_vec: &mut Vec<i32>, low: usize, high: usize) {
 if high == low {
  return;
 }

 let mut mid: usize = (low + (( high — low ) >> 1));

 merge_sort(original_vec, copy_vec, low, mid);
 merge_sort(original_vec, copy_vec, mid + 1, high);
 merge(original_vec, copy_vec, low, mid, high);

}
```

受 Turreta.com 启发的 Rust 源代码[2]。

**运行:**

```
**fn** main() {
 let mut rng = thread_rng();
 let mut int_vec = Vec::new();
 let max_num = 10001; for num in 1..max_num {
  int_vec.push(num as i32);
 } println!("Unshuffled: {:?}", int_vec);
 int_vec.shuffle(&mut rng);
 println!("Shuffled: {:?}", int_vec); let len:usize = int_vec.len() — 1;
 let start = Instant::now();
 merge_sort(&mut int_vec, &mut copy_vec, 0, len);
 let elapsed = start.elapsed();
 println!("It took: {} milliseconds to sort", elapsed.as_millis());

}
```

**性能:**

*   将随机的 10000 个正整数按升序排序花费了 **13** 毫秒
*   按升序对随机的 100000 个正整数排序花费了 174 毫秒

# 思想

作为一个实验，用 Rust 编写的排序算法与用 Python 编写的相比性能稍好。对于 10000 和 100000 个正整数的排序，平均性能分别提高了大约 10 到 100 毫秒。

我觉得除了实时视频分析解决方案等时间关键型应用之外，这在大多数用例中不会成为大问题。我将会发表一篇文章来评估 Python 和 Rust 中经典搜索算法的性能。敬请期待！和平！✌️

[](https://www.eduelk.com/) [## 教育

### 通过我们的练习题为您的下一次技术认证考试树立信心。我们提供课程来加速…

www.eduelk.com](https://www.eduelk.com/) [](https://www.eduelk.com/about-us) [## 关于我们——教育

### 在爱德克教育，我们相信熟能生巧。我们知道准备参加技术认证是…

www.eduelk.com](https://www.eduelk.com/about-us) [](https://www.eduelk.com/shop-for-practice-questions) [## 通过我们负担得起的练习题获得自信——教育

### 编辑描述

www.eduelk.com](https://www.eduelk.com/shop-for-practice-questions) [](https://www.eduelk.com/book-a-lesson) [## 联系方式 1——教育

### 编辑描述

www.eduelk.com](https://www.eduelk.com/book-a-lesson) 

# 参考

1.  加布里埃尔，K. S. (2020 年 1 月 11 日)。Rust 中的快速排序算法示例。2020 年 5 月 22 日检索，来自[https://turreta . com/2019/10/22/quick sort-algorithm-example-in-rust/](https://turreta.com/2019/10/22/quicksort-algorithm-example-in-rust/)
2.  加布里埃尔，K. S. (2020 年 1 月 11 日)。Rust 码中的归并排序算法。2020 年 5 月 22 日检索，来自[https://turreta . com/2019/10/26/merge-sort-algorithm-in-rust-codes/](https://turreta.com/2019/10/26/merge-sort-algorithm-in-rust-codes/)