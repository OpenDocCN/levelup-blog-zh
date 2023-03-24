# 我如何创造了一个无与伦比的井字游戏人工智能

> 原文：<https://levelup.gitconnected.com/how-i-created-an-unbeatable-tic-tac-toe-ai-4ac934aaa4b8>

用最小最大算法创造无与伦比的井字游戏人工智能

![](img/752bae96565a6214bc56bcb4c49c0454.png)

凯文·Ku 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

你有没有想过，当你和井字机器人对战时，它们是如何被编程的？嗯，他们可能是用最小最大算法编程的。起初，最小最大算法可能难以理解和实现。但是一旦你很好地理解了它，你甚至可以在双人回合制游戏中实现 AI，比如国际象棋！

我将给出一个最小最大算法的简单描述，但是如果你想更详细地了解它，我将提供几个资源供你查阅。为了更好地理解这个算法，你需要对二叉树如何工作有一个大致的了解。

# 最小-最大算法如何工作

![](img/889392c0942e0069121b863095b9671d.png)

照片由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

首先，我们有最大化玩家和最小化玩家。从人工智能的角度来看，他们将是最大化的玩家。所以最大化玩家会根据一步棋的好坏来寻求最高分，分数越高，这步棋就越好。最小化玩家将代表人类玩家，我们，并选择得分最低的节点。

通过递归，人工智能将尽可能多地移动，并确定每个节点的分数。一旦创建了所有节点，我们就可以比较每个相邻的叶节点，无论哪个更大，我们都将该节点视为最佳移动，而忽略目标更小的节点。

现在，为了让人工智能在最少的时间内选择最好的可能移动，我们需要知道节点有多深，并将其添加到分数中，深度加分数越大，那么这也是最好的可能移动。

当你和这个人工智能对弈时，它总是会选择最好的一步让你处于劣势。在最好的情况下，你将与人工智能打成平手，但我可以保证，如果实现正确，你将无法击败人工智能。

## 最小最大算法的伪代码

伪代码由 [Sebastion 联盟](https://pastebin.com/VSehqDM3)

以下是使用最小最大算法时代码的大致情况。mini-max 函数被编程为基于音符的深度抓取最大值或最小值，一直到我们到达二叉树的头节点，并在程序中返回移动。

对于最小最大算法，我只能说这些。这个算法不仅限于编程一个井字游戏人工智能。这种人工智能的一些其他应用将是国际象棋，跳棋，甚至四连胜！如果你希望进一步改进这个算法，使它更有效，我将提供一些你可以实现 alpha-beta 剪枝的源代码。感谢您花时间阅读我的故事！

# **资源:**

[](https://www.geeksforgeeks.org/minimax-algorithm-in-game-theory-set-1-introduction/?ref=lbp) [## 博弈论中的极大极小算法|第一集(简介)- GeeksforGeeks

### 极大极小法是一种回溯算法，用于决策和博弈论中寻找最优移动…

www.geeksforgeeks.org](https://www.geeksforgeeks.org/minimax-algorithm-in-game-theory-set-1-introduction/?ref=lbp) [](https://www.geeksforgeeks.org/minimax-algorithm-in-game-theory-set-3-tic-tac-toe-ai-finding-optimal-move/?ref=lbp) [## 博弈论中的极大极小算法| Set 3(井字游戏 AI -寻找最优走法)- GeeksforGeeks

### 先决条件:博弈论中的极大极小算法，博弈论中的评价函数让我们结合所学…

www.geeksforgeeks.org](https://www.geeksforgeeks.org/minimax-algorithm-in-game-theory-set-3-tic-tac-toe-ai-finding-optimal-move/?ref=lbp) [](https://www.geeksforgeeks.org/minimax-algorithm-in-game-theory-set-4-alpha-beta-pruning/?ref=lbp) [## 博弈论中的极大极小算法| Set 4 (Alpha-Beta 剪枝)- GeeksforGeeks

### 先决条件:博弈论中的 Minimax 算法，博弈论中的评价函数α-β剪枝实际上不是…

www.geeksforgeeks.org](https://www.geeksforgeeks.org/minimax-algorithm-in-game-theory-set-4-alpha-beta-pruning/?ref=lbp)