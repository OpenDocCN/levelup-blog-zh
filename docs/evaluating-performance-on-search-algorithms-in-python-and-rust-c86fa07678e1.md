# 评估 Python 和 Rust 中搜索算法的性能

> 原文：<https://levelup.gitconnected.com/evaluating-performance-on-search-algorithms-in-python-and-rust-c86fa07678e1>

![](img/93e91c3644af220f02c53ab52ab8efd9.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [chuttersnap](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) 拍摄

# 背景

有没有想过，你在高中或大学学习的搜索算法，在不同的编程语言中会有不同的表现？

在本文中，我将向您展示如何用两种编程语言实现线性和二分搜索法算法，即 Python 和 Rust 语言。此外，还比较了这两种算法在这两种语言中的性能。

在我们开始这篇文章之前，我想对我的上一篇文章，[https://medium . com/@ Leonard Yeo _ XL/evaluating-performance-on-classic-sorting-algorithms-in-python-and-rust-76f 981 DFC，](https://medium.com/@leonardyeo_xl/evaluating-performance-on-classic-sorting-algorithms-in-python-and-rust-76f981dfc0c)进行一个简短的介绍，如果你对性能评估这类事情感兴趣的话。

好吧，让我们开始吧！

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
*   对一些经典搜索算法的基本理解

# 环境

以下是我在评估中使用的环境:

*   Docker 版本 19.03.6
*   Python 3.7.3
*   铁锈 1.43.1
*   Rust dependencies rand = "0.7.3 "
*   Rust 依赖浮动-duration = "0.1.2 "

注意，算法运行在数据大小为 **900** 个元素的**上。**

开始编码吧！

# 线性搜索

简单回顾一下，线性搜索既可以迭代实现，也可以递归实现。执行搜索时，不必对数据进行排序。

## 计算机编程语言

**源代码迭代**

```
**import** random
**import** datetime**def** sorted_linear_search(search_key, arr):
 for i in range(0, len(arr)-1):
  if arr[i] == search_key:
   return True
 return False
```

**运行迭代**

```
**if** __name__ == "__main__":
 array = [index for index in range(1, 901)]
 random.shuffle(array) start = datetime.datetime.now()
 result = sorted_linear_search(789, array)
 end = datetime.datetime.now()
 print("It took: ",(end-start).total_seconds()*1000," ms to search")
```

**性能迭代**

在 900 的数据量中搜索数字 789 估计花费了 **0.024 毫秒**。

**源代码递归**

```
**import** random
**import** datetime**def** sorted_linear_search_recursion(search_key, arr, tail):
 if tail < 0:
  return False
 elif search_key == arr[tail]:
  return True
 else:
  return sorted_linear_search_recursion(search_key, arr, tail-1)
```

**运行递归**

```
**if** __name__ == "__main__":
 array = [index for index in range(1, 901)]
 random.shuffle(array) start = datetime.datetime.now()
 result = sorted_linear_search_recursion(789, array, len(array)-1)
 end = datetime.datetime.now()
 print("It took: ",(end-start).total_seconds()*1000," ms to search")
```

**性能递归**

在 900 的数据量中搜索数字 789 估计花费了 **0.187 毫秒**。

## 锈

**源代码迭代**

```
**use** rand::seq::SliceRandom;
**use** rand::thread_rng;
**use** std::time::Instant;
**use** floating_duration::TimeFormat;**fn** linear_search_iteration(search_key: i32, vec: &mut Vec<i32>) -> bool {
 for index in 0..vec.len()-1 {
  if vec[index] == search_key {
   return true;
  }
 }
 return false;
}
```

**运行迭代**

```
fn main() {
 let mut rng = thread_rng();
 let mut int_vec = Vec::new();
 let max_num = 901; for num in 1..max_num {
  int_vec.push(num as i32);
 } println!("Unshuffled: {:?}", int_vec);
 int_vec.shuffle(&mut rng);
 println!("Shuffled: {:?}", int_vec); int_vec.shuffle(&mut rng);
 let mut start = Instant::now();
 let result = linear_search_iteration(789, &mut int_vec);
 println!("It took: {} to search", TimeFormat(start.elapsed()));
}
```

**性能迭代**

*   在数据量为 900 的情况下，搜索数字 789 估计花费了 **42.550 秒**(微秒)或 **0.04255 毫秒**。

**源代码递归**

```
**use** rand::seq::SliceRandom;
**use** rand::thread_rng;
**use** std::time::Instant;
**use** floating_duration::TimeFormat;**fn** linear_search_recursion(search_key: i32, vec: &mut Vec<i32>, tail: usize) -> bool {
 if tail > 0 {
  if search_key == vec[tail] {
   return true;
  }
  else {
   return sorted_search_iteration(search_key, vec, tail-1);
  }
 }
 else if tail == 0 {
  if search_key == vec[tail] {
   return true;
  } 
  return false;
 }
 else {
  return false;
 }
}
```

**运行递归**

```
**fn** main() {
 let mut rng = thread_rng();
 let mut int_vec = Vec::new();
 let max_num = 901; for num in 1..max_num {
  int_vec.push(num as i32);
 } println!(“Unshuffled: {:?}”, int_vec);
 int_vec.shuffle(&mut rng);
 println!(“Shuffled: {:?}”, int_vec); let vec_length: usize = int_vec.len()-1;
 start = Instant::now();
 let result = linear_search_recursion(789,&mut int_vec,vec_length);
 println!("It took: {} to search", TimeFormat(start.elapsed()));
}
```

**性能递归**

在 900 的数据大小中搜索数字 789 估计花费了 **15.390 s(微秒)**或 **0.01539 毫秒**。

# 二进位检索

简单回顾一下，二分搜索法可以迭代递归地实现。然而，数据必须按降序或升序进行排序。

## 计算机编程语言

**源代码迭代**

```
**import** random
**import** datetime**def** binSearch_ite(search_key, arr):
 low = 0
 high = len(arr)-1 while low <= high:
  mid = int(low + (high-low)//2)
  if search_key == arr[mid]:
   return True
  if search_key < arr[mid]:
   high = mid-1
  elif search_key > arr[mid]:
   low = mid+1 return False
```

**运行迭代**

```
**if** __name__ == "__main__":
 array = [index for index in range(1, 901)]
 start = datetime.datetime.now()
 result = binSearch_ite(789, array)
 end = datetime.datetime.now()
 print("It took: ",(end-start).total_seconds()*1000," ms to search")
```

**性能迭代**

在 900 的数据量中搜索数字 789 估计花费了 **0.007 毫秒**。

**源代码递归**

```
**import** random
**import** datetime**def** binSearch_re(low, high, search_key, arr):
 if low > high:
  return False
 else:
  mid = int(low + (high-low)//2)
  if search_key == arr[mid]:
   return True
  if search_key < arr[mid]:
   return binSearch_re(low, mid-1, search_key, arr)
  elif search_key > arr[mid]:
   return binSearch_re(mid+1, high, search_key, arr)
```

**运行递归**

```
**if** __name__ == "__main__":
 array = [index for index in range(1, 901)]
 start = datetime.datetime.now()
 result = binSearch_re(0, len(array)-1, 789, array)
 end = datetime.datetime.now()
 print("It took: ",(end-start).total_seconds()*1000," ms to search")
```

**性能递归**

在 900 的数据中搜索数字 789 估计花费了 **0.01 毫秒**。

## 锈

**源代码迭代**

```
**use** rand::seq::SliceRandom;
**use** rand::thread_rng;
**use** std::time::Instant;
**use** floating_duration::TimeFormat;**fn** binary_search_iteration(search_key: i32, vec: &mut Vec<i32>) -> bool {
 let mut low: usize = 0;
 let mut high: usize = vec.len()-1;
 let mut mid: usize = 0; while low <= high {
  mid = low + (high-low)/2;
  if search_key == vec[mid] {
   return true;
  } if search_key < vec[mid] {
   high = mid — 1;
  } else if search_key > vec[mid] {
   low = mid + 1;
  }
 } return false;
}
```

**运行迭代**

```
**fn** main() {
 let mut rng = thread_rng();
 let mut int_vec = Vec::new();
 let max_num = 901; for num in 1..max_num {
 int_vec.push(num as i32);
 } let mut start = Instant::now();
 let result = binary_search_iteration(789, &mut int_vec);
 println!("It took: {} to search", TimeFormat(start.elapsed()));
}
```

**性能迭代**

在数据量为 900 的情况下，搜索数字 789 估计需要**680 纳秒**或 **0.00068 毫秒**。

**源代码递归**

```
**use** rand::seq::SliceRandom;
**use** rand::thread_rng;
**use** std::time::Instant;
**use** floating_duration::TimeFormat;**fn** binary_search_rec(low: usize, high: usize, search_key: i32, vec: &mut Vec<i32>) -> bool {
 if low > high {
  return false;
 } else {
  let mut mid = low + (high-low)/2;
  if search_key == vec[mid] {
   return true;
  } if search_key < vec[mid] {
   return binary_search_rec(low, mid-1, search_key, vec);
  } else {
   return binary_search_rec(mid+1, high, search_key, vec);
  }
 }
}
```

**运行递归**

```
**fn** main() {
 let mut rng = thread_rng();
 let mut int_vec = Vec::new();
 let max_num = 901; for num in 1..max_num {
  int_vec.push(num as i32);
 } start = Instant::now();
 let result = binary_search_rec(0,int_vec.len()-1,789,&mut int_vec);
 println!(“It took: {} to search”, TimeFormat(start.elapsed()));
}
```

**性能递归**

在 900 的数据大小中搜索数字 789 估计花费了**620 纳秒**或 **0.00062 毫秒**。

# 思想

总而言之，

*   线性迭代搜索(Python)估计花费了 **0.024 毫秒**
*   线性递归搜索(Python)估计花费了 **0.187 毫秒**
*   线性迭代搜索(Rust)估计花费了 **0.04255 毫秒**
*   递归线性搜索(Rust)估计花费了 **0.01539 毫秒**
*   二分搜索法迭代(Python)耗时估计 **0.007 毫秒**
*   二分搜索法递归(Python)耗时估计 **0.01 毫秒**
*   二分搜索法迭代(Rust)花费了估计的 **0.00068 毫秒**
*   二分搜索法递归(Rust)耗时估计 **0.00062 毫秒**

以下是我从这个实验中得出的想法，二分搜索法无疑比线性搜索快得多。此外，用 Rust 写二分搜索法，执行速度更快。迭代或递归编写对速度没有太大影响。

在结束之前，我想对我的上一篇文章，[https://medium . com/@ leonardyeo _ XL/evaluating-performance-on-classic-sorting-algorithms-in-python-and-rust-76f 981 DFC 0 c，](https://medium.com/@leonardyeo_xl/evaluating-performance-on-classic-sorting-algorithms-in-python-and-rust-76f981dfc0c)进行一个简短的介绍，如果你对性能评估之类的东西感兴趣的话。

[](https://www.eduelk.com/) [## 教育

### 通过我们的练习题为您的下一次技术认证考试树立信心。我们提供课程来加速…

www.eduelk.com](https://www.eduelk.com/) [](https://www.eduelk.com/about-us) [## 关于我们——教育

### 在爱德克教育，我们相信熟能生巧。我们知道准备参加技术认证是…

www.eduelk.com](https://www.eduelk.com/about-us) [](https://www.eduelk.com/shop-for-practice-questions) [## 通过我们负担得起的练习题获得自信——教育

### 编辑描述

www.eduelk.com](https://www.eduelk.com/shop-for-practice-questions) [](https://www.eduelk.com/book-a-lesson) [## 联系方式 1——教育

### 编辑描述

www.eduelk.com](https://www.eduelk.com/book-a-lesson)