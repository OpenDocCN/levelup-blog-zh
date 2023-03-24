# Go:使用闭包代替结构

> 原文：<https://levelup.gitconnected.com/go-using-a-closure-in-place-of-a-struct-d56ddd1e55f9>

![](img/032875e724a58c60b9967a56eb8bdf49.png)

这篇笔记并不是建议遵循一种模式，而是一种思考练习。让我们看看在惯用结构的地方使用闭包。

## 斐波那契数列发生器

对于这个思想练习，我们将以两种方式实现一个[斐波那契数](https://en.wikipedia.org/wiki/Fibonacci_number)序列生成器，一次使用*结构*，一次使用[闭包](https://gobyexample.com/closures) 。我们将在两者中使用相同的方法进行比较。

## 作为一个结构

因此，这里有一个简单且惯用的结构实现:

```
package mainimport "fmt"type Fibonacci struct {
  x1, x2 int
}func NewFibonacci() *Fibonacci {
  return &Fibonacci{-1,1}
}func (f *Fibonacci) Next() int {
  f.x1, f.x2 = f.x2, f.x1 + f.x2
  return f.x2
}func main() {
  f := NewFibonacci()
  for i := 0; i < 10; i++ {
    fmt.Println(f.Next())
  }
}
```

我们声明结构( *Fibonacci* )、工厂函数( *NewFibonacci* )和下一个函数( *Next* )。

## 作为一个结束

让我们实现与闭包相同的内容:

```
package mainimport "fmt"func fibonacci() func() int {
  x1, x2 := -1, 1
  return func() int {
    x1, x2 = x2, x1 + x2
    return x2
  }
}func main() {
  f := fibonacci()
  for i := 0; i < 10; i++ {
    fmt.Println(f())
  }
}
```

只有一个带闭包的函数(*斐波那契*)。

## 思想

*   *闭包*更干净，更容易理解，代码更少。它隐式地封装了状态数据和工厂函数。
*   这种情况是人为设计的，因为我们只想要 Fibonacci 中的一个函数。当我们想读取值而不更新它或添加额外的函数时，只有一个*结构*会支持它。

不管这里使用闭包有什么好处，这是一个关于闭包能做什么的有趣的思考练习。