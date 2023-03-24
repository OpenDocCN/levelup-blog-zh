# 在 Swift 中从回调到异步/等待

> 原文：<https://levelup.gitconnected.com/from-callbacks-to-async-await-in-swift-aebd38ab0f13>

![](img/98ff7f35209dcbd8e828c19cfbb81de4.png)

照片由[梅姆](https://unsplash.com/@picoftasty?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

闭包是自包含的功能块，可以在你的代码中传递和使用。闭包可以从定义它们的上下文中获取并存储对任何常量和变量的引用。闭包让异步函数接受实际上是另一个函数的参数。闭包的代码完成后，该函数被调用，并带有一个错误或一个值。

现代 Swift 开发涉及大量使用闭包和完成处理程序的异步编程。闭包是编写异步代码最简单的方法。这些都很复杂，但是功能强大且富于表现力，在 iOS 应用程序的开发中被广泛使用。

在本文中，我们将讨论 async/await，这是一种语言扩展，它使异步编程更加自然，更不容易出错。这从奥列格·安德雷耶夫写的一份早期提案中得到一些启发。

# **基于**块的 API 的问题

使用基于块的 API 进行异步编程有很多问题。

## **问题 1:末日金字塔**

我们都遇到过类似这样的嵌套网络回调:

```
func makeSandwich(completionBlock: (result: Sandwich) -> Void) {
    cutBread { buns in
        cutCheese { cheeseSlice in
            cutHam { hamSlice in
                cutTomato { tomatoSlice in
                    let sandwich = Sandwich([buns, cheeseSlice, hamSlice, tomatoSlice] 
                    completionBlock(sandwich))
                }
            }
        }
    }
}

makeSandwich { sandwich in
    eat(sandwich)
}
```

## 问题 2:冗长和旧式的错误处理

错误的处理变得非常困难和冗长。

```
func makeSandwich(completionBlock: (result: Sandwich?, error: NSError?) -> Void) {
    cutBread { buns, error in
        guard let buns = buns else {
            completionBlock(nil, error)
            return
        }
        cutCheese { cheeseSlice, error in
            guard let cheeseSlice = cheeseSlice else {
                completionBlock(nil, error)
                return
            } cutHam { hamSlice, error in
                guard let hamSlice = hamSlice else {
                    completionBlock(nil, error)
                    return
                } cutTomato { tomatoSlice in 
                    guard let tomatoSlice = tomatoSlice else {
                        completionBlock(nil, error)
                        return
                    } let sandwich = Sandwich([buns, cheeseSlice, hamSlice, tomatoSlice]
                    completionBlock(sandwich), nil)
                }
            }
        }
    }
}

makeSandwich { sandwich, error in
    guard let sandwich = sandwich else {
        error("No sandwich today")
        return
    } eat(sandwich)
}
```

## 问题 3:忘记调用完成处理程序

不调用适当的块，直接返回就可以轻松退出。一旦被遗忘，这个问题就很难调试。

```
func makeSandwich(completionBlock: (result: Sandwich?, error: NSError?) -> Void) {
    cutBread { buns, error in
        guard let buns = buns else {
            return // <- forgot to call the block
        } cutCheese { cheeseSlice, error in
            guard let cheeseSlice = cheeseSlice else {
                return *// <- forgot to call the block*
            }
            ...
        }
    }
}
```

## 问题 4:调用完成处理程序后忘记返回

当你没有忘记调用 block 时，你仍然可以在那之后忘记返回。谢天谢地，语法在某种程度上防止了这种情况，但这并不总是相关的。

```
func makeSandwich(recipient: Person, completionBlock: (result: Sandwich?, error: NSError?) -> Void) {
    if recipient.isVegeterian {
        if let sandwich = cachedVegeterianSandwich {
            completionBlock(cachedVegeterianSandwich) *// <- forgot to return after calling the block*
        }
    }
    ...
}
```

## 问题 5:在错误的队列/线程上继续

许多 API 在它们自己的私有队列上调用完成块。用户代码通常希望在自己的私有队列(或主线程)上运行。

忘记将 _async 分派回正确的队列会导致很难调试的问题，这些问题可能会零星地出现，或者出现在程序的一些不相关的部分。

```
func makeSandwich(completionBlock: (result:Sandwich)->Void) {
    cutBread { buns in
        dispatch_async(dispatch_get_main_queue()) {
            cutCheese { cheeseSlice in
                dispatch_async(dispatch_get_main_queue()) {
                    cutHam { hamSlice in
                        dispatch_async(dispatch_get_main_queue()) {
                            cutTomato { tomatoSlice in 
                                dispatch_async(dispatch_get_main_queue()) {
                                    completionBlock(Sandwich([buns, cheeseSlice, hamSlice, tomatoSlice]))
                                }
                            }
                        }
                    }
                }
            }
        }
    }
}
```

# 建议的解决方案:异步/等待

对回调地狱问题最好的解决方案之一是异步/等待。`async/await`，通常被称为`Asynchronous functions`，允许异步代码像直线同步代码一样编写。async/await 的概念很简单:允许强制执行异步代码，也就是说，当异步操作完成时，不使用回调来处理返回值，您只需从普通函数返回一个结果，编译器就会为您完成剩下的工作。

函数可以选择加入*异步语义*，方法是使用`async`关键字并用通常的返回类型替换闭包变量:

```
// Before:
func makeSandwich(completionHandler: (result: Sandwich) -> Void)// After:
async func makeSandwich() -> Sandwich
```

在声明函数时，我们在返回值之前添加了单词`async`，在调用函数之前添加了`await`。

第一个示例可以使用 async/await 以更自然的方式重写，如下所示:

```
async func cutBread() -> Bread
async func cutCheese() -> Cheese
async func cutHam() -> Ham
async func cutTomato() -> Vegetable

async func makeSandwich() -> Sandwich {
    let bread  = await cutBread()
    let cheese = await cutCheese()
    let ham    = await cutHam()
    let tomato = await cutTomato()
    return Sandwich([bread, cheese, ham, tomato])
}
```

异步代码*以与写入相同的顺序开始*的执行。一些操作可能需要在其他操作完成后开始，其他操作可能需要并行运行。通过在适当的地方使用`await`，您可以指定哪些操作等待哪些操作。

注意`await`并没有阻塞线程，它只是告诉编译器将剩余的调用组织成等待操作的继续。使用`await`没有死锁的风险，也不可能用它来让当前线程等待结果。

# 结论

async/await 一点都不新鲜。微软已经在. Net 中实现了它，JavaScript 也支持这个特性。就 Swift 而言，开发人员多年来一直提议增加这一功能，并最终获得批准。

异步功能对 Swift 开发人员来说是个好消息。这种方法的最大好处是**异步代码的编写方式与同步代码**相同，这是一个非常非常大的优势！

我希望这篇文章能说服你们中的一些人去看看异步函数，从回调函数变成看起来更干净、更容易使用的函数。

我希望你喜欢这个简短的介绍。你可以在 [Rahul Garg](https://www.linkedin.com/in/rahulgarg12/) 找到我。