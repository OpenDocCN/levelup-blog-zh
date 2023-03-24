# 苹果公司的面试问题数据库

> 原文：<https://levelup.gitconnected.com/apples-interview-questions-database-784d72f8d061>

以下是苹果公司在采访中提出的 42 个问题，以及他们解决方案的链接。我收到了 Glassdoor 的问题。我在面试前准备了这个数据库，因为我发现它们很有用，所以我想我应该把它作为一个快速资源发布出来。在你预定的面试前两周，试着解决或者至少浏览一下所有这些问题，你就可以开始了。更多关于实习面试的信息，点击这里阅读更多 [**。**](/apples-software-engineering-intern-interview-5b4d250d06a7)

![](img/4196c6933850a7014d2a00b120178eb8.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上[梅达·达伍德](https://unsplash.com/@medhatdawoud?utm_source=medium&utm_medium=referral)拍摄的照片

# 非每日生活津贴:

1.使用 MacOS 的优势

更好的多任务处理功能

更好地优化硬件和软件

简单干净的界面

恶意软件减少

更多信息 [**此处**](https://www.itrelease.com/2019/10/what-are-advantages-and-disadvantages-of-macos/) 。

2.Unix vs Linux —在这里阅读[](https://www.softwaretestinghelp.com/unix-vs-linux/#:~:text=Linux%20refers%20to%20the%20kernel,family%20of%20derived%20operating%20systems.)

**3.你觉得 Siri 怎么样？你能对 Siri 提出什么改进建议？**

**4.你未来 5 年的职业规划是什么？**

**5.当你点击一个网址时会发生什么？[**解**](https://www.freecodecamp.org/news/what-happens-when-you-hit-url-in-your-browser/)**

# **目录系统代理(Directory System Agent)**

# **简单:**

1.  **给定一个整数数组和一个值，确定数组中是否有三个整数的和等于给定值。**

**经过询问边缘案件处理，解决方法应该类似于 [**三 Sum**](https://leetcode.com/problems/3sum/) **。****

**2.给定一个排序后的数组，返回其方块的排序后的数组—此处 **阅读 leetcode [**的讨论部分。**](https://leetcode.com/problems/squares-of-a-sorted-array/discuss/?currentPage=1&orderBy=most_votes&query=)****

**3.判断一个字符串是否是回文。 [**解**](https://www.geeksforgeeks.org/python-program-check-string-palindrome-not/)**

**4.实现具有两个堆栈的队列。 [**解**](https://www.geeksforgeeks.org/queue-using-stacks/)**

**5.按位还原整数。 [**解决方案 1**](https://www.geeksforgeeks.org/reverse-actual-bits-given-number/) **或** [**解决方案 2**](https://leetcode.com/problems/reverse-bits/solution/)**

**6.设计一个 HashSet。 [**解**](https://leetcode.com/problems/design-hashset/discuss/?currentPage=1&orderBy=most_votes&query=)**

**7.设计散列表。 [**解**](https://leetcode.com/problems/design-hashmap/)**

**8.给定一个字符串列表，提供列表中唯一的字符串数量。**

**解决方案——把所有东西都放在一个 hashset 中，然后从中建立一个列表。**

```
ArrayList<String> answer = new ArrayList<>(new HashSet<String>(givenList));
```

**9.求二叉树的深度。 [**解**](https://www.geeksforgeeks.org/write-a-c-program-to-find-the-maximum-depth-or-height-of-a-tree/)**

**10.写一个函数，计算一个给定的整数在链表中出现的次数。 [**解**](https://www.geeksforgeeks.org/write-a-function-that-counts-the-number-of-times-a-given-int-occurs-in-a-linked-list/)**

# **中等**

1.  **实现一个迭代器，除了 hasNext 和 Next 操作之外，还支持对列表的 peek 操作: [**解决方案**](https://leetcode.com/problems/peeking-iterator/discuss/?currentPage=1&orderBy=most_votes&query=)**

**2.复制一个图表。 [**解**](https://leetcode.com/problems/clone-graph/discuss/?currentPage=1&orderBy=most_votes&query=)**

**3.反转一个链表。 [**解**](https://www.geeksforgeeks.org/reverse-a-linked-list/)**

**4.单词搜索，给定一个字符板网格和一个字符串单词，如果该单词存在于网格中，则返回 true。 [**解**](https://leetcode.com/problems/word-search/discuss/?currentPage=1&orderBy=most_votes&query=)**

**5.给定一个返回随机整数 1 或 0 的函数 magicNumber()，编写一个新函数，该函数将使用这个 magicNumber()函数生成一个随机数。**

**解答-询问数字的范围(例如最大为 100)，然后使用 magicNumber()函数为这些数字生成一个位。**

**6.给定两个矩阵，执行矩阵乘法。 [**解**](https://www.geeksforgeeks.org/c-program-multiply-two-matrices/)**

**7.设计一个可以用于 [**最近最少使用(LRU)缓存**](https://en.wikipedia.org/wiki/Cache_replacement_policies#LRU) 的数据结构。 [**解**](https://leetcode.com/problems/lru-cache/discuss/?currentPage=1&orderBy=most_votes&query=)**

**8.检查二叉树的完整性。 [**解**](https://www.geeksforgeeks.org/check-if-a-given-binary-tree-is-complete-tree-or-not/)**

**9.在二叉树中寻找最小公共祖先。 [**解**](https://www.geeksforgeeks.org/lowest-common-ancestor-binary-tree-set-1/)**

**10.整合区间范围。 [**解**](https://leetcode.com/problems/merge-intervals/solution/)**

**11.实现合并排序。 [**解**](https://www.geeksforgeeks.org/merge-sort/)**

**12.交换整数的奇数位和偶数位。 [**解**](https://www.geeksforgeeks.org/swap-all-odd-and-even-bits/)**

**13.最大子阵列和。 [**解**](https://www.geeksforgeeks.org/design-data-structures-algorithms-memory-file-system/)**

**14.拓扑排序。 [**解**](https://www.geeksforgeeks.org/topological-sorting/)**

**15.合并重叠的间隔。 [**解**](http://geeksforgeeks.org/merging-intervals/)**

**16.计算到达楼梯中第 n 级楼梯的方法。[**解决**](https://www.geeksforgeeks.org/count-ways-reach-nth-stair/)**

# **困难的**

1.  **给定一个字符板和一个字符串单词列表，返回板上的所有单词。 [**解**](https://leetcode.com/problems/word-search-ii/discuss/?currentPage=1&orderBy=most_votes&query=)**
2.  **给定三个字符串，如果第三个字符串是其他两个字符串的交错，则返回 true。 [**解**](https://www.geeksforgeeks.org/find-if-a-string-is-interleaved-of-two-other-strings-dp-33/)**

**3.为一个[**【LFU】**](https://en.wikipedia.org/wiki/Least_frequently_used)**缓存设计一个数据结构。 [**解**](https://www.geeksforgeeks.org/least-frequently-used-lfu-cache-implementation/)****

****4.从 LinkedList 中选择一个随机节点。 [**解**](https://www.geeksforgeeks.org/select-a-random-node-from-a-singly-linked-list/)****

****5.实现一个四叉树。 [**解**](https://www.geeksforgeeks.org/quad-tree/)****

****6.从溪流中寻找中间值。 [**解**](https://leetcode.com/problems/find-median-from-data-stream/)****

****7.编写一个代码来添加两个链表，每个链表包含一个数字来表示巨大的数字。 [**解**](https://www.geeksforgeeks.org/sum-of-two-linked-lists/)****

****8.设计文件管理系统。 [**解**](https://www.geeksforgeeks.org/design-data-structures-algorithms-memory-file-system/)****

****9.编写一个程序，使用两个线程打印从 1 到 n 的数字。 [**解法**](https://www.geeksforgeeks.org/print-even-and-odd-numbers-in-increasing-order-using-two-threads-in-java/)****

****10.打印用 x 打开来打开和关闭括号的不同方式。对于 3，这将是:{{{}}}，{}{}{}，{ { } { } { } { }，{}{{}}。 [**解**。](https://www.geeksforgeeks.org/print-all-combinations-of-balanced-parentheses/)****

****11.比合并/快速排序更快地排序 10M 唯一整数的数组。解决方法:既然知道范围，就用 [**基数排序。**](https://www.geeksforgeeks.org/radix-sort/)****

****这就是全部，我会添加更多的，因为我发现他们。我希望这有所帮助。感谢您的阅读，如果您有任何问题，请随时与我联系！****

******编辑:我已经开始提供 1:1 的职业指导，你可以去 anjaliviramgama.com 阅读评论并安排时间******

****安贾利·维拉加马****

****[**LinkedIn**](http://www.linkedin.com/in/anjali-viramgama-085285166)**|**[**insta gram**](https://www.instagram.com/anjali.gama/)****

****您可能感兴趣的文章:****

****[**苹果软件工程实习生面试**](/apples-software-engineering-intern-interview-5b4d250d06a7)****

****[**脸书软件开发商实习生面试**](/nailing-the-facebook-software-developer-intern-interview-5a6dcea630af)****

****[**亚马逊的实习和全职面试指南**](https://towardsdatascience.com/amazons-internship-and-full-time-interview-guide-af3c20455e15)****

****[**彭博的实习和全职指导**](/bloombergs-internship-and-full-time-guide-cde112c9c22d)****

****[**破解编码面试一年计划**](https://towardsdatascience.com/the-one-year-plan-for-competitive-coding-6af53f2f719c)****

# ****分级编码****

****感谢您成为我们社区的一员！[订阅我们的 YouTube 频道](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1)或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。****

****[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev)****