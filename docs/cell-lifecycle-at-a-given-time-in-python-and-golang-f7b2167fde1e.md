# Python 和 Golang 中给定时间的细胞生命周期

> 原文：<https://levelup.gitconnected.com/cell-lifecycle-at-a-given-time-in-python-and-golang-f7b2167fde1e>

![](img/7c86223b961563f5b3d94fd69dadccfd.png)

由 [Pawel Czerwinski](https://unsplash.com/@pawel_czerwinski?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 问题

考虑一个细胞，它有以下生命周期:

*   细胞在时间= 0 时诞生
*   当细胞出生时，年龄将为 0
*   当细胞的年龄为 2 时，该细胞产生另一个细胞
*   当细胞的年龄为 4 时，该细胞产生另一个细胞
*   当细胞年龄为 5 时，细胞死亡

假设你在时间= 0 时有 1 个细胞，那么在时间= X 时会有多少个活细胞？

写一个函数返回给定时间 x 的活细胞数。

## 例子

*   时间= 0，细胞= [0] →总存活数= 1
*   时间= 1，细胞= [1] →总存活数= 1
*   时间= 2，细胞= [2，0] →总存活数= 2
*   时间= 3，细胞= [3，1] →总存活数= 2
*   时间= 4，细胞= [4，2，0，0] →总存活数= 4
*   时间= 6，细胞= [4，2，2，0，0，0] →总存活数= 6
*   时间= 11，细胞= [3，3，3，3，3，1，1，1，1，1，1] →总存活数= 1
*   时间= 20，单元格= [4，4，4，4，4，4，4，4，4，4，4，4，4，4，4，4，4，4，4，4，4，4，4，4，4，4，4，4，4，2，2，2，2，2，2，2，2，2，2，2，2，2，2，2，2，2，2，2，2，2，3，3，4，4，4，4，4，4，4，4，4，4，4，4，4，4，2，2，2，2，2，2，2，2，3，3，4，3，4，4，4，4，4，4，4， 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0，0，0，0，0，0，0，0，0，0，0，0，0，0，0，0，0，0，0，0，0，0，0，0，0] →总存活数= 17

# 解决办法

## 计算机编程语言

```
def Solution(t):
  arr = []

  for i in range(t+1):
    if i == 0:
      arr.append(0)

    elif i > 0:
      for k in range(len(arr)):
        arr[k] += 1 for j in range(len(arr)): 
        if arr[j] == 2 or arr[j] == 4:
          arr.append(0)
        elif arr[j] == 5:
          arr[j] = -1

      arr = [num for num in arr if num != -1]

  return len(arr)
```

## 戈朗

```
package mainimport "fmt"func Solution(t int) int {
  arr := make([]int, 0)
  for i := 0; i < t+1; i++ {
    if i == 0 {
      arr = append(arr, 0)
    } else if i > 0 {
      for k := 0; k < len(arr); k++ {
        arr[k] += 1
      } for j := 0; j < len(arr); j++ {
        if arr[j] == 2 || arr[j] == 4 {
          arr = append(arr, 0)
        } else if arr[j] == 5 {
          arr[j] = -1
        }
      } tempArr := make([]int, 0)
      for _, val := range arr {
        if val != -1 {
          tempArr = append(tempArr, val)
        }
      }
      arr = tempArr
    }
  }
  return len(arr)
}func main() {
  fmt.Println(Solution(20))
}
```

## 时间和空间复杂性

*   时间复杂度:O(T * (N+N+N))。给定 T 是时间数，N 是活细胞数。
*   空间复杂度:O(N)。使用一个临时数组来存储没有被`-1`的活细胞。

# 外卖食品

感谢您阅读这个简短的解题问题。如果有人知道更好或更快的时间复杂度来解决这个问题，请随意评论和反馈。和平！✌️