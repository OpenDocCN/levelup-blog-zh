# 大坚果壳中的约束编程🐚

> 原文：<https://levelup.gitconnected.com/cp-solver-in-a-big-nut-shell-ba03f8fdd8b9>

## 我自己理解的约束编程

![](img/a217ef8a7b11a663b391669db4ceee18.png)

# 这是怎么回事？

我最近有机会在一个工作项目中使用约束编程，创建一个“调度程序”功能，可以帮助人们生成时间表，并且足够灵活，可以在未来轻松扩展/修改。

我们不是以“自下而上”的方式解决问题，而是以“自上而下”的方式解决问题，这一开始就令人难以置信。

# 自上而下与自下而上

首先，我对什么是自顶向下和自底向上的定义不同于正确的，[对它是什么的官方定义](https://en.wikipedia.org/wiki/Top-down_and_bottom-up_design)，就计算机科学和编程而言。在这种情况下，我对“自上而下”方法的定义是，首先从一个问题的所有答案开始，然后通过过滤掉所有错误的答案，“向下”找到解决方案集。所以我定义的“自下而上”的方法是相反的；从没有答案开始，然后启发式地建立可能的解决方案，并将它们添加到答案集中。

通常，当试图想出一种算法来解决某个问题时，你会自然而然地想到自底向上的方法，而不是自顶向下的方法。让我们举一个简单的例题:

> 2Sum —给定一个整数列表，找出所有唯一对的列表，这些对的总和将达到一个目标数

输入数字:`[1,0,-1,2,-2]`

目标:`0`

自底向上的方法可能如下所示:

```
# pseudo-python style
#1\. sort the listsorted_nums = sorted(nums) # [-2, -1, 0, 1, 2]#2\. loop through the sorted list with 2 pivots at the ends
# check if sum is == target
# if it is, add to set of answers
# if sum > target, move right_pivot to left
# if sum < target, move left_pivot to rightl_pivot = 0
r_pivot = len(sorted_nums) - 1pairs = set()while l_pivot > r_pivot:
  sum = sorted_nums[l_pivot] + sorted_nums[r_pivot]
  if sum == target:
    answers.add((sorted_nums[l_pivot], sorted_nums[r_pivot]))
    l_pivot += 1
    r_pivot -= 1
  elif sum > target:
    r_pivot -= 1
  else: # sum < target:
    l_pivot += 1#3\. return list of unique pairs
return list(pairs)
```

要注意的关键点是，你从一个空的答案列表开始，并尝试启发式地建立答案列表。

这是我想象的自上而下方法的样子:

```
# 0.5 Generate all possible pairs from list of numbersnums_with_idx = list(enumerate(nums))
all_pairs = []
for idx_1, num_1 in nums_with_idx:
  for idx_2, num_2 in nums_with_idx:
    if idx_1== idx_2: # same num
      continue
    elif num_1 < num_2: # have some ordering for uniqueness
      all_pairs.append((num_1, num_2))
    else: # equal or num_1 > num_2
      all_pairs.append((num_2, num_1))# 1\. scan through all the pairs
# for each pair, if they don't add up to the target
# remove them from the listfor idx, pair in all_pairs:
  if pair[0] + pair[1] != target:
    all_pairs.pop(idx)# 2\. put them in a set, and return list of unique pairs
return list(set(all_pairs))
```

看起来自上而下的方法是浪费且过于昂贵的，因为事实就是如此。通常，当考虑问题的解决方案时，我们希望制定一个可以使用尽可能少的内存/计算的好算法，而“自顶向下”的方法似乎只是一种丑陋的暴力方法。在这种情况下，我们为什么还要为自顶向下的方法烦恼呢？

## 求解器的通用性

自顶向下算法可能比自底向上算法更好的一个原因是求解器/算法的通用性。在许多情况下，自上而下的方法比自下而上的方法更适用于一般情况。

让我们考虑一下，如果要解决的问题不是一个 2Sum 问题，而是一个 XSum 问题，上面两个现有的 2Sum 算法中哪一个会更合适？自底向上的 2Sum 算法需要完全重写，以便它不仅可以处理一对数字，还可以生成一组唯一的答案。自顶向下的 2Sum 算法可以很容易地修改来解决 XSum 问题的情况，只需要首先生成所有可能的组合，然后遍历所有组合数的总和来找到答案(即使它的时间复杂度将呈指数增长并且很快不可行)。

如果正确的解决方案要求您至少查看每个可能的答案至少一次，那么在计算复杂性方面，自顶向下的方法可能与自底向上的方法类似。

## 最优化问题

我能想到的另一个原因是，自顶向下求解器通常是解决大多数优化问题的工具。

优化问题是指从所有可行的解决方案中寻找最佳解决方案的问题。由于自上而下的求解者已经从“整体”解决方案集开始，很容易看出使用自上而下的方法找到“最佳”解决方案并不是什么大问题。

一般来说，任何涉及寻找所有解中的某个最优解的问题都可以定义为优化问题。一些著名的优化问题是背包问题、装箱问题或旅行推销员问题，它们都属于组合优化问题的范畴。

# 为什么要约束编程？

在直接进入什么是约束编程之前，我们需要首先理解它可以帮助解决的问题。约束编程用于帮助解决约束满足问题和约束优化问题。

有几个概念你需要知道，即**变量**，变量的**域**和问题的**约束**。这基本上类似于一个数学问题，其中给你规则和限制，然后要求你求解 x/y/z。例如，假设你试图求解方程`x + y = z`，其中 x ∈ {0，1}，y ∈ {0，1}和 z ∈ {0，1}。

在这个问题中，我们看到 x，y，z 是我们的变量，它们中的每一个都可以取值范围从 0 到 1(定义域)并且它们必须满足`x + y = z`的约束。我们可以看到，只有某一组 x、y 和 z 的可能组合可以满足这些限制/要求:

`x=0,y=0,z=0`是一个答案，
`x=1,y=0,z=1`是另一个
，而`x=1,y=1,z=1`不满足给定的约束
，`x=1,y=1,z=2`遵循约束，但`z=2`不在提供的域中。

约束优化基本上又向前迈了一步，并试图根据某个目标值/函数从所有可行的解决方案(如果有的话)中找到最优组合。例如，我们可以尝试找到使`x`或`x + z`的值最大化的最佳组合，在这种情况下，设置`z=1`的解决方案将比设置`z=0`的解决方案更受青睐。

# Google 的 OR 工具— CP-SAT 求解器

> 运筹学工具—约束编程—统计可行性求解器

在谷歌的 OR-tools 工具集中，有一个[约束编程](https://developers.google.com/optimization/cp)模块，可以帮助解决约束满足和约束优化问题，称为 CP-SAT 求解器。让我们简要回顾一下求解器使用的内部概念。我将使用 Python 版本，但也支持其他语言。

让我们尝试解决上面提到的问题，稍加改变，找到满足`x + 2*y = z`的`x`、`y`和`z`的值，给定它们的域/约束。

```
# import the package
from ortools.sat.python import cp_modelmodel = cp_model.CpModel() # initialize the model
```

首先要知道的是模型本身。我们首先初始化一个空模型，以包含所有的变量、约束和目标，它们将定义我们想要解决的约束问题。

```
# setup the variables# (lower_bound, upper_bound, name)
x = model.NewIntVar(0, 1, 'x')
y = model.NewIntVar(0, 1, 'y')
z = model.NewIntVar(0, 1, 'z')
```

接下来是变量。CP-solver 实际上支持相当多的变量类型，其中一些甚至是特定于问题的类型。在初始化模型中的变量时，变量的域也是在这个阶段指定的。

```
# add the constraintmodel.Add(x + 2*y == z) 
```

在设置了[变量](https://google.github.io/or-tools/python/ortools/sat/python/cp_model.html#cp_model.CpModel.NewBoolVar)之后，我们可以开始向模型添加带有变量的约束。可以添加多个约束条件，以及变量的任意组合。因为 x 是一个 IntVar，所以我们也可以用常量来操作它，在这个例子中是将它乘以 2。还要注意的是，您正在向模型中添加一个看似评估过的语句(对或错)，但实际上不是。在引擎盖下，模型自动地将约束语句分解为“约束”。

```
solver = cp_model.CpSolver()# search for the first solution, first does not mean anything
status = solver.Solve(model) # status is an enumsolver.StatusName(status) # >> 'FEASIBLE', or whatever status it is# optional status checking up logic
if status == cp_model.FEASBILE:
  x_value = solver.Value(x)
  y_value = solver.Value(y)
  z_value = solver.Value(z)
```

在这个阶段，我们已经可以开始从模型中生成解决方案。在这样做之前，我们需要先求解模型。为此，我们首先需要创建一个规划求解对象。接下来，我们用要求解的模型调用求解器的 Solve 方法，它将返回一个状态。这里返回的状态是一个枚举，我们可以通过调用 solver 的 StatusName 或者只是逐个检查它是哪个状态来得到这个漂亮的名字。

为了从这个解中得到 x、y 和 z 的实际值，我们需要使用求解器的值函数。我们只需将需要其值的变量传递给函数，就可以得到值本身。对于我的运行，它返回了`x=0,y=0,z=0`，这是一个正确的解决方案，但不是最有趣的一个。

```
model.Maximize(z) # for minimization, you can use Minimize or -zstatus = solver.solve(model)if status == cp_model.FEASIBLE or status == cp_model.OPTIMAL:
  x_value = solver.Value(x)
  y_value = solver.Value(y)
  z_value = solver.Value(z)
```

在求解模型之前，接下来可以做的事情就是给模型添加一个目标。使用这些变量，我们可以指导模型朝着给出最佳目标值的解决方案进行优化。在这种情况下，我们试图最大化`z`的值，这意味着它将试图给出`z`的值为 1(而不是 0)的解决方案。在我的例子中，我的解决方案中的值是`x=1,y=0,z=1`。

这涵盖了求解器能做什么的非常基本的概念。还有更高级的概念，如[引导约束](https://developers.google.com/optimization/cp/channeling)来帮助形成变量之间的‘关系’，这我还不太熟悉。

# 不足？

我们可以看到约束问题解决方法是多么强大，因为它几乎可以应用于任何其他问题，并且许多问题也可以很容易地通过修改来解决。然而，我遇到了一些限制。

第一个限制是计算非常昂贵，因为随着更多的变量和条件被添加到模型中，计算复杂性似乎呈指数增长。这几乎和试图用蛮力的方式解决问题一样糟糕，尽管求解器使用了非常复杂的算法，使它比看起来更有效，这些算法我并不太熟悉。

这个求解器的另一个特别的限制是，还没有对多目标解决方案的内置支持。有一个[解决方法](https://github.com/google/or-tools/issues/1344)涉及到解决方案提示，虽然对我的用例来说效果很好，但是为了优化不同的目标，需要注意优化目标的顺序等等。

最后，我能找到的大多数指南(包括我拿出的幻灯片)似乎在一点上是一致的，那就是，如果你能用正常的方法有效地解决问题，那么你也许应该用那种方法来代替。在决定将求解器用于您的用例之前，您可能需要权衡从中获得的好处/坏处！

我希望这是有教育意义的，至少在某种程度上帮助了你。这无疑拓宽了我对其他我以前从未听说过的计算范式的接触。

# 参考

其他可用的 CP 求解器:[http://www.constraint.org/en/tools/](http://www.constraint.org/en/tools/)

[https://www . cs . UPC . edu/~ ero dri/网页/CPS/theory/CP/intro/slides . pdf](https://www.cs.upc.edu/~erodri/webpage/cps/theory/cp/intro/slides.pdf)

[](https://www.sciencedirect.com/topics/computer-science/constraint-programming) [## 约束编程

### 约束规划(CP)已被证明是一个非常成功的技术推理指派问题，因为…

www.sciencedirect.com](https://www.sciencedirect.com/topics/computer-science/constraint-programming)