# 掌握使用模式的动态编程

> 原文：<https://levelup.gitconnected.com/master-dynamic-programming-with-patterns-b25f1fb1297c>

![](img/243a590f5a0f1f3067b712182e9565ef.png)

在开始话题之前，我先自我介绍一下。我是一名移动开发者，目前在 IIT BHU 大学学习，业余时间在 medium 上写文章。两年前开始准备面试。那时候我还不会解二和题。对我来说，简单的问题就像困难的问题，所以大多数时候，我不得不看社论和讨论章节。目前，我已经解决了约 800 个问题，并不时参加竞赛。我通常在比赛中解决三个问题，有时会解决四个问题。好了，我们回到正题。

最近我把注意力集中在动态编程上，因为这是面试准备中最难的话题之一。在 DP 中解决了~140 个问题后，我注意到在不同的问题中很少能找到模式。所以我做了一些研究，找到了以下几个话题。我不会给出解决问题的完整方法，但这些模式可能有助于解决 DP。

# 模式

[达到目标的最小(最大)路径](https://leetcode.com/discuss/study-guide/458695/Dynamic-Programming-Patterns#Minimum-(Maximum)-Path-to-Reach-a-Target)
[截然不同的方式](https://leetcode.com/discuss/study-guide/458695/Dynamic-Programming-Patterns#distinct-ways)
[合并区间](https://leetcode.com/discuss/study-guide/458695/Dynamic-Programming-Patterns#Merging-Intervals)
[串上 DP](https://leetcode.com/discuss/study-guide/458695/Dynamic-Programming-Patterns#DP-on-Strings)
[决策制定](https://leetcode.com/discuss/study-guide/458695/Dynamic-Programming-Patterns#Decision-Making)

# 到达目标的最小(最大)路径

问题列表:

[](https://leetcode.com/list/55ac4kuc) [## 最爱- LeetCode

### 提高你的编码技能，迅速找到工作。这是扩展你的知识和做好准备的最好地方…

leetcode.com](https://leetcode.com/list/55ac4kuc) 

为这个模式生成一个问题陈述

# 声明

> *给定一个目标，找到到达该目标的最小(最大)成本/路径/总和。*

# 方法

> *在当前状态之前的所有可能路径中选择最小(最大)路径，然后为当前状态添加值。*

```
routes[i] = min(routes[i-1], routes[i-2], ... , routes[i-k]) + cost[i]
```

为目标中的所有值生成最佳解决方案，并返回目标的值。

# 自上而下

```
for (int j = 0; j < ways.size(); ++j) {
    result = min(result, topDown(target - ways[j]) + cost/ path / sum);
}
return memo[/*state parameters*/] = result;
```

# 自下而上

```
for (int i = 1; i <= target; ++i) {
   for (int j = 0; j < ways.size(); ++j) {
       if (ways[j] <= i) {
           dp[i] = min(dp[i], dp[i - ways[j]] + cost / path / sum) ;
       }
   }
}

return dp[target]
```

# 类似的问题

746。最小爬楼梯成本 `Easy`

# 自上而下

```
int result = min(minCost(n-1, cost, memo), minCost(n-2, cost, memo)) + (n == cost.size() ? 0 : cost[n]);
return memo[n] = result;
```

# 自下而上

```
for (int i = 2; i <= n; ++i) {
   dp[i] = min(dp[i-1], dp[i-2]) + (i == n ? 0 : cost[i]);
}

return dp[n]
```

64。最小路径和 `Medium`

# 自上而下

```
int result = min(pathSum(i+1, j, grid, memo), pathSum(i, j+1, grid, memo)) + grid[i][j];

return memo[i][j] = result;
```

# 自下而上

```
for (int i = 1; i < n; ++i) {
   for (int j = 1; j < m; ++j) {
       grid[i][j] = min(grid[i-1][j], grid[i][j-1]) + grid[i][j];
   }
}

return grid[n-1][m-1]
```

[322。硬币兑换](https://leetcode.com/problems/coin-change/) `Medium`

# 自上而下

```
for (int i = 0; i < coins.size(); ++i) {
    if (coins[i] <= target) { // check validity of a sub-problem
        result = min(ans, CoinChange(target - coins[i], coins) + 1);
    }
}
return memo[target] = result;
```

# 自下而上

```
for (int j = 1; j <= amount; ++j) {
   for (int i = 0; i < coins.size(); ++i) {
       if (coins[i] <= j) {
           dp[j] = min(dp[j], dp[j - coins[i]] + 1);
       }
   }
}
```

[931。最小下落路径和](https://leetcode.com/problems/minimum-falling-path-sum/) `Medium`

[983。最低票价](https://leetcode.com/problems/minimum-cost-for-tickets/) `Medium`

[650。2 键键盘](https://leetcode.com/problems/2-keys-keyboard/)

[279。完美的正方形](https://leetcode.com/problems/perfect-squares/) `Medium`

1049 年。最后一石重量 II

120。三角形 `Medium`

474。1 和 0`Medium`

221。最大平方 `Medium`

[322。硬币兑换](https://leetcode.com/problems/coin-change/) `Medium`

[1240。用最少的正方形平铺一个矩形](https://leetcode.com/problems/tiling-a-rectangle-with-the-fewest-squares/) `Hard`

174。地牢游戏 `Hard`

871。最少加油次数 `Hard`

# 独特的方式

问题列表:

[](https://leetcode.com/list/55ajm50i) [## 最爱- LeetCode

### 提高你的编码技能，迅速找到工作。这是扩展你的知识和做好准备的最好地方…

leetcode.com](https://leetcode.com/list/55ajm50i) 

为此模式生成问题陈述

# 声明

> 给定一个目标，找出一些不同的方法来达到这个目标。

# 方法

> *总结所有可能达到当前状态的方法。*

```
routes[i] = routes[i-1] + routes[i-2], ... , + routes[i-k]
```

为目标中的所有值生成 sum 并返回目标的值。

# 自上而下

```
for (int j = 0; j < ways.size(); ++j) {
    result += topDown(target - ways[j]);
}
return memo[/*state parameters*/] = result;
```

# 自下而上

```
for (int i = 1; i <= target; ++i) {
   for (int j = 0; j < ways.size(); ++j) {
       if (ways[j] <= i) {
           dp[i] += dp[i - ways[j]];
       }
   }
}

return dp[target]
```

# 类似的问题

70。爬楼梯 `Easy`

# 自上而下

```
int result = climbStairs(n-1, memo) + climbStairs(n-2, memo); 

return memo[n] = result;
```

# 自下而上

```
for (int stair = 2; stair <= n; ++stair) {
   for (int step = 1; step <= 2; ++step) {
       dp[stair] += dp[stair-step];   
   }
}
```

62。唯一路径 `Medium`

# 自上而下

```
int result = UniquePaths(x-1, y) + UniquePaths(x, y-1);return memo[x][y] = result;
```

# 自下而上

```
for (int i = 1; i < m; ++i) {
   for (int j = 1; j < n; ++j) {
       dp[i][j] = dp[i][j-1] + dp[i-1][j];
   }
}
```

[1155。目标总数为](https://leetcode.com/problems/number-of-dice-rolls-with-target-sum/) `Medium`的掷骰子数

```
for (int rep = 1; rep <= d; ++rep) {
   vector<int> new_ways(target+1);
   for (int already = 0; already <= target; ++already) {
       for (int pipe = 1; pipe <= f; ++pipe) {
           if (already - pipe >= 0) {
               new_ways[already] += ways[already - pipe];
               new_ways[already] %= mod;
           }
       }
   }
   ways = new_ways;
}
```

**注**

有些问题指出了重复的次数，在这种情况下，增加一个循环来模拟每次重复。

[688。棋盘中的骑士概率](https://leetcode.com/problems/knight-probability-in-chessboard/) `Medium`

494。目标总和`Medium`

377。组合和四 

935。骑士拨号器 `Medium`

1223 年。掷骰子模拟 `Medium`

[416。划分相等子集和](https://leetcode.com/problems/partition-equal-subset-sum/) `Medium`

808。汤菜`Medium`

790。多米诺骨牌和特罗米诺瓷砖和`Medium`

801。使序列增加的最小互换量

673。最长递增子序列数 `Medium`

[63。独特路径二](https://leetcode.com/problems/unique-paths-ii/) `Medium`

[576。超出边界路径](https://leetcode.com/problems/out-of-boundary-paths/) `Medium`

[1269。数个步骤后停留在同一地点的方式](https://leetcode.com/problems/number-of-ways-to-stay-in-the-same-place-after-some-steps/) `Hard`

[1220。数元音排列](https://leetcode.com/problems/count-vowels-permutation/) `Hard`

# 合并间隔

问题列表:

[](https://leetcode.com/list/55aj8s16) [## 最爱- LeetCode

### 提高你的编码技能，迅速找到工作。这是扩展你的知识和做好准备的最好地方…

leetcode.com](https://leetcode.com/list/55aj8s16) 

为此模式生成问题陈述

# 声明

> *给定一组数字，考虑当前数字和你能从左右两边得到的最佳值，找出一个问题的最优解。*

# 方法

> *寻找每个区间的所有最优解，并返回可能的最佳答案。*

```
// from i to j
dp[i][j] = dp[i][k] + result[k] + dp[k+1][j]
```

左右逢源，为当前位置添加一个解决方案。

# 自上而下

```
for (int k = i; k <= j; ++k) {
    result = max(result, topDown(nums, i, k-1) + result[k] + topDown(nums, k+1, j));
}
return memo[/*state parameters*/] = result;
```

# 自下而上

```
for(int l = 1; l<n; l++) {
   for(int i = 0; i<n-l; i++) {
       int j = i+l;
       for(int k = i; k<j; k++) {
           dp[i][j] = max(dp[i][j], dp[i][k] + result[k] + dp[k+1][j]);
       }
   }
}

return dp[0][n-1];for(int l = 1; l<n; l++) {
   for(int i = 0; i<n-l; i++) {
       int j = i+l;
       for(int k = i; k<j; k++) {
           dp[i][j] = max(dp[i][j], dp[i][k] + result[k] + dp[k+1][j]);
       }
   }
}

return dp[0][n-1]
```

# 类似的问题

1130 年。最小代价树从叶值 `Medium`

```
for (int l = 1; l < n; ++l) {
   for (int i = 0; i < n - l; ++i) {
       int j = i + l;
       dp[i][j] = INT_MAX;
       for (int k = i; k < j; ++k) {
           dp[i][j] = min(dp[i][j], dp[i][k] + dp[k+1][j] + maxs[i][k] * maxs[k+1][j]);
       }
   }
}
```

96。独特的二分搜索法树 `Medium`

1039 年。多边形的最小分数三角剖分 `Medium`

546。移除方框和`Medium`

1000。合并宝石的最小成本 `Medium`

[312。爆裂的气球](https://leetcode.com/problems/burst-balloons/) `Hard`

# 自上而下

```
for (int k = i; k <= j; ++k) {
    result = max(result, topDown(nums, i, k-1, memo) + (i-1 >= 0 ? nums[i-1] : 1) * nums[k] * (j+1 < nums.size() ? nums[j+1] : 1) + topDown(nums, k+1, j, memo));
}
return memo[i][j] = result;
```

# 自下而上

```
for(int l = 1; l < n; l++) {
    for(int i = 0; i < n-l; i++) {
        int j = i+l;
        for(int k = i; k <= j; k++) {
            dp[i][j] = max(dp[i][j], (((k>i && k>0) ? dp[i][k-1] : 0) + (i>0 ? nums[i-1] : 1) * nums[k] * (j<n-1 ? nums[j+1] : 1) + ((k<j && k<n-1) ? dp[k+1][j] : 0)));
        }
    }
}
return dp[0][n-1];
```

375。猜数字高还是低 II

# 弦上的 DP

问题列表:

[](https://leetcode.com/list/55afh7m7) [## 最爱- LeetCode

### 提高你的编码技能，迅速找到工作。这是扩展你的知识和做好准备的最好地方…

leetcode.com](https://leetcode.com/list/55afh7m7) 

这种模式的一般问题陈述可能有所不同，但大多数情况下，您会得到两个字符串，这些字符串的长度都不大

# 声明

> *给出两个字符串* `*s1*` *和* `*s2*` *，返回* `*some result*` *。*

# 方法

> 这个模式上的大多数问题需要一个 O(n)复杂度的解决方案。

```
// i - indexing string s1
// j - indexing string s2
for (int i = 1; i <= n; ++i) {
   for (int j = 1; j <= m; ++j) {
       if (s1[i-1] == s2[j-1]) {
           dp[i][j] = /*code*/;
       } else {
           dp[i][j] = /*code*/;
       }
   }
}
```

> *如果给你一个字符串* `*s*` *这个方法可能变化不大*

```
for (int l = 1; l < n; ++l) {
   for (int i = 0; i < n-l; ++i) {
       int j = i + l;
       if (s[i] == s[j]) {
           dp[i][j] = /*code*/;
       } else {
           dp[i][j] = /*code*/;
       }
   }
}
```

1143 年。最长公共子序列 `Medium`

```
for (int i = 1; i <= n; ++i) {
   for (int j = 1; j <= m; ++j) {
       if (text1[i-1] == text2[j-1]) {
           dp[i][j] = dp[i-1][j-1] + 1;
       } else {
           dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
       }
   }
}
```

647。回文子串 `Medium`

```
for (int l = 1; l < n; ++l) {
   for (int i = 0; i < n-l; ++i) {
       int j = i + l;
       if (s[i] == s[j] && dp[i+1][j-1] == j-i-1) {
           dp[i][j] = dp[i+1][j-1] + 2;
       } else {
           dp[i][j] = 0;
       }
   }
}
```

[516。最长回文子序列](https://leetcode.com/problems/longest-palindromic-subsequence/) `Medium`

[1092。最短公共超序列](https://leetcode.com/problems/shortest-common-supersequence/) `Medium`

72。编辑距离 `Hard`

[115。不同的子序列](https://leetcode.com/problems/distinct-subsequences/) `Hard`

712。两个字符串的最小 ASCII 删除和 `Medium`

[5。最长回文子串](https://leetcode.com/problems/longest-palindromic-substring/) `Medium`

# 决策

问题列表:

[](https://leetcode.com/list/55af7bu7) [## 最爱- LeetCode

### 提高你的编码技能，迅速找到工作。这是扩展你的知识和做好准备的最好地方…

leetcode.com](https://leetcode.com/list/55af7bu7) 

这种模式的一般问题陈述是在被宽恕的情况下决定是否使用当前状态。所以，这个问题需要你在当前的状态下做出决定。

# 声明

> *给定一组值，找到一个带有选择或忽略当前值选项的答案。*

# 方法

> *如果您决定选择当前值，则使用忽略该值的先前结果；反之亦然，如果您决定忽略当前值，则使用先前使用值的结果。*

```
// i - indexing a set of values
// j - options to ignore j values
for (int i = 1; i < n; ++i) {
   for (int j = 1; j <= k; ++j) {
       dp[i][j] = max({dp[i][j], dp[i-1][j] + arr[i], dp[i-1][j-1]});
       dp[i][j-1] = max({dp[i][j-1], dp[i-1][j-1] + arr[i], arr[i]});
   }
}
```

[198。入室抢劫犯](https://leetcode.com/problems/house-robber/) `Easy`

```
for (int i = 1; i < n; ++i) {
   dp[i][1] = max(dp[i-1][0] + nums[i], dp[i-1][1]);
   dp[i][0] = dp[i-1][1];
}
```

[121。买卖股票的最佳时机](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) `Easy`

[714。带交易费买卖股票的最佳时机](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/) `Medium`

[309。买卖有冷却期股票的最佳时机](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/) `Medium`

[123。买卖股票的最佳时机三](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/) `Hard`

[188。买卖股票的最佳时机四](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/) `Hard`

我希望这些提示会有所帮助😊

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)