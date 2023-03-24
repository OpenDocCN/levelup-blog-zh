# Clojure 中的 Reducers

> 原文：<https://levelup.gitconnected.com/reducers-in-clojure-c088a5627412>

![](img/b6687ea37432fe961b759c7b2725ad45.png)

照片由[诺亚·布舍尔](https://unsplash.com/@noahbuscher?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本帖中，我们将探索 reducer 和 Clojure 的高性能 reducer 库。当第一次接触 Clojure 时，很容易忽略学习或使用 reducers，因为递归性质、高阶函数和通用术语一开始可能会令人望而生畏。

让事情变得更容易理解的关键是了解主 reduce 函数如何遍历集合来产生一个值。从那里，我们可以看到一些要减少的参数，看看我们如何利用高阶函数来分离和抽象转换操作，使多个转换链接在一起。

但是在我们继续学习本教程之前，我认为我们应该首先看看为什么如果我们想提高性能，那么努力去真正理解并在代码中使用 reducers 是值得的。

## 为什么要费心学习如何使用减速器？

学习如何正确使用 reducers 的主要原因是，它为您提供了一套强大的工具，通常可以帮助您更快地并行执行代码。

缩减器也构成了*转换器*的基础，后者是从一个缩减函数到另一个缩减函数的转换，并提供了与其输入和输出源的上下文无关的更高级别的可组合转换。我们将在以后的文章中探讨转换器，但本质上它们利用 reducers 使您的处理函数更具可重用性和灵活性，这样您就可以在许多不同的东西上使用相同的转换器，如 core.async 通道或集合，而不必为每个输入数据类型提供特定的实现。

使用 reducers 而不是核心序列处理函数消除了处理过程中需要的一些中间集合，并且它们还消除了一些由懒惰引起的处理开销，最终使您的代码对于一些问题运行得更快。

到目前为止，您可能已经熟悉了基本的序列函数，比如`map` 和`filter`，但是您知道它们是 clojure.core.reducers 名称空间中这些函数`r/map`和`r/filter`的基于 reducer 的版本吗？

理解这些函数是如何工作的并不重要，但一个关键的见解是要认识到像 map 和 filter 这样的函数可以写成 reducing 操作。

以`clojure.core`中的`map` 为例:

```
(defn map                        
 "Returns a lazy sequence consisting of the result of applying f to                         the set of first items of each coll, followed by applying f to the                         set of second items in each coll, until any one of the colls is                         exhausted.  Any remaining items in other colls are ignored. Function                         f should accept number-of-colls arguments. Returns a transducer when                         no collection is provided."                         
{:added "1.0" :static true} ([f] 
 (fn [rf] 
  (fn ([] (rf)) 
    ([result] (rf result))
    ([result input] (rf result (f input))) 
    ([result input & inputs] (rf result (apply f input inputs)))))) ([f coll] (lazy-seq (when-let [s (seq coll)]
      (if (chunked-seq? s)
         (let [c (chunk-first s)
               size (int (count c))
               b (chunk-buffer size)]       
            (dotimes [i size]
              (chunk-append b (f (.nth c i))))
               (chunk-cons (chunk b) (map f (chunk-rest s))))
            (cons (f (first s)) (map f (rest s)))))))   ([f c1 c2] 
       (lazy-seq 
          (let [s1 (seq c1) s2 (seq c2)]
           (when (and s1 s2)
             (cons (f (first s1) (first s2))
                   (map f (rest s1) (rest s2)))))))        ([f c1 c2 c3] 
         (lazy-seq 
            (let [s1 (seq c1) s2 (seq c2) s3 (seq c3)]
               (when (and  s1 s2 s3)
                   (cons (f (first s1) (first s2) (first s3))
                         (map f (rest s1) (rest s2) (rest s3)))))))      ([f c1 c2 c3 & colls]
        (let [step (fn step [cs] 
          (lazy-seq 
            (let [ss (map seq cs)] 
              (when (every? identity ss)  
                    (cons (map first ss) (step (map rest ss)))))))]
          (map #(apply f %) (step (conj colls c3 c2 c1))))))
```

虽然 map 确实是一个有用的函数，但它充满了处理不同类型的实现细节，看起来有点复杂。现在让我们看看`r/map`是如何在我们的`clojure.core.reducers`名称空间中定义的:

```
(defcurried map                         
"Applies f to every value in the reduction of coll. Foldable."                         {:added "1.5"}
[f coll] 
(folder coll 
  (fn [f1]
    (rfn [f1 k]
      ([ret k v](f1 ret (f k v)))))))
```

整个代码更简单、更通用，并尽可能利用文件夹进行并行操作。酷的是，我们也可以使用这些基于 reducer 的版本，语法与我们使用基于序列的转换器相似，主要的警告是我们必须对它们进行约简以获得最终值，因为 reducer 总是返回一个`reducable`而不是一个集合。

让我们看一个人为的例子；一个使用顺序处理功能`map`、`filter` 和`reduce`:

```
(time (->> (range 100000000)
           (map #(* % 10))
           (filter odd?)
           (reduce *)))
"Elapsed time: 3533.9617 msecs"
```

通过切换到这些函数的简化版本，我们可能会获得一些适度的性能提升。

```
(require '[clojure.core.reducers :as r])
(time (->> (range 100000000)
           (r/map #(* % 10))
           (r/filter odd?)
           (r/reduce *)))
"Elapsed time: 2966.89 msecs"
```

对于勇敢和真实的人来说，Clojure 中可能有更好的性能提升的例子，但是我想指出的要点是，上面的语法看起来很相似，并且获得了相同的结果。然而，我们基于序列的处理链中的`map` 和`filter` 步骤导致在每一步之后产生中间集合，而 reducers 不产生集合，而是在每一步返回一个`reducable`。

把一个`reducable`想象成一个食谱或者一套关于如何执行任务的指令。这很有帮助，因为`reducables`可以很好地组合在一起，而不必实现每个中间步骤的输出。最后，当我们将可还原的值还原或折叠成最终值时，就产生了一个集合。

如果我们想并行执行 reducables，我们必须使用 r/fold 或 r/foldcat 和一个可折叠的集合(vector 或 map)。或者，我们可以只调用 reduce 或 into(在内部调用 reduce)来将 reducables 实现为一个集合。

reduce、r/reduce 和 r/fold 的性能将取决于我们正在解决的特定问题，但一般原则是，使用 reducer 编写集合处理函数通常会使您的代码更具性能，尤其是在处理大型集合或计算密集型任务时。

## 减速器的解剖

Clojure 的创建者 Rich Hickey 之前在一篇名为[Reduer 剖析](https://clojure.org/news/2012/05/15/anatomy-of-reducer)的优秀文章中写过关于 reducer 的博客。我发现这个帖子真的很有趣，但是要完全掌握 reducer 库背后的思想仍然需要一点点的实验。

因此，在本教程的剩余部分，我们将通过提供一些相关的信息和示例，尝试遵循 Rich Hickey 在 reducers 及其工作方式背后的一些基本原理。为了从本教程中获得最大收益，你应该在此之后立即阅读 Rich 的文章，看看你是否能跟上。我的希望是，在两篇文章之间，我们应该能够填补一些空白，以解释 reducers 背后的一些核心思想和推导。毕竟，学习一个新思想或概念的最好方法是从许多不同的来源阅读它。

我已经尽了最大努力来解释和扩展 Rich 最初的想法，并提供了额外的见解，但我会添加一个小小的免责声明，这只是我的解释。Rich 是一个聪明的家伙，对这个主题有着深刻的理解，所以 Rich Hickey 对 x 的看法的唯一来源最终应该是 Rich Hickey。每当我在本文的其余部分提到 Rich、他的想法或意图时，请记住这一点。

## 那么，到底什么是减速器呢？

在高层次上，reduce 函数是一个接受两个参数并传递给名为 reduce 的函数的函数。reduce 函数的行为由函数体本身决定，但 reduce 调用它的方式永远不会改变。我们的归约函数的第一个参数称为结果或累加器。它被称为*结果*,因为它通常包含最后一次归约调用的结果，当您对某种值求和或累加时，它也被称为*累加器*。

第二个参数是要减少的源的新输入值。reduce 函数将为集合中的每一项调用该函数，直到返回最终结果或者集合中不再有项。

让我们看看 reduce 的行为:

```
> (reduce (fn [result input] (println input)) [1 2 3 4 5])
2
3
4
5
```

如果仔细观察这个输出，您会注意到集合中的第一项 1 没有作为输入参数传入。这是因为我们的归约函数应该被设计成从第一个元素开始，将两个或更多的元素归约为一个值。因此，如果我们没有传递要减少的初始化值，第一个元素最初总是作为结果传入，如下所示:

```
> (reduce (fn [result input] (println result)) [1 2 3 4 5])
1
nil
nil
nil
```

我们还可以通过传入一个初始化值来改变这种行为，这样我们就可以遍历集合中的每一项:

```
> (reduce (fn [result input] (println input)) 0 [1 2 3 4 5])
1
2
3
4
5
=> nil
```

从这些基本的例子中，我们可以看到我们的函数被调用了(n-1)次或 n 次，这取决于初始值。对于后续的调用，返回值将被填充上一次调用 reducer 的结果。如果我们从函数中返回一个值，我们可能会更清楚地看到这一点:

```
> (reduce (fn [result input] 
            (println result) 
            "apple") [1 2 3 4 5])
1
apple
apple
apple
```

现在让我们让我们的减速器做一些更有用的事情:

```
> (reduce (fn [result input] 
            (println result) 
            (+ result input)) [1 2 3 4 5])
;; It might be helpful to see the [result input] args:
1  ;; args = [1 2], result = 3
3  ;; args = [3 3], result = 6
6  ;; args = [6 4], result = 10
10 ;; args = [10 5], result = 15
=> 15
```

仔细想想，调用 reduce 的 reduce 函数有点傻。它一直以下面的模式用两个参数调用我们的 reducer，直到我们的集合为空(或者返回一个 *reduced* 集合):

```
[1 2] => result1
[result1 3] => result2
[result2 4] => result3
[result3 5] => result4
```

因此，任何设计用来处理两个参数并返回值的函数都可以与 reduce 一起使用。例如，我们可以使用+或字符串:

```
> (reduce + [1 2 3 4 5])
=> 15
> (reduce str [1 2 3 4 5])
=> "12345"
```

或者我们可以在单个 arity 函数上使用&运算符:

```
> (reduce (fn [& args] (println "args =" args)) [1 2 3 4 5])
args = (1 2)
args = (nil 3)
args = (nil 4)
args = (nil 5)
=> nil
```

Reduce 不仅适用于 vectors，我们还可以将它用于 map，从而减少每个键值 MapEntry 对:

```
> (reduce (fn [result input]
          (println "[" result ", " input "]")) 
{:name "Frank" :surname "Sinatra" :age 67 :description "Artist"})[ [:name Frank] ,  [:surname Sinatra] ]
[ nil ,  [:age 67] ]
[ nil ,  [:description Artist] ]
```

既然我们已经牢牢掌握了 reduce，我们还应该知道 reducer 如何通过显式调用 *reduced* 来简化 reducer:

```
> (reduce (fn [result input]
          (println "[" result "," input "]")
          (if (= input 5)
            (reduced "reduced")
            (+ result input))) [1 2 3 4 5 6 7 8 9 10])
[ 1 , 2 ]
[ 3 , 3 ]
[ 6 , 4 ]
[ 10 , 5 ]
=> "reduced"
```

这对于处理无限惰性序列特别有帮助，因为没有它，缩减永远不会结束！

```
Try crashing your repl with:
(reduce println (range))Or use reduce to terminate an infinite lazy-sequence reduction early:(reduce (fn [result input] 
   (if (< input 5) 
       (println input)
       (reduced "finished"))) (range))
1
2
3
4
=> "finished"
```

写 reducer 的时候，我们也可以用 reduced？fn 来查看一个值是否已经在 reduced 函数中包装过。

## 转换减速器…

在本文的第一部分中，我们已经看到了 reduce 如何使用一个简单的 reduce 函数，递归地应用它来转换一个集合。我们可能希望将许多不同类型的转换函数应用于数据集合，例如`map` 和`filter`。酷的是，这些也可以被定义为减速器。

既然我们已经了解了什么是减速器，以及它是如何工作的，我们可以回到 Rich Hickey 的博客文章，来研究下一个想法:改造减速器。

```
(xf reducing-fn) -> reducing-fn
```

Rich 在他的文章中告诉我们，转换 reducer 是 reducer 库中许多核心集合操作背后的概念。例如，他告诉我们想象一个新的高阶映射函数，它返回一个缩减器。

```
(defn mapping [f]
  (fn [f1]
    (fn [result input]
      (f1 result (f input)))))
```

如果我们查看上面的映射函数，我们会注意到我们有一个函数`f`，它对每个新输入进行操作，还有一个函数`f1`，它对结果进行操作。请注意，高阶函数是如何根据函数定义的，并且不包含硬编码的映射函数。

现在让我们用它来创建一个函数，该函数增加每个输入并对结果求和:

```
(reduce ((mapping inc) +) 0 [1 2 3 4])
```

在上面的例子中，`f` 是`inc` 函数，它将递增每个后续输入，`f1` 是我们的`+`操作符，它将结果值添加到我们现有的结果中。如果你以前没有用过高阶函数，那么试着把它分解一下，以理解它是如何工作的。

```
;; Our main format is
(reduce reducer-fn initial-value coll);; When we call (mapping inc) we get back a new function:
(fn [f1]
    (fn [result input]
      (f1 result (inc input))))
;; When we call ((mapping inc) +), we pass + into our previous function to give our final reducer function:
    (fn [result input]
      (+ result (inc input))
```

如果我们将其与之前的 reduce 示例进行比较，我们可以看到，我们已经用对`mapping` 的调用消除了对 *map* 的显式调用，我们的函数只包含+和 inc 操作。该逻辑也不包括任何集合或顺序的概念。

```
;; Previous form
(reduce + 0 (map inc [1 2 3 4]))
;; new form
(reduce ((mapping inc) +) 0 [1 2 3 4])
```

虽然这可能看起来不太令人印象深刻，但是通过使用我们的高阶映射函数返回一个 reducer 并对每个输入应用一个转换，我们可以将收集的数据与转换分开。我们也可以对返回 reducers 的其他高阶函数使用相同的模式，例如:

```
(reduce ((my-filtering even?) +) 0 [1 2 3 4])
```

注意我们的`mapping` 和`my-filtering` 函数是如何与收集数据本身分离的。

这是一个改进，但是`((mapping inc) +)`语法有点笨拙，我们的 reducer 生成函数作为第一个参数出现。如果我们想使用最后线程操作符`->>`将这些线程连接在一起，这将是一个问题。

Rich 还指出这有点奇怪，因为我们操作的是 reducing 操作，而不是 collection 操作。在我们的序列处理世界`map`、`filter` 等中，我们习惯于对集合进行操作——所以我们需要以某种方式修改东西，以便我们的转换函数也对集合起作用，而不仅仅是对归约函数起作用。

为了解决这个问题，Rich 包含了一个名为`reducer` 的额外函数，它减少了集合并应用了一个转换函数 xf。

然而，为了理解`reducer`中发生了什么，让我们快速看一下`clojure.core.reducers.`中的`reduce` 函数

```
(defn reduce
"Like core/reduce except:                           
  When init is not provided, (f) is used. 
  Maps are reduced with reduce-kv"     
([f coll] (reduce f (f) coll))                         
([f init coll]                            
  (if (instance? java.util.Map coll)
    (clojure.core.protocols/kv-reduce coll f init)
    (clojure.core.protocols/coll-reduce coll f init))))
```

您将看到 reduce 最终调用`kv-reduce`(对于地图)或`coll-reduce`来减少集合。这些辅助函数基本上执行不同类型的归约。

让我们检查来自 clojure.core.protocols 的 CollReduce 协议:

```
(defprotocol CollReduce                         
"Protocol for collection types that can implement reduce faster than                         first/next recursion. Called by clojure.core/reduce. Baseline                         implementation defined in terms of Iterable."                         (coll-reduce [coll f] [coll f val]))
```

我们可以从 CollReduce 协议的定义中看到，它为不同的类型提供了比第一次/下一次递归更快的归约方式，在 clojure.core.protocols 文件中，我们可以看到 CollReduce 的定义扩展到了类似于`Objects`、`LazySeq`和`PersistentVector`的东西。

```
;; n.b Some extensions of CollReduce are hidden for brevity
(extend-protocol CollReduce 
 nil 
 (coll-reduce 
   ([coll f] (f)) 
   ([coll f val] val))Object 
 (coll-reduce 
    ([coll f] (seq-reduce coll f))
    ([coll f val] (seq-reduce coll f val)));;for range                        
 clojure.lang.LazySeq                     
   (coll-reduce                          
      ([coll f] (seq-reduce coll f))    
      ([coll f val] (seq-reduce coll f val)));;vector's chunked seq is faster than its iter                         clojure.lang.PersistentVector                         
  (coll-reduce                         
     ([coll f] (seq-reduce coll f))                          
     ([coll f val] (seq-reduce coll f val)))
```

现在让我们再来看看 Rich 介绍的新减速器功能:

```
(defn reducer                         
"Given a reducible collection, and a transformation function xf,                         returns a reducible collection, where any supplied reducing                         fn will be transformed by xf. xf is a function of reducing fn to                         reducing fn."                         
{:added "1.5"}                        
 ([coll xf]                           
    (reify 
      clojure.core.protocols/CollReduce
      (coll-reduce [this f1]
         (clojure.core.protocols/coll-reduce this f1 (f1)))
      (coll-reduce [_ f1 init]
         (clojure.core.protocols/coll-reduce coll (xf f1) init)))))
```

敏锐的观察者可能会注意到，`reducer` 函数包含对`coll-reduce`的调用，就像我们最初的`reduce` 函数一样。您还会看到，在调用`coll-reduce`之前，转换函数`xf` 被应用于缩减函数。

这个`reducer` fn 现在可以和我们的映射函数一起使用，返回一个应用了适当变换的 reducer:

```
(reduce + 0 (reducer [1 2 3 4] (mapping inc)))
```

这里，reducer 将接受一个集合 *coll* 和一个转换函数 *xf* ，并返回一个为 reduce 函数提供数据的 *reducer* 。这看起来像是更多的工作，但是它给了我们轻松链接 reducers 的能力，我们有了操作集合而不是 reducing 函数本身的语法。这对于保持良好的解耦非常重要。

现在，我们已经通过在调用 reduce 之前对集合本身执行 reducing 转换，有效地预处理了集合。如果你仔细观察，你会发现我们在一个 reducing 函数+上调用 reduce，我们的转换结果(`mapping`)在我们的集合上减少了。这也是有益的，因为这意味着我们可以改变我们的减速器的工作方式，而不必修改任何其他的减速功能变压器。

让我们看看是否可以通过修改我们的 reducer 和映射函数来理解我们的函数是如何工作的，以便在调用它们时打印一些调试输出。

```
(require '[clojure.core.reducers :as r])
(require '[clojure.core.protocols :as p])(defn my-reducer
  "Given a reducible collection, and a transformation function xf,
  returns a reducible collection, where any supplied reducing
  fn will be transformed by xf. xf is a function of reducing fn to
  reducing fn."
  ([coll xf]
   (println "my-reducer: called with coll " (str coll))
   (reify
     clojure.core.protocols/CollReduce
     (p/coll-reduce [this f1]
         (clojure.core.protocols/coll-reduce this f1 (f1)))
     (p/coll-reduce [_ f1 init]
       (clojure.core.protocols/coll-reduce coll (xf f1) init)))))

(defn my-mapping [f]
  (fn [f1]
    (fn [result input]
      (println "my-mapping: calling f1 on the result and f on our input for [" (str result) "," (str input)"]")
      (f1 result (f input)))))
```

如果我们现在像 Rich Hickey 的例子一样使用自定义映射和 reduce 函数，那么我们应该能够看到正在发生的事情:

```
> (reduce + 0 (my-reducer [1 2 3 4] (my-mapping inc)))
my-reducer: called with coll  [1 2 3 4]
my-mapping: calling f1 on the result and f on our input for [0,1]
my-mapping: calling f1 on the result and f on our input for [2,2]
my-mapping: calling f1 on the result and f on our input for [5,3]
my-mapping: calling f1 on the result and f on our input for [9,4]
=> 14
```

对 my-mapping 的每次调用都将 inc 应用于每个输入，然后将它添加到上一次调用的结果中。所以我们得到:

```
[0, 1] => (+ 0 (inc 1)) == 2
[2, 2] => (+ 2 (inc 2)) == 5
[5, 3] => (+ 5 (inc 3)) == 9
[0, 4] => (+ 9 (inc 4)) == 14
```

一般模式如下:

```
(reduce f init coll)
f - The function that takes two inputs [result input] and produces the result for each step in the result
init - The initial result value
coll - The collection to reduceWith our reducer function, we are calling:
(reducer coll f)
f - The function which will reduce over the collection coll
coll - The collection to operate on
```

在迄今为止的例子中，我们一直使用映射函数通过`reducer`来转换我们的集合。但是我们也可以使用任何其他的 reducer 转换，只要它返回一个 reducer 函数。

在 Rich 的例子中，我们可以看到如何用不同的函数调用 reducer 来生成不同的转换。

```
(defn rmap [f coll]
  (reducer coll (mapping f)))

(defn rfilter [pred coll]
  (reducer coll (filtering pred)))

(defn rmapcat [f coll]
  (reducer coll (mapcatting f)))
```

现在让我们来看看过滤:

```
(defn my-filtering [pred]
  (println "my-filtering called with predicate " (str pred))
  (fn [f1]
    (fn [result input]
      (println "my-filtering now operating on [result, input] [" (str result)"," (str input)"]")
      (if (pred input)
        (f1 result input)
        result))))
```

如果我们用 my-filtering 替换 my-mapping fn，我们可以看到我们的过滤函数是如何工作的:

```
> (reduce + 0 (my-reducer [1 2 3 4] (my-filtering even?)))
my-filtering called with predicate  clojure.core$even_QMARK_@61dc54a9
my-reducer: called with coll  [1 2 3 4 5 6]
my-filtering now operating on [result, input] [ 0 , 1 ]
my-filtering now operating on [result, input] [ 0 , 2 ]
my-filtering now operating on [result, input] [ 2 , 3 ]
my-filtering now operating on [result, input] [ 2 , 4 ]
my-filtering now operating on [result, input] [ 6 , 5 ]
my-filtering now operating on [result, input] [ 6 , 6 ]
```

看这个例子我们可以看到。应用于每个输入，如果为真，则修改结果。

```
[0, 1] => (even? 1) == false, so return result 0
[0, 2] => (even? 2) == true, so return (+ result 2) = 2
[2, 3] => (even? 3) == false, so return result 2
[2, 4] => (even? 4) == true, so return (+ result 4) = 6
[6, 5] => (even? 5) == false, so return result
[6, 6] => (even? 6) == true, so return (+ result 6) = 12
```

酷，所以我们的 reducer 函数 my-reducer 与 map 和 filter 操作一起工作，返回一个由 f:

```
(my-reducer coll f)
(my-reducer [1 2 3 4 5 6] (my-mapping inc))
(my-reducer [1 2 3 4 5 6] (my-filtering even?))
```

有趣的是，我们可以通过使用另一个 my-reducer 调用来代替 coll:

```
(reduce + 0 
  (my-reducer 
    (my-reducer [1 2 3 4 5 6] 
       (my-filtering even?))
  (my-mapping inc)))(reduce + 0 (my-reducer (my-reducer [1 2 3 4 5 6] (my-filtering even?)) (my-mapping inc)))my-filtering called with predicate  clojure.core$even_QMARK_@61dc54a9
my-reducer: called with coll  [1 2 3 4 5 6]
my-reducer: called with coll  app.core$my_reducer$reify__2320@263c9337
my-filtering now operating on [result, input] [0 , 1]
my-filtering now operating on [result, input] [0 , 2]
my-mapping: calling f1 on the result and f on our input for [0 , 2]
my-filtering now operating on [result, input] [3 , 3]
my-filtering now operating on [result, input] [3 , 4]
my-mapping: calling f1 on the result and f on our input for [3 , 4]
my-filtering now operating on [result, input] [8 , 5]
my-filtering now operating on [result, input] [8 , 6]
my-mapping: calling f1 on the result and f on our input for [8 , 6]
=> 15
```

还记得我们的线程宏吗？

```
(reduce + 0 (-> [1 2 3 4 5 6]                
                (reducer (my-filtering even?))
                (reducer (my-mapping inc))))
```

这是可行的，但是当我们使用集合时，如果我们能使用 thread-last 会更好。让我们定义一些助手函数:

```
(defn rmap [f coll]
  (my-reducer coll (my-mapping f)))

(defn rfilter [pred coll]
  (my-reducer coll (my-filtering pred)))
```

酷，现在我们可以这样做了:

```
(reduce + 0 (->> [1 2 3 4 5 6]
                 (rfilter even?)
                 (rmap inc)))my-filtering called with predicate  clojure.core$even_QMARK_@61dc54a9
my-reducer: called with coll  [1 2 3 4 5 6]
my-reducer: called with coll  app.core$my_reducer$reify__2320@47376c50
my-filtering now operating on [result, input] [0 , 1]
my-filtering now operating on [result, input] [0 , 2]
my-mapping: calling f1 on the result and f on our input for [0 , 2]
my-filtering now operating on [result, input] [3 , 3]
my-filtering now operating on [result, input] [3 , 4]
my-mapping: calling f1 on the result and f on our input for [3 , 4]
my-filtering now operating on [result, input] [8 , 5]
my-filtering now operating on [result, input] [8 , 6]
my-mapping: calling f1 on the result and f on our input for [8 , 6]
=> 15
```

这是怎么回事？使用这些基于 reducer 的函数，我们获得了一些很好的性质。

*   我们得到了与地图、过滤函数相同的基本行为
*   高阶助手函数抽象出了实现细节，因此我们的函数参数只需要基本操作
*   我们现在可以将这些转换串联起来
*   我们最终要求我们正在处理的集合减少它们自己，所以我们的转换减少器不需要知道关于它们的任何事情
*   没有中间集合，一切都是一个缩减，reducer 将与任何缩减函数转换器一起工作
*   我们现在可以利用使用 r/fold 的并行处理，这是我们的转换操作现在使用的默认方法，除非我们需要顺序处理，例如 take。
*   我们的代码现在具有与基于 seq 的函数相似的语义，例如`(reduce + 0 (rmap inc [1 2 3 4]))`

> 多种多样的集合转换可以被表示为缩减函数转换，并且跨多种多样的数据结构应用于顺序和并行上下文中。—里奇·希基

## 我们什么时候应该使用 Reducer 转换？

一个很好的经验是，当您有可以从并行化中获益的转换时，考虑使用基于 reducer 的转换操作。请记住，它们可以帮助您节省内存，并且在处理大型集合或进行大量复杂的计算密集型处理时可以快几个数量级。

序列转换和基于 reducer 的转换之间的一个很大的区别是序列转换通常是延迟应用的，所以这也可能成为您决策的一个因素。

**有用资源:**

[](https://clojure.org/news/2012/05/15/anatomy-of-reducer) [## 减速器的解剖

### 虽然寻找另一种定义核心集合操作的基本方法是一项有趣的工作，但最终…

clojure.org](https://clojure.org/news/2012/05/15/anatomy-of-reducer)  [## 了解你的减压器

### 您已经看到，在大多数情况下，您可以像 seq 函数一样使用 reducers。例如，这会产生相同的…

www.braveclojure.com](https://www.braveclojure.com/quests/reducers/know-your-reducers/) [](https://medium.com/formcept/performance-optimization-in-clojure-using-reducers-and-transducers-a-formcept-exclusive-375955673547) [## 使用减速器和传感器优化 Clojure 的性能——form cept 独有

### 为什么是减速器

medium.com](https://medium.com/formcept/performance-optimization-in-clojure-using-reducers-and-transducers-a-formcept-exclusive-375955673547)