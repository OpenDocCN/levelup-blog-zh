# 如何运行具有副作用的未来遍历

> 原文：<https://levelup.gitconnected.com/how-to-run-future-traverse-that-has-a-side-effect-2c4f466c7659>

![](img/df8c5cd5b385bf7640fd138113f10485.png)

最初发表于[https://edward-huang.com](https://edward-huang.com/scala/functional-programming/2020/04/14/how-to-run-future-traverse-that-has-side-effect/)

让您的程序异步已经成为性能和可伸缩性的不二法门。然而，异步编程的缺陷和问题很难解决。

几天前，我向我的一位同事解释了未来是如何运作的。我们讨论了如何执行一个未来列表来创建一个未来列表。

你可能知道，操作本身已经被广泛使用，标准的 Scala 库有专门解决问题的函数，`Future.traverse`和`Future.sequence`。

当我们深入研究`traverse`和`sequence`的实现时，我想知道 foldLeft 在未来执行时是否会阻塞。

在对未来如何运作进行了无数的研究和理解后，我决定把我的发现和见解贴出来，这样你在处理未来时也能解决一些问题。

# 问题陈述

让我们考虑一个问题，您需要创建一个函数 getPrices，在这里您给出一个咖啡价格的 URL 列表。你批量提取。汇总所有 URL 中咖啡的价格，并将价格存储在现有的数据库中。将聚集的数据返回给用户。

一个条件是 URL 有重复。这意味着，它可能提供多个相同的 URL，返回相同的价格。因此，您也需要处理这种情况。

```
case class Coffee(url:String, price:Int)

*// assuming this the fetch method for coffee* def fetch(url:String): Future[Int] = ???

*// write to DB for existing coffee* def writeToDB(coffee:Coffee): Future[Unit] = ???

*// read value from database* def readfromDB(url:String): Future[Coffee] = ???

def isExistInDB(coffee:Coffee): Future[Boolean] = ???

*// your function here* def batchCoffeePrice(coffeeUrls:List[String]): Future[List[Coffee]] = ???
```

当您将`Coffee`写入数据库时，您需要检查该值是否存在于数据库中，然后再写入该值。

假设价格不会因为本文的说明而改变。

# 执行顺序

一种方法是检查数据库中是否存在该值。如果它不存在，获取 URL 并写入数据库。如果确实存在，从数据库中获取值并返回给用户。

因此，执行是:

1.  检查数据库中是否存在该值
2.  如果该值存在，则获取该值并遍历下一个 URL。
3.  如果值不存在，获取 URL 并创建一个 coffee 实例。
4.  将 coffee 实例写入数据库。

因此，第一本能会是这样做:

```
def batchCoffeePrice(coffeeUrls:List[String]): Future[List[Coffee]] = Future.traverse(coffeeUrls){url =>
  isExistInDB(url).flatMap{ boolean =>
    if(boolean) {
      readFromDB(url)
    }
    else {
      fetch(url).flatMap{
        price =>  {
          val coffee = Coffee(url,price)
          writeToDB(coffee)
          coffee
        }
      }
    }
  }
}
```

看起来不错，逻辑似乎也行得通。但是，当调用`batchCoffeePrice`时，如果 coffeeUrls 是重复的，它会将多个相同的值写入数据库。

这似乎很奇怪。既然`isExistInDB`应该已经检查它是否存在于数据库中，然后执行操作，为什么它仍然有重复的写操作？

# 深入遍历

问题靠`Future.traverse`。遍历是顺序执行还是并行执行未来列表？

如果你研究一下`Future.traverse`的实现:

```
def traverse[A](initial:List[A])(f:A => Future[B]): Future[List[B]] = initial.foldLeft(List.empty[B]){(acc, currA) => 
  val res = f(currA)
  for{
    a <- acc
    b <- res
  } yield a :+ b
}
```

你注意到 foldLeft 中的 Traverse implements，`val res = f(currA)`解释了导致函数不一致的 bug。

上面的遍历函数无阻塞地遍历所有的未来列表。不过，既然未来**热切**，那就看你怎么实现你的`f`功能了。它可以是顺序的，也可以是并行的。

返回语句`Future[List[B]]`没有区别，因为在函数结束时，它返回所有结果`f(currA)`。但是，如果你想用`f(currA)`做一些事情，你需要意识到，尤其是处理一些急切和副作用的事情，它是并行的。

如果你想在将来处理副作用，比如获取数据库，你需要按顺序执行值列表，因为**顺序**很重要。

为了解释上面的代码，函数将首先触发，然后连接到 for-comprehensive。因此，并行调用`List[A]`中的所有值。然而，如果我们像这样将函数签名改为`lazy val res`:

```
def traverse[A](initial:List[A])(f:A => Future[B]): Future[List[B]] = initial.foldLeft(List.empty[B]){(acc, currA) => 
  lazy val res = f(currA)
  for{
    a <- acc
    b <- res
  } yield a :+ b
}
```

我们解决了让异步值顺序运行而不是并行运行的问题。

# 解决问题

为了解决这个问题，我们实现了 foldLeft，并遍历自己，使值懒惰。

如果我们想重构上面的代码并放入`Future.traverse`，你需要再次检查`f`函数`ifExistInDB`的内部。

```
def batchCoffeePrice(coffeeUrls:List[String]): Future[List[Coffee]] = Future.traverse(coffeeUrls){url =>
  isExistInDB(url).flatMap{ boolean =>
    if(boolean) {
      readFromDB(url)
    }
    else {
    for {
      price <- fetch(url)
      exist <- isExistInDB(url)
    } yield {
        val coffee = Coffee(url,price)
        if(exist) writeToDB(coffee) 
        coffee
      }
    }
  }
}
```

但是，你最好不要用 Future。遍历并使用`foldLeft`并使值连续，以在函数中创建更少的 IO。

# 主要外卖

*   `Future.traverse`适合执行一个互不依赖的列表(或者你未来的副作用)。如果你想在未来实现这个功能。执行并行，你可以做未来。
*   未来是天生渴望的。因此，一旦你调用`Future{something}`它就会立即执行。为了不让它急，可以改成 IO。
*   Future.sequence 使用未来。在引擎盖下穿行。